# ONT代码阅读记录 - 共识算法
## vbft共识算法
* 相关代码存放在consensus/vbft目录下
## VRF算法相关的主要函数
### `buildParticipantConfig`
* 根据vrfValue和PosTable，计算得出提议节点，背书节点和提交节点
* 调用`calcParticipantPeers`和`calcParticipant`进行基于PosTable的VRF抽签算法
* 结果保存在`currentParticipantConfig`变量中
* 在每一轮新的共识开始时执行一次
### `startNewRound`
* 开始新一轮的共识流程