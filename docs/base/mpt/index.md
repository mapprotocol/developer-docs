---
title: MPT(Merkle Patricia Tree)
description: 默克尔帕特里夏树简介。
lang: zh
sidebarDepth: 2
---

默克尔帕特里字典树夏树提供了一种经过加密认证的数据结构，可用于存储所有 `(key, value)` 对。

默克尔帕特里夏树是完全确定性的，这意味着有相同 `(key, value)` 对的字典树肯定是完全相同的，就连最后一个字节也相同。 这代表它们有着相同的根哈希，让插入、查找和删除操作具有难以企及的 `O(log(n))` 效率。 此外，相较于更复杂的基于比较的其他字典树（如红黑树），默克尔帕特里夏树更易于理解和编码。

## 前提条件 {#prerequisites}

为了更好地理解本文，具备以下基础知识将有所帮助：[哈希](https://en.wikipedia.org/wiki/Hash_function)、[默克尔树](https://en.wikipedia.org/wiki/Merkle_tree)、[字典树](https://en.wikipedia.org/wiki/Trie)和[序列化](https://en.wikipedia.org/wiki/Serialization)。

## 默克尔帕特里夏树 {#merkle-patricia-trees}

基数树有一个主要限制：效率低下。 如果你想将一个 `(path, value)` 对存储在路径长度为 64 个字符（`bytes32` 中的半字节数）的位置，我们需要超过一千字节的额外空间将每个字符存储一个层级，并且每次查询或删除都需要执行完整的 64 个步骤。 下文介绍的帕特里夏字典树可以解决这个问题。

### 优化 {#optimization}

默克尔帕特里夏树的节点可以是以下任意一种：

1.  `NULL`（表示为空字符串）
2.  `branch`，一个 17 元素节点 ` [ v0 ... v15, vt ]`
3.  `leaf`，一个双元素节点 `[ encodedPath, value ]`
4.  `extension`，一个双元素节点 `[ encodedPath, key ]`

在 64 个字符的路径中，遍历前缀树的前几层后，一定会到达这样的节点：至少部分下游再无分支路径。 为了避免在路径中创建多达 15 个稀疏 `NULL` 节点，我们通过设置一个形式为 `[ encodedPath, key ]` 的 `extension` 节点来精简向下遍历，其中 `encodedPath` 包含要跳过的“部分路径”（使用下面描述的压缩编码），`key` 用于下一次数据库查询。

对于 `leaf` 节点，可以使用 `encodedPath` 的第一个半字节中的标志来标记，其路径编码了所有先前节点的路径片段，并且我们可以直接查询 `value`。

然而，上述优化造成了模棱两可。

当以半字节遍历路径时，最后我们可能需要遍历奇数个半字节，但是所有数据都需要以 `bytes` 格式存储。 两者之间是无法区分的，例如，半字节 `1` 和半字节 `01`（两者都必须存储为 `<01>`）。 要指定奇数个半字节的长度，这部分路径要使用标记位作为前缀。

### 规范：带有可选终止符的十六进制序列的压缩编码 {#specification}

如上文所述，*剩余部分路径长度为奇数 vs 偶数*和*叶节点 vs 扩展节点*的标记位位于任意双元素节点中部分路径的第一个半字节。 从而产生以下结果：

    hex char    bits    |    node type partial     path length
    ----------------------------------------------------------
       0        0000    |       extension              even
       1        0001    |       extension              odd
       2        0010    |   terminating (leaf)         even
       3        0011    |   terminating (leaf)         odd

对于剩余路径长度为偶数的情况（`0` 或 `2`），一定会附加一个 `0`“占位”半字节。

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

示例：

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

以下为获取默克尔帕特里夏树中节点的扩展代码：

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

## MAPO中的前缀树 {#tries-in-ethereum}

MAPO的执行层中的所有默克尔树均使用跟以太坊一样的默克尔帕特里夏树。

在区块头，有来自 3 棵前缀树的 3 个根。

1.  stateRoot
2.  transactionsRoot
3.  receiptsRoot

### 状态树 {#state-trie}

有一个全局状态字典树，每次客户端处理一个区块时它都会更新。 其中，`path` 始终为：`keccak256(ethereumAddress)`，`value` 始终为：`rlp(ethereumAccount)`。 更具体地说，一个MAPO `account` 是包含 4 个元素的数组：`[nonce,balance,storageRoot,codeHash]`。 关于这一点值得注意的是，`storageRoot` 是另一个帕特里夏字典树的根：

### 存储树 {#storage-trie}

存储树是*所有*合同数据存放之处。 每个帐户都有一棵单独的存储树。 要用给定地址在特定的存储位置检索值，需要存储地址、存储器中存储数据的整数位置，以及区块 ID。 之后，这些数据可以作为参数传入 JSON-RPC 应用程序接口中定义的 `eth_getStorageAt`，例如用于检索地址 `0x295a70b2de5e3953354a6a8344e616ed314d7251` 的存储插槽 0 中的数据：

```
curl -X POST --data '{"jsonrpc":"2.0", "method": "eth_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x0", "latest"], "id": 1}' localhost:8545

{"jsonrpc":"2.0","id":1,"result":"0x00000000000000000000000000000000000000000000000000000000000004d2"}

```

更多的是检索存储器中的其他元素，因为必须首先计算存储树中的位置。 该位置作为地址和存储位置的 `keccak256` 哈希值进行计算，两者都从左侧开始用零填充 32 个字节的长度。 例如，地址 `0x391694e7e0b0cce554cb130d723a9d27458f9298` 的数据在存储时隙 1 中的位置是：

```
keccak256(decodeHex("000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"))
```

在控制台中，可以按以下方式计算：

```
> var key = "000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"
undefined
> web3.sha3(key, {"encoding": "hex"})
"0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9"
```

因此，`path` 是 `keccak256(<6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9>)`。 与以前相同，此地址现在可用于从存储树检索数据：

```
curl -X POST --data '{"jsonrpc":"2.0", "method": "eth_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9", "latest"], "id": 1}' localhost:8545

{"jsonrpc":"2.0","id":1,"result":"0x000000000000000000000000000000000000000000000000000000000000162e"}
```

### 交易树 {#transaction-trie}

每个区块都有一个独立的交易字典树，也用于存储 `(key, value)` 对。 路径为：`rlp(transactionIndex)`，代表了对应一个值的键，值由以下决定：

```
if legacyTx:
  value = rlp(tx)
else:
  value = TxType | encode(tx)
```


### 收据树 {#receipts-trie}

每个区块都有自己的收据树。 此处的 `path` 是：`rlp(transactionIndex)`。 `transactionIndex` 是它在挖矿区块中的索引。 收据字典树从不更新。 与交易字典树类似，它也有当前和以前的收据。 为了在收据字典树中查询特定的收据，需要提供区块中交易的索引、收据有效载荷以及交易类型。 返回的收据可以是 `Receipt` 类型，定义为 `transaction type` 和 `transaction payload` 的串接，也可以是 `LegacyReceipt` 类型，定义为 `rlp([status, cumulativeGasUsed, logsBloom, logs])`。
