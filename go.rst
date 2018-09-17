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

+---------+--------+------------------+
| 参数    | 类型   | 描述             |
+=========+========+==================+
| address | string | 待检测的账户地址 |
+---------+--------+------------------+



响应数据：

+---------+--------+------------------+
| 参数    | 类型   | 描述             |
+=========+========+==================+
| IsValid | string | 账户地址是否有效 |
+---------+--------+------------------+


错误码：

+--------------+--------+--------------+
| 异常         | 错误码 | 描述         |
+==============+========+==============+
| SYSTEM_ERROR | 20000  | System error |
+--------------+--------+--------------+


示例

::

 var reqData model.AccountCheckValidRequest
 address := "buQtfFxpQP9JCFgmu4WBojBbEnVyQGaJDgGn"
 reqData.SetAddress(address)
 resData := testSdk.Account.CheckValid(reqData)
 if resData.ErrorCode == 0 {
 fmt.Println(resData.Result.IsValid)
 }

Create
~~~~~~

接口说明：

形成私钥对

调用方法：

Create() model.AccountCreateResponse

响应数据：

+------------+--------+------+
| 参数       | 类型   | 描述 |
+============+========+======+
| PrivateKey | string | 私钥 |
+------------+--------+------+
| PublicKey  | string | 公钥 |
+------------+--------+------+
| Address    | string | 地址 |
+------------+--------+------+

示例

::

 resData := testSdk.Account.Create()
 if resData.ErrorCode == 0 {
 fmt.Println("Address:",resData.Result.Address)
 fmt.Println("PrivateKey:",resData.Result.PrivateKey)
 fmt.Println("PublicKey:",resData.Result.PublicKey)
 }

GetInfo-Account
~~~~~~~~~~~~~~~

接口说明：

查询账户信息

调用方法：

GetInfo(model.AccountGetInfoRequest) model.AccountGetInfoResponse

请求参数：

+---------+--------+------------------+
| 参数    | 类型   | 描述             |
+=========+========+==================+
| address | string | 待检测的账户地址 |
+---------+--------+------------------+

响应数据：

+---------+------------------+----------------+
| 参数    | 类型             | 描述           |
+=========+==================+================+
| Address | string           | 账户地址       |
+---------+------------------+----------------+
| Balance | int64            | 账户余额       |
+---------+------------------+----------------+
| Nonce   | int64            | 账户交易序列号 |
+---------+------------------+----------------+
| Priv    | `Priv <#priv>`_  | 账户权限       |
+---------+------------------+----------------+

Priv
~~~~

+--------------+----------------------------+--------------+
| 参数         | 类型                       | 描述         |
+==============+============================+==============+
| MasterWeight | int64                      | 账户自身权重 |
+--------------+----------------------------+--------------+
| Signers      | [] `Signer <#signer>`__    | 签名者权重   |
+--------------+----------------------------+--------------+
| Thresholds   | `Threshold <#threshold>`__ | 门限         |
+--------------+----------------------------+--------------+


Signer
~~~~~~

+---------+--------+--------------+
| 参数    | 类型   | 描述         |
+=========+========+==============+
| Address | string | 签名账户地址 |
+---------+--------+--------------+
| Weight  | int64  | 签名账户权重 |
+---------+--------+--------------+

Threshold
~~~~~~~~~~

+----------------+------------------------------------+--------------------+
| 参数           | 类型                               | 描述               |
+================+====================================+====================+
| TxThreshold    | string                             | 交易默认门限       |
+----------------+------------------------------------+--------------------+
| TypeThresholds | `TypeThreshold <#typethreshold>`__ | 不同类型交易的门限 |
+----------------+------------------------------------+--------------------+

错误码：

