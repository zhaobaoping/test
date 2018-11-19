BUMO ATP Standard
==================

Overview
---------

ATP1.0(Account-based Tokenization Protocol) provides protocol standards for issuing, tranferring and additionally issuing tokens based on BUMO account. 

Purpose
--------

ATP protocol is aimed to provide interfaces for applications to issue, transfer and additionally issue tokens on BUMO.

Attributes of Tokens
---------------------

You can set the attributes of the issued tokens by setting the metadata of the source account of tokens, 
so the applications can manage and check token information conveniently.


+--------------+----------------------------+
| Variables    | Description                |
+==============+============================+
| name         | token name.                |
+--------------+----------------------------+
| code         | token code.                |
+--------------+----------------------------+
| description  | description of tokens.     |
+--------------+----------------------------+
| decimals     | decimal places of tokens.  |
+--------------+----------------------------+
| totalSupply  | total amount of tokens.    |
+--------------+----------------------------+
| icon         | token icon(optional).      |	
+--------------+----------------------------+	
| version      | ATP version                |
+--------------+----------------------------+

.. note:: 

 - code: capitalized spell is recommended.
 - decimals: the number of decimal places which is in the range of 0~8, 0 means no decimal places.
 - totalSupply: the value is in the range of 0~2^63-1. 0 means no upper limit.
 - icon: base64-bit encoding, the file size is less than 32k, 200*200 pixels is recommended.

Operations
-----------

The operations provided in BUMO ATP standards include `registrating tokens`_, `issuing tokens`_, `transferring tokens`_, `additionally issuing tokens`_, `querying tokens`_, and `querying specified metadata`_.


Registrating Tokens
^^^^^^^^^^^^^^^^^^^^

Registrating tokens is to set the metadata of the tokens. You can set the **key**, **value** and  **version** of metadata by sending a  transaction of ``Setting Metadata`` type.
The following is an example of registrating tokens.


**format in json**

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

 The value of **key** must be composed of the prefix **asset_property_** and token code, you can refer to the code parameters when issuing tokens. 
 You can check the result  by querying the specified metadata after you have set the values.

Issuing Tokens
^^^^^^^^^^^^^^

Issuing tokens is to issue an amount of digital tokens, and the balance of the account will go up by the same amount of tokens.
When issuing tokens, the user set the parameters **amount(amount of tokens to be issued)** and **code(token code)** by initiating an transaction of ``Issuing Assets`` type.
The following is an example of issuing 10000 DT tokens with decimals of 8.


**format in json**

::

 {
  "type": 2,
  "issue_asset": {
    "amount": 1000000000000,
    "code": "DT"
  }
 }

Transferring Tokens
^^^^^^^^^^^^^^^^^^^^

Transferring tokens is to transfer an amount of tokens to a destination account.
When transferring tokens, the user set the parameters by initiating an transaction of ``Transferring Assets`` type.
The following table shows the parameters to be set.


+----------------------------------+-----------------------------------------+
| Parameters                       | Description                             |
+==================================+=========================================+
| pay_asset.dest_address           | address of the destination account.     |
+----------------------------------+-----------------------------------------+
| pay_asset.asset.key.issuer       | address of the token issuer.            |
+----------------------------------+-----------------------------------------+
| pay_asset.asset.key.code         | token code.                             |
+----------------------------------+-----------------------------------------+
| pay_asset.asset.amount           | amount of tokens to                     |
|                                  | be transferred * accuracy.              |
+----------------------------------+-----------------------------------------+
| pay_asset.input                  | input parameters for triggering         |
|                                  | the contract, empty string is defaulted.|                          
+----------------------------------+-----------------------------------------+

The following is an example of transferring 500000000000 DT tokens to the destination account buQaHVCwXj9ERtFznDnAuaQgXrwj2J7iViVK.


**json format**

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

After the transfer, the destination account has DT tokens of **amount**. 


.. note:: If the destination account is not activated, the transaction of tranferring tokens will fail.

Additionally Issuing Tokens 
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Additionally issuing tokens is that the account continues to issue a certain amount of tokens on the original token code by setting the same transaction code with the previously issued tokens.  
Applications controls the amount of additionally issued tokens and makes sure it does not exceed **totalSupply**.
There will be an increase in the amount of tokens after additionally issuing tokens.


Querying Tokens
^^^^^^^^^^^^^^^^

Querying tokens is to check the token information of the source account, the following are the parameters you have to specify when querying tokens.

+----------------------------------+----------------------------------------------------------------+
| Parameters                       | Description                                                    |
+==================================+================================================================+
| address                          | account address, required                                      |
+----------------------------------+----------------------------------------------------------------+
| code &                           | **issuer** is the account address which issues the tokens and  |
| issuer                           | **code** is the token code. The specified token can be         |
|                                  | displayed correctly only when the code&issuer are both correct;|
|                                  | otherwise all the tokens will be displayed by default.         |
+----------------------------------+----------------------------------------------------------------+
| type                             | currently **type** can only be 0, you can leave it blank.      |
+----------------------------------+----------------------------------------------------------------+

The following is the code of querying tokens:


::

 HTTP GET /getAccountAssets?address=buQhzVyca8tQhnqKoW5XY1hix2mCt5KTYzcD




If the account has tokens, the following content will be returned:

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

If the account does not have tokens, the following content will be returned:

::

 {
   "error_code" : 0,
   "result" : null
 }

Querying Specified Metadata
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Querying specified metadata is to check the information about **metadata**, including **key**, **value** and **version**.


+----------------------------------+---------------------------------------------------+
| Parameters                       | Description                                       |
+==================================+===================================================+
| address                          | account address, required.                        |
+----------------------------------+---------------------------------------------------+
| key                              | key value of the specified metadata.              |
+----------------------------------+---------------------------------------------------+ 

The following is the code of querying specified metadata:


::

 HTTP GET /getAccountMetaData?address=buQhzVyca8tQhnqKoW5XY1hix2mCt5KTYzcD&key=asset_property_DT


If the specified key has a value, the following content will be returned:

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

If the specified key does not have a value, the following content will be returned:

::

 {
   "error_code" : 0,
   "result" : null
 }