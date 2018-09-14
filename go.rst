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

说明：
(1) ErrorCode: 0表示无错误，大于0表示有错误
(2) ErrorDesc: 空表示无错误，有内容表示有错误
(3) Result:
返回结果的结构体，其中结构体的名称，格式是[类名][方法名]Result。
例如Account.GetNonce()的结构体名是AccountGetNonceResult：

::

 type AccountGetNonceResult struct {
 Nonce int64
 }