+-----------------------+--------+-------------------------+
| 异常                  | 错误码 | 描述                    |
+=======================+========+=========================+
| INVALID_ADDRESS_ERROR | 11006  | Invalid address         |
+-----------------------+--------+-------------------------+
| CONNECTNETWORK_ERROR  | 11007  | Fail to Connect network |
+-----------------------+--------+-------------------------+
| SYSTEM_ERROR          | 20000  | System error            |
+-----------------------+--------+-------------------------+

示例

::

 var reqData model.AccountGetInfoRequest
 var address string = "buQtfFxpQP9JCFgmu4WBojBbEnVyQGaJDgGn"
 reqData.SetAddress(address)
 resData := testSdk.Account.GetInfo(reqData)
 if resData.ErrorCode == 0 {
 data, _ := json.Marshal(resData.Result)
 fmt.Println("Info:", string(data))
 }

GetNonce
~~~~~~~~

接口说明：

该接口用于获取账户的nonce

调用方法：

GetNonce(model.AccountGetNonceRequest) model.AccountGetNonceResponse

请求参数：

+---------+--------+------------------+
| 参数    | 类型   | 描述             |
+=========+========+==================+
| address | string | 待检测的账户地址 |
+---------+--------+------------------+

响应数据：

+---------+--------+------------------+
| 参数    | 类型   | 描述             |
+=========+========+==================+
| address | string | 待检测的账户地址 |
+---------+--------+------------------+

错误码：

+-----------------------+--------+-------------------------+
| 异常                  | 错误码 | 描述                    |
+=======================+========+=========================+
| INVALID_ADDRESS_ERROR | 11006  | Invalid address         |
+-----------------------+--------+-------------------------+
| CONNECTNETWORK_ERROR  | 11007  | Fail to Connect network |
+-----------------------+--------+-------------------------+
| SYSTEM_ERROR          | 20000  | System error            |
+-----------------------+--------+-------------------------+

示例

::

 var reqData model.AccountGetNonceRequest
 var address string = "buQtfFxpQP9JCFgmu4WBojBbEnVyQGaJDgGn"
 reqData.SetAddress(address)
 if resData.ErrorCode == 0 {
 fmt.Println(resData.Result.Nonce)
 }

GetBalance-Account
~~~~~~~~~~~~~~~~~~~

接口说明：

该接口用于获取账户的Balance

调用方法：

GetBalance(model.AccountGetBalanceRequest)
model.AccountGetBalanceResponse

请求参数：

+---------+--------+------------------+
| 参数    | 类型   | 描述             |
+=========+========+==================+
| address | string | 待检测的账户地址 |
+---------+--------+------------------+

响应数据：

+---------+-------+--------------+
| 参数    | 类型  | 描述         |
+=========+=======+==============+
| Balance | int64 | 该账户的余额 |
+---------+-------+--------------+

错误码：

+-----------------------+--------+-------------------------+
| 异常                  | 错误码 | 描述                    |
+=======================+========+=========================+
| INVALID_ADDRESS_ERROR | 11006  | Invalid address         |
+-----------------------+--------+-------------------------+
| CONNECTNETWORK_ERROR  | 11007  | Fail to Connect network |
+-----------------------+--------+-------------------------+
| SYSTEM_ERROR          | 20000  | System error            |
+-----------------------+--------+-------------------------+

示例

::

 var reqData model.AccountGetBalanceRequest
 var address string = "buQtfFxpQP9JCFgmu4WBojBbEnVyQGaJDgGn"
 reqData.SetAddress(address)
 resData := testSdk.Account.GetBalance(reqData)
 if resData.ErrorCode == 0 {
 fmt.Println("Balance", resData.Result.Balance)
 }

GetAssets
~~~~~~~~~~

接口说明：

该接口用于获取账户的nonce

调用方法：

GetAssets(model.AccountGetAssetsRequest) model.AccountGetAssetsResponse

请求参数：

+---------+--------+------------------+
| 参数    | 类型   | 描述             |
+=========+========+==================+
| address | string | 待检测的账户地址 |
+---------+--------+------------------+

响应数据：

