# Zoro 区块链熟悉
## 计划
* 了解区块链基本概念
* 了解钱包：密钥、地址、代币；本地跑起来一个Zoro-Cli，同步区块数据、创建一个钱包
* 开启Zoro-Rpc-Host，写代码实现一个区块链爬虫，获得自己钱包地址的所有交易数据
* 自己搭建一个Zoro私链，将1、2的操作切到私链进行一次
* 编写一个智能合约，发布合约，实现保存和读取hello world
* 编写nep5智能合约，发布合约，实现一笔nep5资产转账
## Zoro 相关代码
* Zoro： Zoro 区块链核心代码，地址：https://github.com/ZoroChain/Zoro
* Zoro-Cli：Zoro cli 节点程序，地址：https://github.com/ZoroChain/Zoro-Cli
* Zoro-Plugins：运行 Zoro-Cli 时可以选择的一些插件，地址：https://github.com/ZoroChain/Zoro-Plugins
* neo-vm：neo 智能合约虚拟机，Zoro 智能合约目前也用该虚拟机，地址：https://github.com/ZoroChain/neo-vm
* Zoro-RpcHost：Rpc 代码，开启 RPC 时要用，地址：https://github.com/ZoroChain/Zoro-RpcHost
* CowboyLib：RpcAgent 和 Zoro-RpcHost 引用的库，地址：https://github.com/ZoroChain/CowboyLib

