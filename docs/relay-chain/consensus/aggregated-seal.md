# Aggregated Seal

This document describes the generation process of the `AggregatedSeal` field in the block header's `extraData` field.

## MAP Block Header Structure

The MAP block header contains the following fields:

```go
// Header represents a block header in the Ethereum blockchain.
type Header struct {
	ParentHash  common.Hash    `json:"parentHash"       gencodec:"required"`
	Coinbase    common.Address `json:"miner"            gencodec:"required"`
	Root        common.Hash    `json:"stateRoot"        gencodec:"required"`
	TxHash      common.Hash    `json:"transactionsRoot" gencodec:"required"`
	ReceiptHash common.Hash    `json:"receiptsRoot"     gencodec:"required"`
	Bloom       Bloom          `json:"logsBloom"        gencodec:"required"`
	Number      *big.Int       `json:"number"           gencodec:"required"`
	GasLimit    uint64         `json:"gasLimit"         gencodec:"required"`
	GasUsed     uint64         `json:"gasUsed"          gencodec:"required"`
	Time        uint64         `json:"timestamp"        gencodec:"required"`
	Extra       []byte         `json:"extraData"        gencodec:"required"`
	MixDigest   common.Hash    `json:"mixHash"`
	Nonce       BlockNonce     `json:"nonce"`

	// BaseFee was added by EIP-1559 and is ignored in legacy headers.
	BaseFee *big.Int `json:"baseFeePerGas" rlp:"optional"`
}
```

The `extraData` field in the block header is the result of RLP encoding. The structure before encoding is as follows:

```go
type IstanbulExtra struct {
	// AddedValidators are the validators that have been added in the block
	AddedValidators []common.Address
	// AddedValidatorsPublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsPublicKeys []blscrypto.SerializedPublicKey
	// AddedValidatorsG1PublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsG1PublicKeys []blscrypto.SerializedG1PublicKey
	// RemovedValidators is a bitmap having an active bit for each removed validator in the block
	RemovedValidators *big.Int
	// Seal is an ECDSA signature by the proposer
	Seal []byte
	// AggregatedSeal contains the aggregated BLS signature created via IBFT consensus.
	AggregatedSeal IstanbulAggregatedSeal
	// ParentAggregatedSeal contains and aggregated BLS signature for the previous block.
	ParentAggregatedSeal IstanbulAggregatedSeal
}

type IstanbulAggregatedSeal struct {
	// Bitmap is a bitmap having an active bit for each validator that signed this block
	Bitmap *big.Int
	// Signature is an aggregated BLS signature resulting from signatures by each validator that signed this block
	Signature []byte
	// Round is the round in which the signature was created.
	Round *big.Int
}
```

To obtain the structured information of `extraData`, you only need to RLP decode the bytes after the first 32 bytes of extraData. The specific decoding process can be found in the `ExtractIstanbulExtra` function:

```go
// ExtractIstanbulExtra extracts all values of the IstanbulExtra from the header. It returns an
// error if the length of the given extra-data is less than 32 bytes or the extra-data can not
// be decoded.
func ExtractIstanbulExtra(h *Header) (*IstanbulExtra, error) {
	
    ......

	var istanbulExtra *IstanbulExtra
    // IstanbulExtraVanity = 32
	err := rlp.DecodeBytes(h.Extra[IstanbulExtraVanity:], &istanbulExtra)
	if err != nil {
		return nil, err
	}
	return istanbulExtra, nil
}
```

## Calculating Block Header Hash

Calculating the hash of a block header requires all fields in the block header. However, special attention must be paid to extraData, as its length affects the hash calculation method.

### When `extraData` length is less than 32 bytes

The block header hash is simply the keccak256 hash of the RLP-encoded block header.

### When `extraData` length is 32 bytes or more