+--------+-----------------------+----------+
| 参数   | 类型                  | 描述     |
+========+=======================+==========+
| Assets | [] `Asset <#asset>`__ | 账户资产 |
+--------+-----------------------+----------+

Asset
~~~~~

+--------+----------------+--------------+
| 参数   | 类型           | 描述         |
+========+================+==============+
| Key    | `Key <#key>`__ | 资产惟一标识 |
+--------+----------------+--------------+
| Amount | int64          | 资产数量     |
+--------+----------------+--------------+

Key
~~~~

+--------+--------+----------------------+
| 参数   | 类型   | 描述                 |
+========+========+======================+
| Code   | string | 资产编码，长度[1 64] |
+--------+--------+----------------------+
| Issuer | string | 资产发行账户地址     |
+--------+--------+----------------------+

错误码：

+-----------------------+--------+-------------------------+
| 异常                  | 错误码 | 描述                    |
+=======================+========+=========================+
| INVALID_ADDRESS_ERROR | 11006  | Invalid address         |
+-----------------------+--------+-------------------------+
| CONNECTNETWORK_ERROR  | 11007  | Fail to Connect network |
+-----------------------+--------+-------------------------+
| SYSTEM_ERROR          | 20000  | System error            |
+-----------------------+--------+-------------------------+

示例：

::

 var reqData model.AccountGetAssetsRequest
 var address string = "buQtfFxpQP9JCFgmu4WBojBbEnVyQGaJDgGn"
 reqData.SetAddress(address)
 resData := testSdk.Account.GetAssets(reqData)
 if resData.ErrorCode == 0 {
 data, _ := json.Marshal(resData.Result.Assets)
 fmt.Println("Assets:", string(data))
 }

GetMetadata
~~~~~~~~~~~~

接口说明：

获取账户的metadata信息

调用方法：

GetMetadata(model.AccountGetMetadataRequest)
model.AccountGetMetadataResponse

请求参数：

+---------+--------+-------------------------------------+
| 参数    | 类型   | 描述                                |
+=========+========+=====================================+
| address | string | 待检测的账户地址                    |
+---------+--------+-------------------------------------+
| key     | string | 选填，metadata关键字，长度[1, 1024] |
+---------+--------+-------------------------------------+

响应数据：

+-----------+-----------------------------+------+
| 参数      | 类型                        | 描述 |
+===========+=============================+======+
| Metadatas | [] `Metadata <#metadata>`__ | 账户 |
+-----------+-----------------------------+------+

Metadata
~~~~~~~~

+---------+--------+------------------+
| 参数    | 类型   | 描述             |
+=========+========+==================+
| Key     | string | metadata的关键词 |
+---------+--------+------------------+
| Value   | string | metadata的内容   |
+---------+--------+------------------+
| Version | int64  | metadata的版本   |
+---------+--------+------------------+

错误码：

+-----------------------+--------+----------------------------------------------+
| 异常                  | 错误码 | 描述                                         |
+=======================+========+==============================================+
| INVALID_ADDRESS_ERROR | 11006  | Invalid address                              |
+-----------------------+--------+----------------------------------------------+
| CONNECTNETWORK_ERROR  | 11007  | Fail to Connect network                      |
+-----------------------+--------+----------------------------------------------+
| INVALID_DATAKEY_ERROR | 11011  | The length of key must be between 1 and 1024 |
+-----------------------+--------+----------------------------------------------+
| SYSTEM_ERROR          | 20000  | System error                                 |
+-----------------------+--------+----------------------------------------------+

示例：

::

 var reqData model.AccountGetMetadataRequest
 var address string = "buQemmMwmRQY1JkcU7w3nhruoX5N3j6C29uo"
 reqData.SetAddress(address)
 resData := testSdk.Account.GetMetadata(reqData)
 if resData.ErrorCode == 0 {
 data, _ := json.Marshal(resData.Result.Metadatas)
 fmt.Println("Metadatas:", string(data))
 }

