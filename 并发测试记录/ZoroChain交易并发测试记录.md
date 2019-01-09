# ZoroChain交易并发测试记录
## 2018/12/29
### NativeNEP5交易
* 带宽上限1mb/s，并发数:500/s，交易总量:5w，MaxTxnPerBlock：500，共识节点:Zoro1~Zoro4
  * 总共耗时:大约8~9分钟
  * TPS:大约100
  
## 2019/1/8
### NativeNEP5交易
* 带宽上限5mb/s，并发数:500/s，交易总量:5w，MaxTxnPerBlock：1000，共识节点:Zoro1~Zoro4
  * 总共耗时: 240秒
  * TPS:208
* 带宽上限5mb/s，并发数:500/s，交易总量:5w，MaxTxnPerBlock：1000，共识节点:Zoro1~Zoro4
  * 总共耗时: 大约280秒
  * TPS:179
* 带宽上限5mb/s，并发数:500/s，交易总量:5w，MaxTxnPerBlock：1000，共识节点:Zoro6~Zoro9
  * 总共耗时: 162秒
  * TPS: 308
* 带宽上限5mb/s，并发数:500/s，交易总量:10w，MaxTxnPerBlock：1000，共识节点:Zoro6~Zoro9
  * 总共耗时: 大约400秒
  * TPS: 250

### NEP5交易
* 带宽上限5mb/s，并发数:500/s，交易总量:5w，MaxTxnPerBlock：1000，共识节点:Zoro6~Zoro9
  * 总共耗时: 大约340秒
  * TPS:大约147
  * 测试次数:2次

## 2019/1/9
### NativeNEP5交易
* 带宽上限10mb/s，并发数:500/s，交易总量:5w，MaxTxnPerBlock：500，共识节点:Zoro6~Zoro9
  * 测试1: 
    * 总共耗时:146秒
    * TPS:342
  * 测试2:
    * 总共耗时:138
    * TPS:362秒
  * 测试3:
    * 总共耗时:180秒
    * TPS:278  
  * 带宽占用:>10Mbps

### NEP5交易
* 带宽上限10mb/s，并发数:500/s，交易总量:5w，MaxTxnPerBlock：500，共识节点:Zoro6~Zoro9
  * 测试1: 
    * 总共耗时: 244秒
    * TPS:205
  * 测试2: 
    * 总共耗时: 287秒
    * TPS:174
  * 测试2: 
    * 总共耗时: 268秒
    * TPS:186    
  * 带宽占用:>10Mbps

### NativeNEP5交易
* 带宽上限10mb/s，并发数:500/s，交易总量:5w，MaxTxnPerBlock：500，共识节点:Zoro1~Zoro4
  * 测试1: 
    * 总共耗时:226秒
    * TPS:221
  * 测试2:
    * 总共耗时:240秒
    * TPS:208
  * 测试3:
    * 总共耗时:275秒
    * TPS:182
  * 测试3:
    * 总共耗时:279秒
    * TPS:179
  * 带宽占用:>10Mbps