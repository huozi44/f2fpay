当面付是支付宝的一款支付产品，支持二维码和条码支付,本项目是为支付宝当面付适配的扩展。   
主要逻辑：   
生成订单插入数据库-->显示二维码供用户支付-->支付完成后支付宝异步通知到notify.php-->notify.php对支付宝传来的数据进行校验，改变数据库中订单状态-->query.php返回订单成功状态-->完成支付
生成订单-->显示二维码-->直接根据支付宝的接口查询订单状态-->成功   

## 一．程序功能说明：   
在支付宝标准SDK的基础上再做了一层封装， 即PHP版本demo中f2fpay文件夹(集成了支付轮询撤销逻辑)   
   
## 二．运行环境要求：   
	PHP5.5以上   
   
## 三．封装代码结构   
D:.   
│  AopSdk.php   
│  config.php   
│  index.html   
│  indexdy.html   
│  notify.php   
│  pay.php   
│  query.php   
│   
├─f2fpay   
│  ├─model   
│  │  ├─builder   
│  │  │      AlipayTradeCancelContentBuilder.php   
│  │  │      AlipayTradePrecreateContentBuilder.php   
│  │  │      AlipayTradePrecreateRequest.php   
│  │  │      AlipayTradeQueryContentBuilder.php   
│  │  │      AlipayTradeQueryRequest.php   
│  │  │      AopClient.php   
│  │  │      ContentBuilder.php   
│  │  │      ExtendParams.php   
│  │  │      GoodsDetail.php   
│  │  │      RoyaltyDetailInfo.php   
│  │  │      SignData.php   
│  │  │   
│  │  └─result   
│  │          AlipayF2FPayResult.php   
│  │          AlipayF2FPrecreateResult.php   
│  │          AlipayF2FQueryResult.php   
│  │   
│  └─service    
│          AlipayTradeService.php   
│   
├─pay   
│      base64.js   
│      index.php   
│      md5.js   
│      md5_encode.html   
│      qrcode.class.php   
│      qrcode.php   
│   
└─static   
        avatar.jpg   
        erphpdown.css   
        payment-alipay.png   
        signin.css   
   
## 四．接口方法   
AlipayTradeService.php文件中相应方法：   
	// 当面付2.0条码支付(带轮询逻辑)   
	public function barPay($req)   
   
// 当面付2.0消费查询   
	public function queryTradeResult($outTradeNo)   
   
// 当面付2.0消费退款   
	public function refund($req)    
   
//当面付2.0预下单(生成二维码,带轮询)   
	public function qrPay($req)   
   
## **五、使用方法：**   
**拿到源码后，傻瓜式配置就可以拥有属于自己的支付接口。**    
此源码分必须修改和可选修改两部分。完成必须修改部分即可满足你自己的支付接口来进行收款了。可选修改部分为有更多需要的商家提供功能扩展。   
   
### **1、必须修改部分：**   
本源码仅需修改一个文件中的三个参数即可。   
需要修改的文件是：**armpay/config.php**   
***三个参数是：   
第一个：AppID（也叫应用ID）   
第二个：支付宝公钥（公钥分为 应用公钥 和 支付宝公钥，我们要用到的是支付宝公钥）   
第三个：私钥（或者叫商户私钥）   
如下，把他们分别填入下面的""内即可。***       
```
  //支付宝公钥   
  'alipay_public_key' => " ",   
   
  //商户私钥   
  'merchant_private_key' => " ",   
   
  //应用ID   
  'app_id' => " ",
```
### **2、可选修改部分：**   
这里只介绍支付成功后的跳转功能，因为这个比较简单，且能满足大部分人的需求了。   
支付成功后，可以根据需要设置跳转到指定网页或更新页面状态。   
修改 armpay/pay/index.php中的 location.href = " "，如下，我这里跳转到 https://baidu.com   
 ```php
        setOrder = setInterval(function() {   
            $.ajax({
                type: 'post',
                url: '../query.php',
                data: {
                    no: '<?php echo $no; ?>',
                    t: Math.random()
                },
                dataType: 'json',
                success: function(data) {
                    if (data.status.toLowerCase() == "success") {
                        alert("支付成功!");
                        location.href = "https://baidu.com";
                    }

                },
                error: function(XMLHttpRequest, textStatus, errorThrown) {
                    alert(errorThrown);
                }
            });

        }, 5000);
``` 

商品信息，价格等，根据需要自行修改form表各属性对应的值即可：
``` html
    <form class="form-signin" method="post" action="./pay.php">
        <img class="mb-4" src="./static/avatar.jpg" alt="" width="120" height="120">
        <h1 class="h3 mb-3 font-weight-normal">支付测试</h1>
        <label for="money" class="sr-only">付款金额</label>
        <input type="text" id="money" class="form-control" name="amount" placeholder="付款金额" value="0.01" autofocus="">
        <label for="reason" class="sr-only">付款理由</label>
        <input type="text" id="reason" class="form-control" name="body" placeholder="付款理由" required="" maxlength="15">
        <button class="btn btn-lg btn-primary btn-block" type="submit">马上付款</button>
     </form>
```
[支付效果测试](http://armpay.cn/armpay/index.html)   
[视频教程](http://armpay.cn/vedio1.html)   
[了解更多](http://armpay.cn)   
[源码下载](http://armpay.cn/assets/images/armPay%E6%BA%90%E7%A0%81.zip)   