资产服务
--------

资产服务主要是资产相关的接口，目前有1个接口：GetInfo

GetInfo-Asset
^^^^^^^^^^^^^^

接口说明：

取账户指定资产数量

调用方法：

GetInfo(model.AssetGetInfoRequest) model.AssetGetInfoResponse

请求参数：

+---------+--------+-----------------------------+
| 参数    | 类型   | 描述                        |
+=========+========+=============================+
| address | string | 必填，待查询的账户地址      |
+---------+--------+-----------------------------+
| code    | string | 必填，资产编码，长度[1, 64] |
+---------+--------+-----------------------------+
| issuer  | string | 必填，资产发行账户地址      |
+---------+--------+-----------------------------+

响应参数：

+--------+-----------------------+----------+
| 参数   | 类型                  | 描述     |
+========+=======================+==========+
| Assets | [] `Asset <#asset>`__ | 账户资产 |
+--------+-----------------------+----------+

错误码：

+-------------------------+-------------------------+------------------+
| 异常                    | 错误码                  | 描述             |
+=========================+=========================+==================+
| INVALID_ADDRESS_ERROR   | 11006                   | Invalid address  |
+-------------------------+-------------------------+------------------+
| CONNECTNETWORK_ERROR    | 11007                   | Fail to Connect  |
|                         |                         | network          |
+-------------------------+-------------------------+------------------+
| INVALID_ASSET_CODE_ERRO | 11023                   | The length of    |
| R                       |                         | asset code must  |
|                         |                         | be between 1 and |
|                         |                         | 1024             |
+-------------------------+-------------------------+------------------+
| INVALID_ISSUER_ADDRESS_ | 11027                   | Invalid issuer   |
| ERROR                   |                         | address          |
+-------------------------+-------------------------+------------------+
| SYSTEM_ERROR            | 20000                   | System error     |
+-------------------------+-------------------------+------------------+

示例：

::

 var reqData model.AssetGetInfoRequest
 var address string = "buQemmMwmRQY1JkcU7w3nhruoX5N3j6C29uo"
 reqData.SetAddress(address)
 reqData.SetIssuer("buQnc3AGCo6ycWJCce516MDbPHKjK7ywwkuo")
 reqData.SetCode("HNC")
 resData := testSdk.Token.Asset.GetInfo(reqData)
 if resData.ErrorCode == 0 {
 data, _ := json.Marshal(resData.Result.Assets)
 fmt.Println("Assets:", string(data))
 }

合约服务：
--------

合约服务主要是合约相关的接口,目前有1个接口:GetInfo

GetInfo-contract
^^^^^^^^^^^^^^^^^

接口说明：

获取合约信息

调用方法：

GetInfo(model.ContractGetInfoRequest) model.ContractGetInfoResponse

请求参数：

+-----------------+--------+--------------------+
| 参数            | 类型   | 描述               |
+=================+========+====================+
| contractAddress | string | 必填，合约账户地址 |
+-----------------+--------+--------------------+

响应数据：

+---------+--------+-----------------+
| 参数    | 类型   | 描述            |
+=========+========+=================+
| Type    | int64  | 合约类型，默认0 |
+---------+--------+-----------------+
| Payload | string | 合约代码        |
+---------+--------+-----------------+

错误码：

+-------------------------+-------------------------+------------------+
| 异常                    | 错误码                  | 描述             |
+=========================+=========================+==================+
| INVALID_CONTRACTADDRESS | 11037                   | Invalid contract |
| _ERROR                  |                         | address          |
+-------------------------+-------------------------+------------------+
| CONTRACTADDRESS_NOT_CON | 11038                   | contractAddress  |
| TRACTACCOUNT_ERROR      |                         | is not a         |
|                         |                         | contract account |
+-------------------------+-------------------------+------------------+
| CONNECTNETWORK_ERROR    | 11007                   | Fail to Connect  |
|                         |                         | network          |
+-------------------------+-------------------------+------------------+
| SYSTEM_ERROR            | 20000                   | System error     |
+-------------------------+-------------------------+------------------+

