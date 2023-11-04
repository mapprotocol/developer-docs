# Component Layer of Full-Stack Development

## How does it work

MOS messages allow projects built on one chain to easily synchronize some project information to other chains, and also call contract methods on other connected chains.

MOS uses the MAP Protocol light client to verify cross-chain message transactions, ensuring cross-chain messages are verifiable on-chain.

With MOS, you can achieve interoperability with two chains:

- Call contracts on Chain B from Chain A.

- Package message changes on Chain A and write them to Chain B to synchronize messages.

## Prerequisites


- The application must be on one of the chains supported by the MAP Protocol. See [lightClient](https://docs.mapprotocol.io/develop/light-client) for a list of chains that can deploy the MAPO light client. This list is updated as new chains are added. Cross-chain message executable contract permissions must be granted to the MOS contract on the corresponding chain.

- Both Chain A and Chain B must deploy the MOS message contract (MOS messages for Near Chain are still under development).

## How MOS completes cross-chain messages

### On the source chain
1. The user (dApp) organizes the messages to be cross-chained and organizes the callData for calling the target chain.

2. The dApp calls the MOS transferOut method and pays the gas fee for cross-chaining

3. MOS sends the cross-chain transaction and emits a cross-chain message log. You can view transaction details in the source chain browser.

### On the MAP relay chain

1. The messenger detects the message log on the source chain, builds proof data from the source chain, and calls the method transferIn to notify the MOS contract on the relay chain.

2. The MOS relay contract (MOS contract on the MAP relay chain) confirms the message log on the source chain, verifies the authenticity of the source chain transaction through the light client, determines that it is going to another chain, sends a transaction, and continues to emit a cross-chain message log.

3. If the MAP relay chain is the destination chain, execute the call method and emit an execution log.

### On the destination chain

1. The messenger detects the message log on the MAP relay chain, builds proof data from the relay chain, and calls the transferIn method to notify the MOS contract on the destination chain.

2. The MOS contract verifies the authenticity of the MAPO message log through the light client.

3. The destination chain emits an execution log to complete the cross-chain contract call message.

## Process Architecture

![messageFlow](./crossChainMessage.png)