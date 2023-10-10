运行你自己的节点为你提供各种好处，打开新的可能性，并为支持生态系统提供帮助。 这个页面将引导你启动自己的节点，并参与验证交易。

## 前提条件

### 硬件要求

硬件要求因节点类型不同而异，但通常不是很高。下面是我们推荐的配置。

1. 机器配置
   MAP是一种权益证明网络，其与工作量证明网络有不同的硬件要求。权益证明共识相对于 CPU 的要求较低，但对网络连接和延迟更为敏感。
   下面是在MAP网络上运行验证器所需的标准要求列表：
   - 内存：16 GB RAM
   - CPU：四核 2.5 GHz（64位）
   - 硬盘：256 GB SSD存储空间，加上较好的二级HDD
   - 网络：至少具有 100 MB 的输入/输出以太网，最好具有冗余连接和 HA 交换机
2. 地图数量
   您的账户需要至少有1,000,000 MAP

### 操作系统要求

Atlas 客户端支持主流操作系统——Linux、MacOS、Windows。
这意味着你可以在普通台式机或服务器上运行节点，并在这些设备上安装最适合你的操作系统。 为了避免出现潜在的问题和安全漏洞，请确保你的操作系统为最新。

### 软件要求

构建atlas需要git、Go（版本1.14或更高版本）和C编译器。

## 克隆代码仓库并构建

```shell
git clone https://github.com/mapprotocol/atlas.git
cd atlas
git checkout v1.1.5
make atlas
```

## 运行节点

运行 atlas -h 获取帮助信息

### 运行主网节点

```shell
atlas --datadir ./node console
```

### 运行单节点网络

单节点网络是用于测试和开发目的的网络。 它不与任何其他网络同步，并且不存储任何数据。

要运行一个单节点网络，请运行以下命令：

```shell
atlas --datadir ./node --single console
```

如果在命令中添加 --http 标志，将启用RPC服务器，然后您可以使用 http://127.0.0.1:7445 访问 RPC

```shell
atlas --datadir ./node --single -http --http.addr "127.0.0.1" --http.port 7445 console
```