示例：

::

 var reqData model.ContractGetInfoRequest
 var address string = "buQfnVYgXuMo3rvCEpKA6SfRrDpaz8D8A9Ea"
 reqData.SetAddress(address)
 resData := testSdk.Contract.GetInfo(reqData)
 if resData.ErrorCode == 0 {
 data, _ := json.Marshal(resData.Result.Contract)
 fmt.Println("Contract:", string(data))
 }

交易服务
--------

交易服务主要是交易相关的接口，目前有5个接口：BuildBlob, EvaluateFee,
Sign, Submit, GetInfo。

其中调用BuildBlob之前需要构建一些操作，目前操作有16种，分别包括AccountActivateOperation，AccountSetMetadataOperation,
AccountSetPrivilegeOperation, AssetIssueOperation, AssetSendOperation,
BUSendOperation, Ctp10TokenIssueOperation, Ctp10TokenTransferOperation,
Ctp10TokenTransferFromOperation, Ctp10TokenApproveOperation,
Ctp10TokenAssignOperation, Ctp10TokenChangeOwnerOperation,
ContractInvokeByAssetOperation, ContractInvokeByBUOperation,
LogCreateOperation,ContractCreateOperation

操作说明
~~~~~~~~

BaseOperation
^^^^^^^^^^^^^^

操作对象，根据不同的操作生成，详情如下：

AccountActivateOperation

+---------------+--------+---------------------------------------+
| 成员变量      | 类型   | 描述                                  |
+===============+========+=======================================+
| sourceAddress | string | 选填，操作源账户                      |
+---------------+--------+---------------------------------------+
| destAddress   | string | 必填，目标账户地址                    |
+---------------+--------+---------------------------------------+
| initBalance   | int64  | 必填，初始化资产，大小[1, max(int64)] |
+---------------+--------+---------------------------------------+
| metadata      | string | 选填，备注                            |
+---------------+--------+---------------------------------------+

AccountSetMetadataOperation

+---------------+--------+---------------------------------------+
| 成员变量      | 类型   | 描述                                  |
+===============+========+=======================================+
| sourceAddress | string | 选填，操作源账户                      |
+---------------+--------+---------------------------------------+
| key           | string | 必填，metadata的关键词，长度[1, 1024] |
+---------------+--------+---------------------------------------+
| value         | string | 选填，metadata的内容，长度[0, 256K]   |
+---------------+--------+---------------------------------------+
| version       | int64  | 选填，metadata的版本                  |
+---------------+--------+---------------------------------------+
| deleteFlag    | bool   | 选填，是否删除metadata                |
+---------------+--------+---------------------------------------+
| metadata      | string | 选填，备注                            |
+---------------+--------+---------------------------------------+

AccountSetPrivilegeOperation

+-----------------------+--------------------------+---------------------------------------+
| 成员变量               | 类型                    | 描述                                  |
+=======================+==========================+=======================================+
| sourceAddress         | string                  | 选填，操作源账户                       |
+-----------------------+--------------------------+---------------------------------------+
| masterWeight          | string                  | 选填，账户自身权重，                    |
|                       |                         | 大小[0, max(uint32)]                  |
+-----------------------+--------------------------+---------------------------------------+
| signers               | []  `Signer`_           | 选填，签名者权重列表                    |
+-----------------------+--------------------------+---------------------------------------+
| txThreshold           | string                  | 选填，交易门限，大小[0, max(int64)]     |
+-----------------------+--------------------------+---------------------------------------+
| typeThreshold         | `TypeThreshold`_       | 选填，指定类型交易门限                  |
+-----------------------+--------------------------+---------------------------------------+
| metadata              | string                  | 选填，备注                             |
+-----------------------+--------------------------+---------------------------------------+


