---
title: mapo-relay-chain json rpc
description: 
lang: zh
---

为了让软件应用程序与`MAPO-Relay-Chain`区块链交互（通过读取区块链数据或向网络发送交易），它必须连接到`MAPO-Relay-Chain`节点或其提供的[公共网络](docs/base/mapo-relay-chain/public-service.md)。以下`MAPO-Relay-Chain`统称为MAPO.

JSON-RPC（JavaScript Object Notation Remote Procedure Call）是一种轻量级的远程过程调用（RPC）协议，它使用 JSON 作为数据交换格式。JSON-RPC 允许在客户端和服务器之间进行跨网络通信，以调用远程服务或方法，类似于传统的远程过程调用（RPC）协议，但使用 JSON 格式进行数据传输。

JSON-RPC 的主要特点包括：

+ 轻量级: JSON-RPC 使用 JSON 格式，这是一种轻量级的数据交换格式，易于解析和生成。

+ 语言无关性: JSON-RPC 不依赖于特定的编程语言或平台，可以在不同编程语言之间进行通信。

+ HTTP 或其它协议: JSON-RPC 可以在多种传输协议上运行，最常见的是使用 HTTP 协议进行通信，但也可以在其它协议上使用，如 WebSocket。

+ 简单性: JSON-RPC 的协议规范相对简单，易于实现。

JSON-RPC 请求通常包含以下部分：

+ 方法名（Method）: 要调用的远程方法或函数的名称。
+ 参数（Params）: 传递给远程方法的参数，通常是一个数组或对象。
+ ID（Identifier）: 一个唯一标识符，用于标识请求和匹配响应。通常是一个整数或字符串。
+ JSON-RPC 响应包含以下部分：

+ 结果（Result）: 远程方法的执行结果，通常是一个数组或对象。
+ 错误（Error）: 如果发生错误，包含错误信息的对象。
+ ID（Identifier）: 与请求中的 ID 对应，用于将响应与请求匹配。

JSON-RPC 被广泛用于构建分布式系统、Web 服务、区块链节点通信等场景，因为它的轻量级和语言无关性使得不同平台和语言的应用能够相互通信。一些知名的区块链平台，如 Ethereum，也使用 JSON-RPC 作为与客户端应用程序进行通信的方式。

## atlas rpc

MAPO提供了兼容以太坊API的json rpc,具体API请参考[这里](/docs/sdk/mapo-relay-chain/json-rpc/atlas-json-rpc.md)

## atlas consensus rpc

MAPO还提供了一组共识相关的API,具体API请参考[这里](/docs/sdk/mapo-relay-chain/json-rpc/atlas-consensus-rpc.md)




