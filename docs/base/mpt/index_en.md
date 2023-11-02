---
title: MPT(Merkle Patricia Tree)
description: Merkle Patricia Tree Introduction
lang: en
sidebarDepth: 2
---

A Merkle Patricia Trie is a cryptographically authenticated data structure used to store all `(key, value)` pairs.

The Merkle Patricia Trie is fully deterministic, meaning that dictionaries with the same `(key, value)` pairs will always be exactly the same, even down to the last byte. This means they share the same root hash, making insertions, lookups, and deletions extremely efficient at `O(log(n))` complexity. Furthermore, compared to more complex, comparison-based dictionaries like red-black trees, Merkle Patricia Tries are easier to understand and code.

## prerequisites

To better understand this document, having the following foundational knowledge will be helpful:[Hashing](https://en.wikipedia.org/wiki/Hash_function)、[Merkle Tree](https://en.wikipedia.org/wiki/Merkle_tree)、[Trie (Prefix Tree)](https://en.wikipedia.org/wiki/Trie)和[Serialization](https://en.wikipedia.org/wiki/Serialization)。

## merkle-patricia-trees

A radix tree has a significant limitation: inefficiency. If you want to store a `(path, value)` pair at a position with a path length of 64 characters `(half-bytes32)`, you would need over a thousand bytes of additional space to store each character at a level, and each query or deletion would require executing a complete 64 steps. The Patricia dictionary tree described below can address this problem.

### optimization

The nodes in a Merkel Patricia Trie can be any of the following types:

1.  `NULL`（represents an empty string）
2.  `branch`，a 17-element node ` [ v0 ... v15, vt ]`
3.  `leaf`，a two-element node `[ encodedPath, value ]`
4.  `extension`，a two-element node `[ encodedPath, key ]`

When traversing the prefix tree in a 64-character path, after traversing the initial layers, you will reach a node where there are no more branching paths downstream, or at least not many. To avoid creating up to 15 sparse `NULL` nodes in the path, we streamline the traversal down by setting an `extension` node in the form `[encodedPath, key]`. The `encodedPath` contains the "partial path" to skip (using the compressed encoding described below), and the `key` is used for the next database query.

For `leaf` nodes, you can use a flag in the first half-byte of the `encodedPath` to mark that the path encodes all path segments of previous nodes, and you can query the `value` directly.

However, this optimization introduces ambiguity. When traversing the path by half-bytes, you may end up having to traverse an odd number of half-bytes, but all data must be stored in `bytes` format. There is no distinction between them, for example, a half-byte `1` and a half-byte `01` (both must be stored as `<01>`). To specify the length of an odd number of half-bytes, this part of the path should use a flag as a prefix.

### specification：Compressed encoding of hexadecimal sequences with optional terminators

As described above, the flag for the remaining part of the path length being odd vs. even and whether it's a leaf node vs. an extension node is found in the first nibble (half-byte) of the partial path in any two-element node, resulting in the following:

    hex char    bits    |    node type partial     path length
    ----------------------------------------------------------
       0        0000    |       extension              even
       1        0001    |       extension              odd
       2        0010    |   terminating (leaf)         even
       3        0011    |   terminating (leaf)         odd

For the case where the remaining path length is even (`0` or `2`), a `0` "placeholder" nibble is always appended.

```
    def compact_encode(hexarray):
        term = 1 if hexarray[-1] == 16 else 0
        if term: hexarray = hexarray[:-1]
        oddlen = len(hexarray) % 2
        flags = 2 * term + oddlen
        if oddlen:
            hexarray = [flags] + hexarray
        else:
            hexarray = [flags] + [0] + hexarray
        // hexarray now has an even length whose first nibble is the flags.
        o = ''
        for i in range(0,len(hexarray),2):
            o += chr(16 * hexarray[i] + hexarray[i+1])
        return o
```

example：

```
    > [ 1, 2, 3, 4, 5, ...]
    '11 23 45'
    > [ 0, 1, 2, 3, 4, 5, ...]
    '00 01 23 45'
    > [ 0, f, 1, c, b, 8, 10]
    '20 0f 1c b8'
    > [ f, 1, c, b, 8, 10]
    '3f 1c b8'
```

Here is the code to retrieve nodes in a Merkle Patricia Tree:

```
    def get_helper(node,path):
        if path == []: return node
        if node = '': return ''
        curnode = rlp.decode(node if len(node) < 32 else db.get(node))
        if len(curnode) == 2:
            (k2, v2) = curnode
            k2 = compact_decode(k2)
            if k2 == path[:len(k2)]:
                return get(v2, path[len(k2):])
            else:
                return ''
        elif len(curnode) == 17:
            return get_helper(curnode[path[0]],path[1:])

    def get(node,path):
        path2 = []
        for i in range(len(path)):
            path2.push(int(ord(path[i]) / 16))
            path2.push(ord(path[i]) % 16)
        path2.push(16)
        return get_helper(node,path2)
```

## tries-in-MAPO

All the Merkle trees in `MAPO`'s execution layer use the same Merkle Patricia Trie as Ethereum.

In the block header, there are three roots from three prefix trees.

1.  stateRoot
2.  transactionsRoot
3.  receiptsRoot

### state-trie

There is a global state dictionary tree that gets updated each time a client processes a block. In this tree, the `path` is always: `keccak256(ethereumAddress)`, and the `value` is always: `rlp(ethereumAccount)`. More specifically, a MAPO `account` is an array containing four elements: `[nonce, balance, storageRoot, codeHash]`. It's worth noting that the `storageRoot` is the root of another Patricia Trie dictionary tree.

### storage-trie

The storage tree is where `all contract data`is stored. Each account has its own separate storage tree. To retrieve a value at a specific storage position with a given address, you'll need the address, the integer position in storage where the data is stored, and the block ID. Afterward, you can use these parameters in the JSON-RPC application interface's `eth_getStorageAt` function, for example, to retrieve data in storage slot 0 of address `0x295a70b2de5e3953354a6a8344e616ed314d7251`:

```
curl -X POST --data '{"jsonrpc":"2.0", "method": "eth_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x0", "latest"], "id": 1}' localhost:8545

{"jsonrpc":"2.0","id":1,"result":"0x00000000000000000000000000000000000000000000000000000000000004d2"}

```
To retrieve other elements from storage, you first need to calculate the position in the storage tree. This position is calculated as the `keccak256` hash of the address and the storage location, both left-padded with zeros to a length of 32 bytes. For example, the position of the data for address `0x391694e7e0b0cce554cb130d723a9d27458f9298` at storage slot 1 is:

```
keccak256(decodeHex("000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"))
```

You can calculate it in the console as follows:：

```
> var key = "000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"
undefined
> web3.sha3(key, {"encoding": "hex"})
"0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9"
```

so，`path` is `keccak256(<6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9>)`。 As before, this address can now be used to retrieve data from the storage tree.：

```
curl -X POST --data '{"jsonrpc":"2.0", "method": "eth_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9", "latest"], "id": 1}' localhost:8545

{"jsonrpc":"2.0","id":1,"result":"0x000000000000000000000000000000000000000000000000000000000000162e"}
```

### transaction-trie

每个区块都有一个独立的交易字典树，也用于存储 `(key, value)` 对。 路径为：`rlp(transactionIndex)`，代表了对应一个值的键，值由以下决定：

Each block has a separate transaction trie, which is also used to store `(key, value)` pairs. The path is formed by using `rlp(transactionIndex)`, representing the key corresponding to a value. The value is determined by the following:

```
if legacyTx:
  value = rlp(tx)
else:
  value = TxType | encode(tx)
```


### receipts-trie

Each block has its own receipt trie. Here, the `path` is `rlp(transactionIndex)`, where the `transactionIndex` is its index within the mined block. The receipt trie is never updated. Similar to the transaction trie, it contains both current and previous receipts. To query a specific receipt in the receipt trie, you need to provide the index of the transaction within the block, the receipt payload, and the transaction type. The returned receipt can be of the `Receipt type`, defined as a concatenation of `transaction type` and `transaction payload`, or it can be of the LegacyReceipt type, defined as `rlp([status, cumulativeGasUsed, logsBloom, logs])`.