Installation Guide for BUMO
===========================

::

   // 初始化请求参数
   Long blockNumber = 617247L;// 第617247区块
   BlockGetTransactionsRequest request = new BlockGetTransactionsRequest();
   request.setBlockNumber(blockNumber);

   // 调用getTransactions接口
   BlockGetTransactionsResponse response = sdk.getBlockService().getTransactions(request);
   if(0 == response.getErrorCode()){
   System.out.println(JSON.toJSONString(response.getResult(), true));
   }else{
   System.out.println("error: " + response.getErrorDesc());
   }





.. code:: javascript

//初始化请求参数
   Long blockNumber = 617247L;// 第617247区块
   BlockGetTransactionsRequest request = new BlockGetTransactionsRequest();
   request.setBlockNumber(blockNumber);

   // 调用getTransactions接口
   BlockGetTransactionsResponse response = sdk.getBlockService().getTransactions(request);
   if(0 == response.getErrorCode()){
   System.out.println(JSON.toJSONString(response.getResult(), true));
   }else{
   System.out.println("error: " + response.getErrorDesc());
   }