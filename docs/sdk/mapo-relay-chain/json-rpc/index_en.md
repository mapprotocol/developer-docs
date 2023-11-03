---
title: mapo-relay-chain json rpc
description: 
lang: en
---

In order for a software application to interact with the `MAPO-Relay-Chain` blockchain (by reading blockchain data or sending transactions to the network), it must connect to `MAPO-Relay-Chain` nodes or its provided [public network](/docs/base/mapo-relay-chain/public-service_en.md).

The following `MAPO-Relay-Chain` is collectively referred to as MAPO.

JSON-RPC (JavaScript Object Notation Remote Procedure Call) is a lightweight remote procedure call (RPC) protocol that utilizes JSON as the data exchange format. JSON-RPC enables cross-network communication between clients and servers for invoking remote services or methods, similar to traditional RPC protocols, but with data transmission in JSON format.


The key features of JSON-RPC include:

+ Lightweight: JSON-RPC uses JSON, a lightweight data exchange format that is easy to parse and generate.

+ Language-agnostic: JSON-RPC is not tied to a specific programming language or platform, enabling communication between different programming languages.

+ Transport Protocol Flexibility: JSON-RPC can operate over various transport protocols, with the most common being HTTP for communication but also capable of using other protocols such as WebSocket.

+ Simplicity: The JSON-RPC protocol specification is relatively simple and straightforward to implement.

JSON-RPC requests typically consist of the following parts:

+ Method Name (Method): The name of the remote method or function to be invoked.
+ Parameters (Params): The parameters passed to the remote method, often an array or an object.
+ ID (Identifier): A unique identifier used to associate requests and match responses, usually an integer or a string.
+ JSON-RPC responses contain the following components:

+ Result (Result): The result of the remote method's execution, often an array or an object.
+ Error (Error): If an error occurs, this part contains an object with error information.
+ ID (Identifier): Corresponding to the ID in the request, used to match responses with requests.

JSON-RPC is widely used in building distributed systems, web services, blockchain node communication, and other scenarios due to its lightweight and language-agnostic nature, allowing applications in different platforms and languages to communicate with each other. Some well-known blockchain platforms, such as Ethereum, also use JSON-RPC as a means of communication with client applications.

## atlas rpc

MAPO provides a JSON-RPC API that is compatible with Ethereum's API. For specific details about this API, please refer [this](/docs/sdk/mapo-relay-chain/json-rpc/atlas-json-rpc.md)

## atlas consensus rpc

MAPO also provides a set of consensus-related APIs. For specific details about these APIs, please refer [this](/docs/sdk/mapo-relay-chain/json-rpc/atlas-consensus-rpc.md)



