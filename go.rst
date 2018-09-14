Bumo Go SDK
===========

概述
----

本文档简要概述Bumo Go SDK常用接口文档，方便开发者更方便地写入和查询BU区块链。

配置
----

包引用
~~~~~~

所依赖的golang包在src文件夹中寻找，依赖的golang包如下：

::

 //获取包
 go get github.com/bumoproject/bumo-sdk-go

名词解析
--------

操作BU区块链： 向BU区块链写入或修改数据

提交交易： 向BU区块链发送修改的内容

查询BU区块链： 查询BU区块链中的数据

账户服务： 提供账户相关的有效性校验与查询接口

资产服务： 提供资产相关的查询接口

Ctp10Token服务： 提供合约资产相关的有效性校验与查询接口

合约服务： 提供合约相关的有效性校验与查询接口

交易服务： 提供交易相关的提交与查询接口

区块服务： 提供区块的查询接口

账户nonce值：每个账户都维护一个序列号，用于用户提交交易时标识交易的执行顺序

参数与响应
----------

请求参数
~~~~~~~~

请求参数的格式，是[类名][方法名]Request，比如Account.GetInfo()的请求参数是AccountGetInfoRequest。

请求参数的成员，是各个方法的入参的成员变量名。

例如：Account.GetInfo()的入参成员是address，那么AccountGetInfoRequest的结构如下：

::

 type AccountGetInfoRequest struct {
 address string
 }

响应结果
^^^^^^^^

响应结果的格式，包含错误码，错误描述和result，格式是[类名][方法名]Response。

例如: Account.GetInfo()的结构体名是AccountGetInfoResponse：

::

 type AccountGetInfoResponse struct {
 ErrorCode int
 ErrorDesc string
 Result  AccountGetInfoResult
 }

.. note:: |

 说明：

 (1) ErrorCode: 0表示无错误，大于0表示有错误

 (2) ErrorDesc: 空表示无错误，有内容表示有错误

 (3) Result:

 返回结果的结构体，其中结构体的名称，格式是[类名][方法名]Result。

 例如：Account.GetNonce()的结构体名是AccountGetNonceResult：

::

 type AccountGetNonceResult struct {
 Nonce int64
 }

使用方法
--------

这里介绍SDK的使用流程，然后调用相应服务的接口，其中服务包括账户服务、资产服务、合约服务、交易服务、区块服务，接口按使用分类分为生成公私钥地址接口、有效性校验接口、查询接口、提交交易相关接口。

包导入
~~~~~~

导入使用的包

::

 import(
 "github.com/bumoproject/bumo-sdk-go/src/model"
 "github.com/bumoproject/bumo-sdk-go/src/sdk"
 )

生成SDK实例
~~~~~~~~~~~

初始化SDK结构

::

 var testSdk sdk.sdk

调用SDK的接口Init

::

 url :="http://seed1.bumotest.io:26002"
 var reqData model.SDKInitRequest
 reqData.SetUrl(url)
 reqData := testSdk.Init(reqData)

生成公私钥地址
~~~~~~~~~~~~~

通过调用Account的Create生成账户，例如：

::

resData :=testSdk.Account.Create()

有效性校验
~~~~~~~~~~

此接口用于校验信息的有效性，直接调用相应的接口即可，比如，校验账户地址有效性，调用如下：

::

 //初始化传入参数
 var reqData model.AccountCheckValidRequest
 var address string = "buQtfFxpQP9JCFgmu4WBojBbEnVyQGaJDgGn"
 reqData.SetAddress(address)
 //调用接口检查
 resData := testSdk.Account.CheckValid(reqData)

查询
~~~~

调用相应的接口，例如：查询账户信息

::

 //初始化传入参数
 var reqData model.AccountGetInfoRequest
 var address string = "buQtfFxpQP9JCFgmu4WBojBbEnVyQGaJDgGn"
 reqData.SetAddress(address)
 //调用接口查询 
 resData := testSdk.Account.GetInfo(reqData)

提交交易
~~~~~~~~

提交交易的过程包括以下几步：获取账户nonce值，构建操作，构建交易Blob,签名交易和广播交易

获取账户nonce值
^^^^^^^^^^^^^^^

开发者可自己维护各个账户nonce，在提交完一个交易后，自动递增1，这样可以在短时间内发送多笔交易，否则，必须等上一个交易执行完成后，账户的nonce值才会加1。接口调用如下：

::

 //初始化请求参数
 var reqData model.AccountGetNonceRequest
 var address string = "buQtfFxpQP9JCFgmu4WBojBbEnVyQGaJDgGn"
 reqData.SetAddress(address)
 resData := testSdk.Account.GetNonce(resData)
 //调用GetNonce接口
 resData := testSdk.Account.GetNonce(reqData)

构建操作
^^^^^^^^

这里的操作是指在交易中做的一些动作。例如：构建发送BU操作BUSendOperation，调用如下:

::

 var buSendOperation model.buSendOperation
 buSendOperation.Init()
 var amount int64 = 100
 var address string = "buQVU86Jm4FeRW4JcQTD9Rx9NkUkHikYGp6z"
 buSendOperation.SetAmount(amount)
 buSendOperation.SetDestAddress(address)

构建交易Blob
^^^^^^^^^^^^

该接口用于生成交易Blob串，接口调用如下：

.. note:: |
 gasPrice和feeLimit的单位是MO，且 1BU =10^8 MO

::

 //初始化传入参数
 var reqDataBlob model.TransactionBuildBlobRequest
 reqDataBlob.SetSourceAddress(surceAddress)
 reqDataBlob.SetFeeLimit(feeLimit)
 reqDataBlob.SetGasPrice(gasPrice)
 reqDataBlob.SetNonce(senderNonce)
 reqDataBlob.SetOperation(buSendOperation)
 //调用BuildBlob接口
 resDataBlob := testSdk.Transaction.BuildBlob(reqDataBlob)

签名交易
^^^^^^^^

该接口用于交易发起者使用私钥对交易进行签名。接口调用如下：

::

 //初始化传入参数
 PrivateKey := []string{"privbUPxs6QGkJaNdgWS2hisny6ytx1g833cD7V9C3YET9mJ25wdcq6h"}
 var reqData model.TransactionSignRequest
 reqData.SetBlob(resDataBlob.Result.Blob)
 reqData.SetPrivateKeys(PrivateKey)
 //调用Sign接口
 resDataSign := testSdk.Transaction.Sign(reqData)

广播交易
^^^^^^^^

该接口用于向BU区块链发送交易，触发交易的执行。接口调用如下：

::

 var reqData model.TransactionSubmitRequest
 reqData.SetBlob(resDataBlob.Result.Blob)
 reqData.SetSignatures(resDataSign.Result.Signatures)
 //调用Submit接口
 resDataSubmit := testSdk.Transaction.Submit(reqData)

账户服务
--------

账户服务主要是账户相关的接口，包括5个接口：CheckValid, GetInfo,
GetNonce, GetBalance, GetAssets, GetMetadata。

CheckValid
~~~~~~~~~~~

接口说明:

该接口用于检测账户地址的有效性。

调用方法：

CheckValid(model.AccountCheckValidRequest)model.AccountCheckValidResponse

请求参数：

+---------+--------+-----------------+
| 参数    | 类型    | 描述            |
+=========+========+=================+
| address | string | 待检测的账户地址 |
+---------+--------+-----------------+

响应数据：

+---------+--------+----------------+
| 参数    | 类型    | 描述           |
+=========+========+================+
| IsValid | string | 账户地址是否有效 |
+---------+--------+----------------+

..

 错误码：
