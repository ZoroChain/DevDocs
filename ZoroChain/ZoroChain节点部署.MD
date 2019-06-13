## 具有 RPC 功能的普通节点部署
ZoroChain 将 RPC 服务和产生智能合约运行的 Notify 等功能采用插件机制实现，在需要该功能时添加相应的插件即可。发布的共识节点文件中已包含 SimplePolicy 插件。

### 环境准备
准备一台和[共识节点](ZoroChain%20部署方案.MD#准备工作)同样的环境，安装相应的依赖包。

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