Before calculating the hash, we first need to decode extraData and then set the AggregatedSeal field to empty. This specific operation can be found in the `IstanbulFilteredHeader` function below. After that, we perform a keccak256 hash on the RLP-encoded block header to obtain the block header hash.

```go
// Hash returns the block hash of the header, which is simply the keccak256 hash of its
// RLP encoding.
func (h *Header) Hash() common.Hash {
	// Seal is reserved in extra-data. To prove block is signed by the proposer.
    // IstanbulExtraVanity = 32
	if len(h.Extra) >= IstanbulExtraVanity {
		if istanbulHeader := IstanbulFilteredHeader(h, true); istanbulHeader != nil {
			return rlpHash(istanbulHeader)
		}
	}
    // which is simply the keccak256 hash of its RLP encoding.
	return rlpHash(h)
}

// IstanbulFilteredHeader returns a filtered header which some information (like seal, aggregated signature)
// are clean to fulfill the Istanbul hash rules. It returns nil if the extra-data cannot be
// decoded/encoded by rlp.
func IstanbulFilteredHeader(h *Header, keepSeal bool) *Header {
	newHeader := CopyHeader(h)
	istanbulExtra, err := ExtractIstanbulExtra(newHeader)
	if err != nil {
		return nil
	}

	if !keepSeal {
		istanbulExtra.Seal = []byte{}
	}
	istanbulExtra.AggregatedSeal = IstanbulAggregatedSeal{}

	payload, err := rlp.EncodeToBytes(&istanbulExtra)
	if err != nil {
		return nil
	}

    // IstanbulExtraVanity = 32
	newHeader.Extra = append(newHeader.Extra[:IstanbulExtraVanity], payload...)

	return newHeader
}
```

## Validator Broadcasting Commit Message

Validator nodes broadcast commit messages that carry `CommittedSeal`. The `Signature` field in `AggregatedSeal` is the result of aggregating all the `CommittedSeal` signatures from the collected commit messages.

```go
func (c *core) broadcastCommit(sub *istanbul.Subject) {
	
	......

	committedSeal, err := c.generateCommittedSeal(sub)
	
	......
	
	istMsg := istanbul.NewCommitMessage(&istanbul.CommittedSubject{
		Subject:               sub,
		CommittedSeal:         committedSeal[:],
		EpochValidatorSetSeal: epochValidatorSetSeal[:],
	}, c.address)
	c.broadcast(istMsg)
}
```

## Generating Committed Seal

- `sub.Digest`: The hash of the block
- `sub.View.Round`: The round number

```go
func (c *core) generateCommittedSeal(sub *istanbul.Subject) (blscrypto.SerializedSignature, error) {
	seal := PrepareCommittedSeal(sub.Digest, sub.View.Round)
	
	committedSeal, err := c.backend.SignBLS(seal, []byte{}, false, false)
	if err != nil {
		return blscrypto.SerializedSignature{}, err
	}
	return committedSeal, nil
}
```

Convert `hash`, `round`, `MsgCommit` into bytes and concatenate to get a simple seal. `MsgCommit` is a constant with value 2.

```go
func PrepareCommittedSeal(hash common.Hash, round *big.Int) []byte {
	var buf bytes.Buffer
	buf.Write(hash.Bytes())
	buf.Write(round.Bytes())
	buf.Write([]byte{byte(istanbul.MsgCommit)})
	return buf.Bytes()
}
```

By concatenating the block header's `hash`, `round`, and a fixed `MsgCommit`, we get a simple seal. However, this is not enough because anyone can generate this seal. We also need to BLS sign the seal with the validator's private key to get the final `committedSeal`.

