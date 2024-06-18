# yii2-easy-wechat

> 支持 overtrue/wechat 6.x

由于 [jianyan74/yii2-easy-wechat](https://github.com/max-wen/yii2-easy-wechat) 不支持 EasyWechat 6.x 所以建立该项目
 

## 安装 EasyWechat 6.x
```
composer require hfz/yii2-easy-wechat:dev-master
```

## 配置

添加 SDK 到Yii2的 `config/main.php` 的 `component`:

```php

'components' => [
	// ...
	'wechat' => [
		'class' => 'hfz\easywechat\Wechat',
		'userOptions' => [],  // 用户身份类参数
		'sessionParam' => 'wechatUser', // 微信用户信息将存储在会话在这个密钥
		'returnUrlParam' => '_wechatReturnUrl', // returnUrl 存储在会话中
		'rebinds' => [ // 自定义服务模块 
		    // 'cache' => 'common\components\Cache',
		]
	],
	// ...
]
```

设置基础配置信息和微信支付信息到 `config/params.php`:
```php
// 微信配置 具体可参考EasyWechat 
'wechatConfig' => [],

// 微信支付配置 具体可参考EasyWechat
'wechatPaymentConfig' => [],

// 微信小程序配置 具体可参考EasyWechat
'wechatMiniProgramConfig' => [],

// 微信开放平台第三方平台配置 具体可参考EasyWechat
'wechatOpenPlatformConfig' => [],

// 微信企业微信配置 具体可参考EasyWechat
'wechatWorkConfig' => [],

// 微信企业微信开放平台 具体可参考EasyWechat
'wechatOpenWorkConfig' => [],

// 微信小微商户 具体可参考EasyWechat
'wechatMicroMerchantConfig' => [],
```

配置文档

[微信配置说明文档.](https://www.easywechat.com/docs/master/official-account/configuration)  
[微信支付配置说明文档.](https://www.easywechat.com/docs/master/payment/jssdk)  
[微信小程序配置说明文档.](https://www.easywechat.com/docs/master/mini-program/index)  
[微信开放平台第三方平台](https://www.easywechat.com/docs/master/open-platform/index)  
[企业微信](https://www.easywechat.com/docs/master/wework/index)  
[企业微信开放平台](https://www.easywechat.com/docs/master/open-work/index)  
[小微商户](https://www.easywechat.com/docs/master/micro-merchant/index)

## 使用例子


微信网页授权+获取当前用户信息

```php
if (Yii::$app->wechat->isWechat && !Yii::$app->wechat->isAuthorized()) {
    return Yii::$app->wechat->authorizeRequired()->send();
}

// 获取微信当前用户信息方法一
Yii::$app->session->get('wechatUser')

// 获取微信当前用户信息方法二
Yii::$app->wechat->user
```
获取微信SDK实例

```php
$app = Yii::$app->wechat->app;
```
获取微信支付SDK实例

```php
$payment = Yii::$app->wechat->payment;
```
获取微信小程序实例

```php
$miniProgram = Yii::$app->wechat->miniProgram;
```

获取微信开放平台第三方平台实例

```php
$openPlatform = Yii::$app->wechat->openPlatform;
```

获取企业微信实例

```php
$work = Yii::$app->wechat->work;
```

获取微信企业微信开放平台

```php
$work = Yii::$app->wechat->openWork;
```

获取微信小微商户

```php
$microMerchant = Yii::$app->wechat->microMerchant;
```


微信支付(JsApi):

```php
// 支付参数
$orderData = [ 
    'openid' => '.. '
    // ... etc. 
];

// 生成支付配置
$payment = Yii::$app->wechat->payment;
$result = $payment->order->unify($orderData);
if ($result['return_code'] == 'SUCCESS') {
    $prepayId = $result['prepay_id'];
    $config = $payment->jssdk->sdkConfig($prepayId);
} else {
    throw new yii\base\ErrorException('微信支付异常, 请稍后再试');
}  

return $this->render('wxpay', [
    'jssdk' => $payment->jssdk, // $app通过上面的获取实例来获取
    'config' => $config
]);

```

JSSDK发起支付
```
<script src="http://res.wx.qq.com/open/js/jweixin-1.4.0.js" type="text/javascript" charset="utf-8"></script>
<script type="text/javascript" charset="utf-8">
    //数组内为jssdk授权可用的方法，按需添加，详细查看微信jssdk的方法
    wx.config(<?php echo $jssdk->buildConfig(array('chooseWXPay'), true) ?>);
    // 发起支付
    wx.chooseWXPay({
        timestamp: <?= $config['timestamp'] ?>,
        nonceStr: '<?= $config['nonceStr'] ?>',
        package: '<?= $config['package'] ?>',
        signType: '<?= $config['signType'] ?>',
        paySign: '<?= $config['paySign'] ?>', // 支付签名
        success: function (res) {
            // 支付成功后的回调函数
        }
    });
</script>
```

 
 

