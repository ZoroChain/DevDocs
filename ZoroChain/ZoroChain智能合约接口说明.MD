## 智能合约调用全局资产接口

在智能合约中调用Native合约、全局资产的接口，如ZORO、BCT等，调用时根据参数类型、个数选择相应接口即可。

### Zoro.NativeNEP5.Call
参数 (string method, byte[] assetId)

返回 byte[]

## Zoro.NativeNEP5.Call
参数 (string method, byte[] assetId, byte[] parameter)

返回 byte[]

## Zoro.NativeNEP5.Call
参数 (string method, object[] parameters)

返回 byte[]

## Zoro.NativeNEP5.Call
参数 (string method, byte[] assetId, byte[] sender, byte[] to, BigInteger amount)

返回 bool

## Zoro.NativeNEP5.GetTransferLog
参数 (byte[] assetId, byte[] txid)

返回 TransferLog 对象

根据txid查询交易详情