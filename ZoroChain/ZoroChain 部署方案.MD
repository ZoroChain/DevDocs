# ZoroChain 部署方案 
##  共识节点部署
### 准备工作
部署一套 ZoroChain 至少需要四个节点用作共识，这里使用四台 CentOS 服务器作为四个节点。每台服务器需要安装运行的依赖环境，如下：

* 安装 dotnet core：
    * sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm
    * sudo yum update
    * sudo yum install dotnet-sdk
* 安装 sqlite、leveldb、libunwind
    * yum install -y sqlite-devel leveldb-devel libunwind-devel

配置完成环境之后记录四台服务器的 IP 地址和要使用的端口，P2P 广播默认端口为 20001，需要在防火墙开通该端口。

使用 Zoro-Cli 创建四个钱包文件，记录钱包公钥。

### 运行 Zoro-Cli
* 分别 clone 编译好的 Zoro-Cli 文件到四台服务器，git clone https://github.com/ZoroChain/Zoro-Publish.git，
或者直接下载发布好的压缩包 https://github.com/ZoroChain/Zoro-Publish/releases ，解压即可。

* 编辑 protocol.json，protocol.json 是 Zoro 区块链相关的配置文件，和 Zoro-Cli.dll 在同一目录下：
    * protocol.json 内容：
    ```
    {
        "ProtocolConfiguration": {
            "NetworkType": "Internet",
            "Magic": 20190111,
            "AddressVersion": 23,
            "SecondsPerBlock": 1,
            "StandbyValidators": [
                "03072384ece95c4febe7211ee4833cd36cdf0f255ec5263b28a4be1f2af7a3d7f3",
                "0208da33cee651a4dc119654b5b8067ade533e39e63a8b4fdbd160d97f80c4cfb4",
                "026f0af815d5c3895205e9f54b9402329f3ecc4d249fc0c8500e9b3ea8a3ec9d5b",
                "03b4e070988627e2a5710b2c8c79f0f0be18821e1af5875b7beeb3fa8a921cde3f"
            ],
            "SeedList": [
                "127.0.0.1:20001",
                "127.0.0.1:20001",
                "127.0.0.1:20001",
                "127.0.0.1:20001"
            ]
        }
    }
    ```
    * Magic：uint 整数，数值要在 [0 - 4294967295] 区间内，用于标记消息来源，确保不同网络的信息在传输过程中不会发送到其他网络。
    * SecondsPerBlock：出块时间，一般在 1-15 秒。
    * StandbyValidators：共识者，将之前创建的四个钱包文件的公钥填到这里。
    * SeedList：种子节点，将之前记录的四台服务器 IP 和端口填在这里。

* 编辑 config.json，config.json 是 Zoro 节点的配置文件，和 Zoro-Cli.dll 在同一目录下：
    * config.json 内容：
    ```
    {
        "ApplicationConfiguration": {
            "Paths": {
                "Chain": "Chain_{0}"
            },
            "P2P": {
                "Port": 20001,
                "WsPort": 20005
            },
            "RPC": {
                "BindAddress": "127.0.0.1",
                "Port": 20332,
                "SslCert": "",
                "SslCertPassword": ""
            },
            "UnlockWallet": {
                "HttpAddress": "http://+:20330/",
                "Path": "",
                "Password": "",
                "StartConsensus": false,
                "IsActive": true
            },
                "PluginURL": "https://github.com/ZoroChain/Zoro-Plugins/releases/download/v{1}/{0}.zip"
        }
    }
    ```
    * Paths: 区块数据保存路径，Chain_{0} 表示 Chain_Magic 数字。
    * P2P Port：P2P 广播端口。
    * RPC BindAddress：RPC 服务绑定的 IP。
    * RPC Port：RPC 服务使用的端口。
    * UnlockWallet IsActive：是否开启打开钱包功能，Zoro 共识节点启动后需要打开钱包，IsActive 为 true 时，启动节点需输入钱包密码，节点启动后会开启一个 Http 服务，然后将钱包文件以 Http Post 方式发送到节点。
    * UnlockWallet HttpAddress：打开钱包的 Http 服务地址。
    * UnlockWallet StartConsensus：是否开启共识，默认 false，因为开启共识需要先打开钱包，节点启动并打开钱包以后使用 start sonsensus 命令开启共识。

* 在四台服务器上分别启动 Zoro-Cli，进入 Zoro-Cli.dll 所在目录，使用 dotnet Zoro-Cli.dll 运行 Zoro-Cli，输入钱包密码。
* 打开钱包：启动节点后，使用 Postman 发送钱包文件到节点，请求地址为：http://节点IP:20330/openwallet
，请求方式：Post，发送数据：钱包 json 文件的内容，打开钱包 json 文件，复制内容到 Postman，打开钱包成功后会返回 Open wallet successfully。
`在四个节点做同样操作，每个节点使用对应的钱包文件，和 protocol.json 中共识节点的公钥一一对应；`
`清除钱包数据使用 http://节点IP:20330/closewallet `。
* 开启共识：在每个节点运行窗口中使用 start consensus 命令开启共识，开启成功后可以看到每个节点的高度开始变化。


