BUMO ATP标准
============

概述
----

ATP1.0(Account based Tokenization Protocol)指基于BuChain的账号结构对资产进行发行、转移和增发token的标准协议，token在此文代表账号资产。

目标
--------

标准协议可以让其他应用程序方便地调用接口在BUMO上进行token的发行、转移和增发操作。

Token属性参数
-------------

发行的token需要通过设置token源账户的metadata来记录token的相关属性。方便应用程序管理和查询token数据信息。

+--------------+----------------------------+
| 变量         | 描述                       |
+==============+============================+
| name         | token 名称                 |
+--------------+----------------------------+
| code         | token 代码                 |
+--------------+----------------------------+
| description  | token 描述                 |
+--------------+----------------------------+
| decimals     | token 小数位数             |
+--------------+----------------------------+
| totalSupply  | token 总量                 |
+--------------+----------------------------+
| icon         | token 图表（optional）     |	
+--------------+----------------------------+	
| version      | ATP 版本                   |
+--------------+----------------------------+

.. note:: 

 - code：推荐使用大写简拼。
 - decimals：小数位在0~8的范围，0表示无小数位。
 - totalSupply：范围是0~2^63-1。0表示不固定token的上限。
 - icon：base64位编码，图标文件大小是32k以内,推荐200*200像素。

操作
--------

BUMO CTP标准中的操作如下：

登记token
^^^^^^^^^^

登记token即设置token的metadata参数。发送Setting Metadata的交易，设置token metadata参数key、value和version。如下例子:

**json格式**

::

 {
  "type": 4,
  "set_metadata": {
    "key": "asset_property_DT",
    "value": "{\"name\":\"Demon Token\",\"code\":\"DT\",\"totalSupply\":\"10000000000000\",\"decimals\":8,
    \"description\":\"This is hello Token\",\"icon\":\"iVBORw0KGgoAAAANSUhEUgAAAAE....\",\"version\":\"1.0\"}",
    "version": 0
  }
 }

.. note::

 key值必须是asset_property_前缀和token code的组合(参考发行token的code参数)。
 设置成功后通过查询指定metadata可以看到metadata设置的数据。

发行token
^^^^^^^^^^

发行token即账户发行一笔数字token，执行成功后账户的token余额中会出现这一笔token。客户端通过发起一笔操作类型是Issuing Assets的交易,设置参数amount(发行的数量)、code(token代码)。
例如：发行一笔数量是10000,精度为8的的DT token。

**json格式**

::

 {
  "type": 2,
  "issue_asset": {
    "amount": 1000000000000,
    "code": "DT"
  }
 }

转移token
^^^^^^^^^

转移token即账户将一笔token转给目标账户。设置参数，发送Transferring Assets的交易。以下是相应参数。

+----------------------------------+------------------------------------+
| 参数                             | 描述                               |
+==================================+====================================+
| pay_asset.dest_address           | 目标账户地址                       |
+----------------------------------+------------------------------------+
| pay_asset.asset.key.issuer       | token发行方地址                    |
+----------------------------------+------------------------------------+
| pay_asset.asset.key.code         | token 代码                         |
+----------------------------------+------------------------------------+
| pay_asset.asset.amount           | 要转移的数量*精度                  |
+----------------------------------+------------------------------------+
| pay_asset.input                  | 触发合约调用的入参，默认为空字符串 |
+----------------------------------+------------------------------------+


下面是给已激活的目标账户buQaHVCwXj9ERtFznDnAuaQgXrwj2J7iViVK转移数量500000000000的DT的例子：

**json格式**

::

    {
      "type": 3,
      "pay_asset": {
        "dest_address": "buQaHVCwXj9ERtFznDnAuaQgXrwj2J7iViVK",
        "asset": {
          "key": {
            "issuer": "buQhzVyca8tQhnqKoW5XY1hix2mCt5KTYzcD",
            "code": "DT"
          },
          "amount": 500000000000
        }
      }
    }

转移成功后通过查询token可以看到目标账户拥有amount数量的DT。

.. note:: 给未激活的目标账户转移token，交易的执行结果是失败的。

增发token
^^^^^^^^^

增发token即账户继续在原token代码上发行一定数量的token，通过设置和之前发行token相同的交易类型代码，继续发送发行token的交易。
应用程序根据具体业务去控制增发token数量是否超过totalSupply，增发成功后可以看到token数量会有所增加。

查询token
^^^^^^^^^^

查询token即查询源账户的token信息。


::

 HTTP GET /getAccountAssets?address=buQhzVyca8tQhnqKoW5XY1hix2mCt5KTYzcD

返回指定账号的token信息:

+----------------------------------+---------------------------------------------------+
| 参数                             | 描述                                              |
+==================================+===================================================+
| address                          | 账号地址，必填                                    |
+----------------------------------+---------------------------------------------------+
| code                             | issuer表示token发行账户地址，code表示token代码。  |
| issuer                           | 只有同时填写正确code&issuer才能正确显示指定token  |
|                                  | 否则默认显示所有token。                           |
+----------------------------------+---------------------------------------------------+
| type                             | 目前type只能是0，可以不用填写。                   |
+----------------------------------+---------------------------------------------------+


返回内容:

::

 
 {
    "error_code": 0,
    "result": [
        {
            "amount": 469999999997,
            "key": {
                "code": "DT",
                "issuer": "buQhzVyca8tQhnqKoW5XY1hix2mCt5KTYzcD"
            }
        },
        {
            "amount": 1000000000000,
            "key": {
                "code": "ABC",
                "issuer": "buQhzVyca8tQhnqKoW5XY1hix2mCt5KTYzcD"
            }
        }
    ]
 }

如果该账号不存在token,则返回内容:

::

 {
   "error_code" : 0,
   "result" : null
 }

查询指定metadata
^^^^^^^^^^^^^^^^^

::

 HTTP GET /getAccountMetaData?address=buQhzVyca8tQhnqKoW5XY1hix2mCt5KTYzcD&key=asset_property_DT

返回指定账号的MetaData信息:
+----------------------------------+---------------------------------------------------+
| 参数                             | 描述                                              |
+==================================+===================================================+
| address                          | 账号地址，必填。                                  |
+----------------------------------+---------------------------------------------------+
| key                              | 指定metadata中的key值。                           |
+----------------------------------+---------------------------------------------------+

返回内容：

::

 {
    "error_code": 0,
    "result": {
        "asset_property_DT": {
            "key": "asset_property_DT",
            "value": "{\"name\":\"DemonToken\",\"code\":\"DT\",\"totalSupply\":\"1000000000000\",\"decimals\":8,\"description\":\"This is hello Token\",\"icon\":\"iVBORw0KGgoAAAANSUhEUgAAAAE\",\"version\":\"1.0\"}",
            "version": 4
        }
    }
 }

如果该账号指定的key不存在metadata,则返回内容:

::

 {
   "error_code" : 0,
   "result" : null
}