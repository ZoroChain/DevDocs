# ONT代码阅读记录 - 共识算法

### vrf核心算法的代码仓库
* 代码仓库："github.com/ontio/ontology-crypto/vrf"
### 计算Vrf
* 函数：`computeVrf`
* 用新块的高度和上个块的Vrf值合并成到一起，序列化后得到用来计算Vrf的输入数据`data`
* 调用`vrf.Vrf(sk, data)`计算出Vrf和VrfProof，其中`sk`是私钥  
### 验证Vrf
* 函数：`verifyVrf`
* 用新块的高度和上个块的Vrf值合并成到一起，序列化后得到用来计算Vrf的输入数据`data`
* 调用`vrf.Verify(pk, data, newVrf, proof)`，其中`pk`是公钥

## ONT的共识处理流程
### 发起提案
* `makeProposal`
  * 构造`blockProposalMsg`消息结构，用来发起提案的新区块，打包交易
  * 验证该区块里的所有交易
  * 将新区块广播给其他节点
* `constructProposalMsg`
  * 构造新的区块，填入交易数据
  * 包括计算新区块的VRF，填入系统交易和用户交易

### 处理提案
* `processProposalMsg`
  * 在收到从其他节点发来的提案消息时执行
  * 验证新区块的VRF
  * 验证新区块打包的交易
  * 如果自身是验证节点，调用`endorseBlock`，为新区块发送背书

### 发送背书
* `endorseBlock`
  * 构造`blockEndorseMsg`消息结构，用来发送对提案区块的背书
  * 将背书广播给其他节点

### 处理背书
* `processMsgEvent`中处理`BlockEndorseMessage`的代码
  * 调用`endorseDone`，判断背书是否达成了共识
  * 如果背书达成了共识，调用`makeCommitment`发起提交流程

### 发送提交
* `commitBlock`
  * 构造`blockCommitMessage`消息结构，用来发送提交区块的消息

### 处理提交
* `processMsgEvent`中处理`BlockCommitMessage`的代码
  * 调用`commitDone`，判断提交是否达成共识
  * 如果达成了共识，调用`makeSealed`，执行生成新区块的流程

### 生成新区块  
* `sealProposal`
  * 调用`sealBlock`生成新区块
  * 调用`startNewRound`开始新一轮的共识流程

## VBFT的网络消息处理
### 网络消息接收后的处理函数
* `onConsensusMsg`
  * 在`Server.run`函数中，采用`routine`的方式运行
  * 调用`processConsensusMsg`，由后者调用`processMsgEvent`，对消息进行实际的处理

### 消息处理函数
* `processConsensusMsg`
  * 在`onConsensusMsg`函数中被调用
  * 在`processProposalMsg`函数中被调用
  * 在`endorseBlock`函数中被调用
  * 在`commitBlock`函数中被调用
  * 将消息发送给频道`msgC`处理，后者会调用`processMsgEvent`

### 网络消息
* BlockProposalMessage
  * 由提案人发出的新区块的提案消息
  * 对应的结构体: `blockProposalMsg`
```
type blockProposalMsg struct {
    Block *Block `json:"block"`
}
```
* BlockEndorseMessage
  * 由验证节点发出的背书消息
  * 对应的结构体: `blockEndorseMsg`
```
type blockEndorseMsg struct {
    Endorser          uint32          `json:"endorser"`
    EndorsedProposer  uint32          `json:"endorsed_proposer"`
    BlockNum          uint32          `json:"block_num"`
    EndorsedBlockHash common.Uint256  `json:"endorsed_block_hash"`
    EndorseForEmpty   bool            `json:"endorse_for_empty"`
    FaultyProposals   []*FaultyReport `json:"faulty_proposals"`
    ProposerSig       []byte          `json:"proposer_sig"`
    EndorserSig       []byte          `json:"endorser_sig"`
}
```
* BlockCommitMessage
  * 由提交节点发出的提交消息
  * 对应的结构体: `blockCommitMsg`
```
type blockCommitMsg struct {
    Committer       uint32            `json:"committer"`
    BlockProposer   uint32            `json:"block_proposer"`
    BlockNum        uint32            `json:"block_num"`
    CommitBlockHash common.Uint256    `json:"commit_block_hash"`
    CommitForEmpty  bool              `json:"commit_for_empty"`
    FaultyVerifies  []*FaultyReport   `json:"faulty_verifies"`
    ProposerSig     []byte            `json:"proposer_sig"`
    EndorsersSig    map[uint32][]byte `json:"endorsers_sig"`
    CommitterSig    []byte            `json:"committer_sig"`
}
```

## VRF算法相关的主要函数
### `buildParticipantConfig`
* 根据vrfValue和PosTable，计算得出提议节点，背书节点和提交节点
* 调用`calcParticipantPeers`和`calcParticipant`进行基于PosTable的VRF抽签算法
* 结果保存在`currentParticipantConfig`变量中
* 在每一轮新的共识开始时执行一次
### `startNewRound`
* 开始新一轮的共识流程