## 具有 RPC 功能的普通节点部署
ZoroChain 将 RPC 服务和产生智能合约运行的 Notify 等功能采用插件机制实现，在需要该功能时添加相应的插件即可。发布的共识节点文件中已包含 SimplePolicy 插件。

### 环境准备
准备一台和共识节点同样的环境，安装相应的依赖包。

### 准备 Zoro-Cli
* 下载 Zoro-Cli 文件： clone 编译好的 Zoro-Cli 文件到四台服务器，git clone https://github.com/ZoroChain/Zoro-Publish.git，
或者直接下载发布好的压缩包 https://github.com/ZoroChain/Zoro-Publish/releases ，解压即可。
* 修改配置文件，和部署共识节点时一样编辑 config.json 和 protocol.json 文件，注意这里将 config.json 的  "UnlockWallet" 下的 "IsActive" 设置为 false，因为非共识节点不需要开启钱包功能。
            
* 下载 RpcAgent 和 ApplicationLogs 插件：下载 https://github.com/ZoroChain/Zoro-Plugins/releases/tag/Zoro-Plugins 下的 ApplicationLogs.zip 和 RpcAgent.zip，将解压后的文件放到 Zoro-Cli.dll 所在目录下的 Plugins 文件夹中。
* Zoro-RpcHost.dll；Zoro-RpcHost.dll 是 Zoro 的 RPC 服务，和 RpcAgent 插件一起使用，Zoro-RpcHost.dll 包含在已发布的共识节点文件中，下载 Zoro-Cli 文件时会一起下载。
* 编辑 rpchost.json ，rpchost.json 是 Zoro-RpcHost 的运行配置文件， 和 Zoro-Cli.dll 放在同一目录下：
    * rpchost.json 内容：
    ```
    {
        "ApplicationConfiguration": {
            "AgentAddress": "127.0.0.1",
            "AgentPort": 20334,
            "Port": 20333,
            "SslCert": "",
            "SslCertPassword": "",
            "TimeoutSeconds": 30,
            "MinThreadCount": 1000
        }
    }
    ```
    * AgentAddress：RPC 代理的地址；
    * AgentPort：RPC Agent 使用的端口；
    * Port：Zoro-RpcHost 使用的端口；RPC Agent 会使用该端口和 Zoro 节点进行交互；
    * TimeoutSeconds：RPC 超时时间；
    * MinThreadCount：线程池线程数量配置，默认 1000。

* 编辑 RpcAgent 的 config.json，位置：Plugins\RpcAgent\config.json
    * config.json 的内容：
    ```
    {
        "PluginConfiguration": {
            "Port": 20334,
            "MaxConnections": 10
        }
    }
    ```
    * Port：RpcAgent 使用的端口，和 rpchost.json 中的 AgentPort 相同；
    * MaxConnections：最大连接数量，RpcAgent 可接收并发请求数量。
### 开启 Zoro Rpc
准备好之前的准备工作后，就可以运行具有 Rpc 功能的 Zoro 节点了，首先启动节点：在 Zoro-Cli.dll 所在目录运行 dotnet Zoro-Cli.dll；然后运行 Zoro-RpcHost.dll，在 Zoro-Cli.dll 所在目录运行 dotnet Zoro-RpcHost.dll，启动后显示 RPC server {IP：20334} has connected，说明 RPC 功能启用成功，接下来用 Postman 请求该 Rpc 地址就可以和 Zoro 进行交互了。
例如：
请求 http://RpcIP地址:20334?jsonrpc=2.0&id=1&method=getblockcount&params=[''] ，响应结果为：
{
    "jsonrpc": "2.0",
    "id": "1",
    "result": 173914
}

## 提取 BCP 和 BCT 代币
Zoro 链在启动时，在创世区块中会创建两种代币，BCP 和 BCT，其中 BCP 有总量限制，BCT 可以增发，两种代币的所有者为初始的四个共识节点，要提取代币必须要有四个共识节点钱包的多方签名。

提取 BCP 使用 Transfer：
```
sb.EmitSysCall("Zoro.NativeNEP5.Call", "MintToken", Genesis.BcpContractAddress, targetAddressScripthash, value);
```
提取 BCT 使用 MintToken：
```
sb.EmitSysCall("Zoro.NativeNEP5.Call", "MintToken", Genesis.BctContractAddress, targetAddressScripthash, value);
```
交易均需要四个共识节点钱包的多签，具体可以参考：https://github.com/ZoroChain/InvokeContract/blob/master/InvokeContractTest/BCP/RetrieveBCP.cs .