## 1. 关于文档

> 本文档是微信支付API的官方手册。 因此，它是针对集成微信支付系统的技术架构师，研发工程师，测试工程师和服务工程师的教程，其中包括供应商的系统，例如在线购物平台，收银员系统或自动智能POS系统。 下面的主题旨在按顺序阅读，并且引用了“以前见过的”主题和“尚未出现”的主题。 这些参考文献进行了相应的链接，因此继续阅读其他主题通常不会成为问题。
 
## 2. 文档术语

### 1.支付方式

##### 1) 快速支付

> 商家扫描付款人微信“快速付款”页面上的条形码或二维码，直接付款。此模式适用于离线支付场景。
 
##### 2) 本机支付
 
> 付款人扫描商户微信支付交易二维码以完成付款。此模式适用于在网站，实体店，媒体广告或其他情况下进行的付款。

##### 3) 应用内基于web支付(正式账户支付方式)
 
> 付款人在其微信上打开商户H5页面，并通过JSAPI接口调用微信支付模块进行交易支付。 该模式适用于以下场景：

* 付款人输入商户的官方帐户交易付款
* 付款人根据朋友分享商户的付款链接交易付款
* 付款人扫描商户付款二维码交易付款

##### 4) 应用内付款
 
> 商户的应用通过集成开放SDK来调用微信支付模块进行支付交易。

### 2. 定义

##### 1) 微信公众号管理平台

> 微信公众号管理平台是官方帐号的应用程序录入和管理平台。商户可以使用该平台提交其基本信息，业务数据和财务信息，以启用微信支付。

URL: [http://mp.weixin.qq.com](http://mp.weixin.qq.com)

##### 2) 微信开放平台

> 微信开放平台用作商户应用程序访问微信支付开放API的入口点。商家可以使用该平台申请微信应用内付款。

URL: [http://open.weixin.qq.com](http://open.weixin.qq.com)

##### 3) 微信商户平台

> 微信商户平台充当与微信支付相关的供应商功能的功能中心，包括参数设置，支付数据查询和统计，在线退款，移动优惠券管理以及其他功能。

URL: [http://pay.weixin.qq.com](http://pay.weixin.qq.com)

##### 4) 微信支付系统

> 微信支付系统，包括API的后端服务处理系统，帐户系统和微信支付过程回调通知系统。

##### 5) 商家销售终端

> 商户销售终端是指收银员通常使用的POS系统，可帮助记录产品数据，创建订单，协助付款人付款并打印交易账单。与微信支付集成时，此系统需要开发和测试POS系统。

##### 6) 商家后端系统

> 商户后端系统是供应商后端服务处理系统的通用术语，包括商户的网站，结帐系统，进销存系统，交付系统和客户服务系统。

##### 7) 扫描器

> 扫描仪用于帮助商户系统快速读取图像中的编码数据。 根据图像编码的类型，供应商可以使用二维码扫描仪或条形码扫描仪。 就扫描仪类型而言，有红外扫描仪和激光扫描仪。

##### 8) 商户证书

> 商户证书是微信提供的二进制文件，当商户的系统启动与微信后端支付服务器的请求会话时，该证书将用作识别商户身份的证书。

##### 9) 签名

> 商户的后端和微信支付系统基于相同的密钥和算法创建相同的签名用于验证彼此的身份。签名算法由微信创建和提供。常用的签名模式有MD5，SHA1，SHA256和HMAC。

##### 10) OpenID

> OpenID用于将用户的身份共享到正式帐户，与正式帐户有区别。商户的后端在登录授权，付款通知以及调用查询订单API时获取付款人的OpenID。系统可以根据OpenID检查与付款相关的操作是否由同一付款人完成，并发送服务来源和模板化消息给付款人。

### 3. 支付账号

> 商户可以按照指示在微信官方帐户管理平台（用于本机快速付款和官方帐户付款）或微信开放平台（用于应用内付款）上申请付款方式。微信支付工作人员收到并审核申请后，将为商家打开相应的支付许可。商户会从微信支付助手收到一封包含所需付款说明的电子邮件，如图3.1所示。

![3.1](https://cdn.firstlinkapp.com/real/i_pic/20191211/36a164ec-ef90-480c-aa71-0a40b6f70068.png)