```go
type Account struct {
	Address common.Address `json:"address"` // Ethereum account address derived from the key
	URL     URL            `json:"url"`     // Optional resource locator within a backend
}

func (ks *KeyStore) SignBLS(a accounts.Account, msg []byte, extraData []byte, useComposite, cip22 bool) (blscrypto.SerializedSignature, error) {
	// Look up the key to sign with and abort if it cannot be found
	ks.mu.RLock()
	defer ks.mu.RUnlock()

    // a.Address is the address of validator
    // when starting the validator node, you need to unlock the validator account through the `--unlock` flag. When unlocking,
    // the account's private key and other information will be put into `unlocked`
	unlockedKey, found := ks.unlocked[a.Address]
	if !found {
		return blscrypto.SerializedSignature{}, ErrLocked
	}

    // The private key corresponding to the validator account
	privateKeyBytes, err := blscrypto.CryptoType().ECDSAToBLS(unlockedKey.PrivateKey)
	if err != nil {
		return blscrypto.SerializedSignature{}, err
	}

	blskey, err := bn256.DeserializePrivateKey(privateKeyBytes)
	if err != nil {
		return blscrypto.SerializedSignature{}, err
	}

	sign, err := bn256.Sign(blskey, blskey.ToPublic(), msg)
	if err != nil {
		return blscrypto.SerializedSignature{}, err
	}
	signature := blscrypto.SerializedSignature{}
	copy(signature[:], sign.Marshal())
	return signature, nil
}
```

## Creating Aggregated Seal

Put all the CommittedSeals from collected messages into a two-dimensional array. The first dimension stores the index of messages in the message list, and the second dimension stores the actual CommittedSeal. Then perform BLS aggregate signature on this two-dimensional array.

```go
func GetAggregatedSeal(seals MessageSet, round *big.Int) (types.IstanbulAggregatedSeal, error) {
	bitmap := big.NewInt(0)
	committedSeals := make([][]byte, seals.Size())
    // Get all collected messages
	for i, v := range seals.Values() {
        // IstanbulExtraBlsSignature = 64
		committedSeals[i] = make([]byte, types.IstanbulExtraBlsSignature)
        // get the `committedSubject` in each message
		commit := v.Commit()
        // the commit.CommittedSeal here is the CommittedSeal in the message broadcast by the validator
		copy(committedSeals[i][:], commit.CommittedSeal[:])

		j, err := seals.GetAddressIndex(v.Address)
		if err != nil {
			return types.IstanbulAggregatedSeal{}, err
		}
		bitmap.SetBit(bitmap, int(j), 1)
	}

	asig, err := blscrypto.CryptoType().AggregateSignatures(committedSeals)
	if err != nil {
		return types.IstanbulAggregatedSeal{}, err
	}
	return types.IstanbulAggregatedSeal{Bitmap: bitmap, Signature: asig, Round: round}, nil
}
```

## Appending Signature to Block Header's extraData Field

```go
// writeAggregatedSeal writes the extra-data field of a block header with given committed
// seals. If isParent is set to true, then it will write to the fields related
// to the parent commits of the block
func writeAggregatedSeal(h *types.Header, aggregatedSeal types.IstanbulAggregatedSeal, isParent bool) error {
	// IstanbulExtraBlsSignature = 64
    if len(aggregatedSeal.Signature) != types.IstanbulExtraBlsSignature {
		return errInvalidAggregatedSeal
	}

	istanbulExtra, err := types.ExtractIstanbulExtra(h)
	if err != nil {
		return err
	}

	if isParent {
		istanbulExtra.ParentAggregatedSeal = aggregatedSeal
	} else {
		istanbulExtra.AggregatedSeal = aggregatedSeal
	}

	payload, err := rlp.EncodeToBytes(&istanbulExtra)
	if err != nil {
		return err
	}

	// compensate the lack bytes if header.Extra is not enough IstanbulExtraVanity bytes.
    // IstanbulExtraVanity = 32
	if len(h.Extra) < types.IstanbulExtraVanity {
		h.Extra = append(h.Extra, bytes.Repeat([]byte{0x00}, types.IstanbulExtraVanity-len(h.Extra))...)
	}

	h.Extra = append(h.Extra[:types.IstanbulExtraVanity], payload...)
	return nil
}
```
