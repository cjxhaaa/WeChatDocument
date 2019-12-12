[1.关于文档](#user-content-1-关于文档)<br>
[2.文档术语](#user-content-2-文档术语)<br>
[3.支付账号](#user-content-3-支付账号)<br>
[4.API规则](#user-content-4-API规则)<br>
[5.付款码付款编程指南](#user-content-5-付款码付款编程指南)<br>
[6.本机扫码支付编程指南](#user-content-6-本机扫码支付编程指南)<br>
[7.公众号付款](#user-content-7-公众号付款)<br>
[8.App付款](#user-content-8-app付款)<br>


# 1. 关于文档

> 本文档是微信支付API的官方手册。 因此，它是针对集成微信支付系统的技术架构师，研发工程师，测试工程师和服务工程师的教程，其中包括供应商的系统，例如在线购物平台，收银员系统或自动智能POS系统。 下面的主题旨在按顺序阅读，并且引用了“以前见过的”主题和“尚未出现”的主题。 这些参考文献进行了相应的链接，因此继续阅读其他主题通常不会成为问题。
 
# 2. 文档术语

## 1.付款方式

### 1) 付款码支付(Quick Pay)

> 商家扫描付款人微信“付款码付款”页面上的条形码或二维码，直接付款。此模式适用于离线支付场景。
 
### 2) 扫码支付(Native Pay)
 
> 付款人扫描商户微信支付交易二维码以完成付款。此模式适用于在网站，实体店，媒体广告或其他情况下进行的付款。

### 3) App基于web付款(公众号付款Official Account Payment)
 
> 付款人在其微信上打开商户H5页面，并通过JSAPI接口调用微信支付模块进行交易支付。 该模式适用于以下场景：

* 付款人输入商户的公众号交易付款
* 付款人根据朋友分享商户的付款链接交易付款
* 付款人扫描商户付款二维码交易付款

### 4) App付款(In-App Payment)
 
> 商户的应用通过集成开放SDK来调用微信支付模块进行支付交易。

## 2. 定义

### 1) 微信公众号管理平台

> 微信公众号管理平台是公众号的应用程序录入和管理平台。商户可以使用该平台提交其基本信息，业务数据和财务信息，以启用微信支付。

URL: [http://mp.weixin.qq.com](http://mp.weixin.qq.com)

### 2) 微信开放平台

> 微信开放平台用作商户应用程序访问微信支付开放API的入口点。商家可以使用该平台申请微信App内付款。

URL: [http://open.weixin.qq.com](http://open.weixin.qq.com)

### 3) 微信商户平台

> 微信商户平台充当与微信支付相关的供应商功能的功能中心，包括参数设置，支付数据查询和统计，在线退款，移动优惠券管理以及其他功能。

URL: [http://pay.weixin.qq.com](http://pay.weixin.qq.com)

### 4) 微信支付系统

> 微信支付系统，包括API的后端服务处理系统，帐户系统和微信支付过程回调通知系统。

### 5) 商家销售终端

> 商户销售终端是指收银员通常使用的POS系统，可帮助记录产品数据，创建订单，协助付款人付款并打印交易账单。与微信支付集成时，此系统需要开发和测试POS系统。

### 6) 商家后端系统

> 商户后端系统是供应商后端服务处理系统的通用术语，包括商户的网站，结帐系统，进销存系统，交付系统和客户服务系统。

### 7) 扫描器

> 扫描仪用于帮助商户系统快速读取图像中的编码数据。 根据图像编码的类型，供应商可以使用二维码扫描仪或条形码扫描仪。 就扫描仪类型而言，有红外扫描仪和激光扫描仪。

### 8) 商户证书

> 商户证书是微信提供的二进制文件，当商户的系统启动与微信后端支付服务器的请求会话时，该证书将用作识别商户身份的证书。

### 9) 签名

> 商户的后端和微信支付系统基于相同的密钥和算法创建相同的签名用于验证彼此的身份。签名算法由微信创建和提供。常用的签名模式有MD5，SHA1，SHA256和HMAC。

### 10) OpenID

> OpenID用于将用户的身份共享给公众号。商户的后端在登录授权，付款通知以及调用查询订单API时获取付款人的OpenID。系统可以根据OpenID检查与付款相关的操作是否由同一付款人完成，并发送服务来源和模板化消息给付款人。

# 3. 支付账号

> 商户可以按照指示在微信公众号管理平台（用于本机扫码付款和公众号支付）或微信开放平台（用于App内付款）上申请付款方式。微信支付工作人员收到并审核申请后，将为商家打开相应的支付许可。商户会从微信支付助手收到一封包含所需付款说明的电子邮件，如图3.1所示。

<!--![3.1](https://cdn.firstlinkapp.com/real/i_pic/20191211/36a164ec-ef90-480c-aa71-0a40b6f70068.png)-->
 
Figure 3.1 邮件模板

Table 3.1 邮件参数和api参数关系说明.
 
Table 3.1 账号参数
 
| 邮件参数 | API参数名 | 说明 |
| ------- | -------- | --- |
| APPID | appid | appid是微信公众号管理平台或微信开放平台中每个应用程序的唯一标识密钥，开发人员在这些平台上申请后由微信分配。申请批准电子邮件中也包含此字段。|
| 微信支付商户号 | mch_id | 商户申请微信支付后由微信支付分配的指定商户ID |
| API Key | key | 此密钥是为交易签名而创建的，并保留在商户的后端和微信支付系统中，不应公开或在互联网上使用。 商户应保持此密钥的安全，并避免将其泄露给他人。 商户可以根据电子邮件说明配置密钥。
| Appsecret | secret | AppSecret是与APPID对应的API密码，用于获取用于调用API access_token的证书（access_token）。使用微信支付时，您应该通过OAuth2.0接口获得一个OpenID，并将其用于公众号支付的单个接口中。开发人员应具备在开发模式下获得AppSecret的资格。
 
# 4. API规则

## 1. 协议规则
> 以下是商户访问微信支付时调用的API规则

Table 4.1 API规则

 
| 名称 | 说明 |
| --- | --- |
| 传输模式 | 使用https保证传输安全 |
| 提交方式 | POST |
| 数据格式 | 数据提交返回都是XML格式 |
| 字符编码 | utf-8 |
| 签名算法 | MD5 或 HMAC-SHA256|
| 签名要求 | 请求签名和接收数据需要签名检查。更多信息，参见4.3.1节签名算法 |
| 证书要求 | 调用提交退款API或撤销订单API都需要商户证书 |
| 逻辑判断 | 要确定协议字段，服务字段和事务状态。|
 
## 2. 参数规则
### 1) 支付金额
> 默认情况下，交易的货币为CNY（人民币）。付款金额使用的单位是【分】，必须是整数。但是下载交易记录时，交易金额使用单位【元】。对于国外交易，交易金额将使用最小的货币单位，但参考值必须是不带小数的整数。 例如，使用货币类型“美元”，付款金额中的参考值“1750”将等于17.50美元。
### 2) 货币种类

> 以下为货币种类列表：

* GBP： 英镑
* HKD： 港币
* USD： 美元
* JPY： 日元
* CAD： 加拿大元
* AUD： 澳大利亚元
* EUR： 欧元
* NZD： 新西兰元
* KRW： 韩元
* THB： 泰铢
* SGD： 新加坡元
* RUB： 俄罗斯卢布

> 注意：付款和退款的货币类型必须相同。

### 3) 时间约定

> 本文档中使用中国标准时间（UTC + 08）。如果商户不在该时区，则应将其当前时间转换为中国标准时间。例如，如果某个商户在当地时间2014年11月11日0:00在伦敦，那么它将在2014年11月11日8:00AM在北京。

### 4) 时间戳

> 以中国标准时间（UTC + 08）为标准时区，时间戳以世界标准时间1970/01/01 00:00:00 UTC开始的秒数计算，是在本文档中存储时间戳的必需方法。注意：毫秒应四舍五入为秒（10位数字）。

### 5) 商户订单号

> 付款订单号由商户定义且唯一 我们建议在当前时间的末尾添加一个随机序列，以创建唯一的序列号。重复订单发起付款时应使用原订单号以避免重复交易 但是，已付款，已关闭或已撤销的订单（更多信息，请参见第9节“公共API”）无法再次付款。

### 6) 字段"body"要求

> 调用微信支付API时，字段“body”有一些特殊标准：
 
| 场景 | 支付方式 | 输入标准 | 案例 | 备注 |
| --- | --- | --- | --- | --- |
| PC端网页| 本机扫码支付| 网站标题-产品说明 | Tencent recharge center—QQ member recharging | |
| 微信浏览器 | 公众号支付 | 商品名称-产品类别 | Dangdang-Books | |
| 店家二维码 | 公众号支付 | 商店名称-产品类别 | Southern store-Super market | 线下商店支付 |
| 店家二维码 | 本机扫码支付 | 商店名称-产品类别 | Southern store-Super market | 线下商店支付 |
| 店家扫付款码 | 付款码支付 | 商店名称-产品类别 | Southern store-Super market | 线下商店支付 |
| 第三方移动浏览器 | H5支付 | 移动网站标题-产品说明 | Tencent recharge center—QQ member recharging | |
| 第三方APP | App支付 | App名称-产品说明 | Cool Running-Recharging | |
 
## 3. 付款API的访问节点
> 海外商户的付款API有两个访问节点：
 
 * 东南亚： [https://apihk.mch.weixin.qq.com/](https://apihk.mch.weixin.qq.com/)
 * 欧洲或美国： [https://apius.mch.weixin.qq.com/](https://apius.mch.weixin.qq.com/)
 
> 国内节点 [https://api.mch.weixin.qq.com/](https://api.mch.weixin.qq.com/)
> 注意：商家应根据其服务器部署区域为支付API选择最佳访问节点。 建议考虑其他接入节点的兼容性。 一旦主节点出现问题，系统可以自动切换到其他节点。

## 4. 安全指标
### 1 签名算法

> 创建签名的一般步骤：
 
> 步骤1：假定所有发送和接收的数据都是集合M。以字母升序（即字典顺序）对M中的非空值进行排序，然后通过相应的URL键值格式（例如key1=value1＆key2=value2）将它们连接到字符串A中。
 
注意：
* 根据参数名称的ASCII编码名称（例如字典顺序），按字母升序对它们进行排序；
* 空参数值不包括在签名中；
* 参数名称区分大小写；
* 在检查返回的数据或微信推送通知签名时，由于与创建的签名进行比较，该签名中不包含已传递的签名参数。
 
>步骤2：在字符串A的末尾添加“key=API密钥值”以获取stringSignTemp，对stringSignTemp执行MD5算法，将所有结果字符转换为大写，从而获得sign的值（signValue）。
 
例：
 
有如下参数：
```
appid:         wxd930ea5d5a258f4f
mch_id:      10000100
device_info: 1000
body:           test
nonce_str:   ibuaiVcKdpRxkhJA*

(1)根据格式"key=value"对参数名称的ASCII码按字典序列排序
string A ="appid=wxd930ea5d5a258f4f＆body=test＆device_info=1000＆mch_id=10000100＆nonce_str=ibuaiVcKdpRxkhJA”

(2)加入API keys
stringSignTemp="stringA&key=192006250b4c09247ec02edce69f6a2d"
sign=MD5(stringSignTemp).toUpperCase()="9A0A8659F005D6984697E2CA0A9CF3B7"

得到以下传输数据：
<xml>
    <appid>wxd930ea5d5a258f4f</appid>
    <mch_id>10000100</mch_id>
    <device_info>1000<device_info>
    <body>test</body> 
    <nonce_str>ibuaiVcKdpRxkhJA</nonce_str> 
    <sign>9A0A8659F005D6984697E2CA0A9CF3B7</sign>
<xml>
微信为此API提供了在线签名工具：URL1。
```

### 2 随机字符串算法
> 为了确保签名的不可预测性，微信支付API协议包含了nonce_str。我们建议调用random()函数来创建签名并将其值转换为字符串。

### 3 商户证书
#### 1) 获取商户证书
 
> 与付款回滚（例如退款或撤销的订单）相关的API需要商户的证书。商户成功申请微信支付后，会通过邮件通知的方式发放。可能需要四个证书，如下所示：
 
Table 4.2: Certificate Description
 
| 证书附件 | 说明 | 使用案例 | 备注 |
| --- | --- | --- | --- |
| pkcs12格式(apiclient_cert.p12)| 包含p12(pfx)格式的私钥信息证书，并由微信支付签发以进行身份验证 | 调用撤销订单API和提交退款API | 双击以导入Windows系统，并根据提示输入证书密码。 默认情况下，证书密码是供应商的ID（例如10010000）|
| pem格式证书(apiclient_cert.pem) | 可以导入apiclient_cert.p12证书文件来创建pem格式的证书。不要向他人透露哦~ | pem格式应用于PHP应用程序，因为PHP不能使用p12格式 | 您还可以使用“ openssl”命令来导入p12格式的证书，如下所示：openssl pkcs12 -clcerts-nokeys -in apiclient_cert.p12-out apiclient_cert.pem |
| pem格式密钥(apiclient_key.pem) | 可以导入apiclient_cert.p12证书文件来创建pem格式的证书 | pem格式应用于PHP应用程序，因为PHP不能使用p12格式 | 您还可以使用“ openssl”命令来导入p12格式的证书，如下所示：openssl pkcs12 -nocerts -in apiclient_cert.p12 -out apiclient_key.pem|
| CA 证书 (rootca.pem) | 微信支付API服务器还部署了服务器证书以验证微信支付的身份。商户调用API时，应验证被调用服务器的真实性和域名。| 该文件是签署微信支付证书的机构颁发的根证书，可用于验证微信支付服务器证书的真实性。| 根证书是某些工具内置的。对于没有根证书的工具，可以使用此处提供的工具。|
 
#### 2) 使用商户证书
* apiclient_cert.p12是除基于PHP的开发以外的所有R＆D操作的供应商证书文件。
* 使用.NET环境的商户应确保其框架版本大于2.0。他们可以在使用之前双击安装证书"apiclient_cert.p12"。
* 调用商户证书和安装的默认密码是商户ID(mch_id)。
* 基于PHP的开发需要"apiclient_cert.pem"和"apiclient_key.pem"，并且rootca.pem是CA证书。
 
有关更多调用示例，请参阅微信支付提供的[演示出站链接](https://pay.weixin.qq.com/wiki/doc/api/download/cert.zip)

#### 3) 商户证书安全
> 证书文件不应存储在Web服务器上的虚拟目录中。相反，应将它们放置在具有严格访问控制权限的目录中，以避免他人下载证书。供应商的服务器也应该没有病毒和木马，以免证书被盗。
 
4. 商户回调API安全
> 大部分网络环境中，HTTP请求承担这DNS欺骗，非法弹出以及数据窃取的风险。供应商的回调API应该使用HTTPS以确保数据传输的安全性。 因此，我们建议所有供应商将HTTPS用于所有微信支付回调。 有关更多信息，请参见[HTTPS构建指南](https://pay.weixin.qq.com/wiki/doc/api/micropay.php?chapter=10_4)
 
### 4 获取OpenID

微信公众号管理平台：
 
用户关注商户公众号后，公众号可以获取关注者的OpenID。每个用户的微信ID加密后都有一个唯一的OpenID。用户的OpenID因不同的公众号而异。
 
公众号可以通过下面的API获取用户的OpenID。有关用户别名，照片图片，性别，位置，语言和后续时间的信息，需要用户授权。
 
网址： [http://admin.wechat.com/wiki/index.php?title=User_Profile_via_Web](http://admin.wechat.com/wiki/index.php?title=User_Profile_via_Web)
 
要将来自不同平台的OpenID用作同一用户的一个ID，开发人员可以使用以下API：
 
网址： [https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140839](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140839)
 
微信开放平台：
 
移动应用可以使用下面的API来获取用户的OpenID： [https://open.weixin.qq.com/cgibin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317851&token=&lang=zh_CN](https://open.weixin.qq.com/cgibin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317851&token=&lang=zh_CN)
 
网站应用可以使用下面的API来获取用户OpenID： [https://open.weixin.qq.com/cgibin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419316505&token=&lang=zh_CN](https://open.weixin.qq.com/cgibin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419316505&token=&lang=zh_CN)

# 5. 付款码付款编程指南
## 1. 使用案例
> 步骤1：登录微信后，付款人在“我”->“钱包”中进入"付款码支付"，如图5.1
>
> 步骤2：收银员创建交易订单，付款人确认在销售点终端上显示的付款金额；
> 
> 步骤3：收银员扫描付款人出示的条码或二维码到销售终端点，这样交易订单会被微信支付服务提交给交易系统。
>
> 步骤4：交易系统收到付款请求后，交易系统先确认是否需要验证付款人的支付密码。如果不需要支付密码，会直接付款。反之，会被提示输入付款密码，如图5.2所示。如果支付成功，付款人将在微信中看到'支付成功'的消息如图5.3所示；如果付款失败，会显示付款错误页面

<img title="5.1" src="https://cdn.firstlinkapp.com/real/i_pic/20191212/c4890c60-d034-4679-a24b-a9bc47ae9cee.png" width="30%">
<img title="5.2" src="https://cdn.firstlinkapp.com/real/i_pic/20191212/2fd20aea-d063-4e27-b362-9c0c547311e3.png" width="30%">
<img title="5.3" src="https://cdn.firstlinkapp.com/real/i_pic/20191212/2a8b2c1b-e8ea-4844-9f28-eaf3920fa9f9.png" width="30%">

## 2. 付款验证码规则
* 当交易总额超过1000人民币时必须验证付款人密码
* 对于低于1000元的交易，每个微信账号允许5次免密交易，超过限制需要密码验证
* 对于任何可信或者可疑的交易都需要验证密码

## 3. 正在参与的商户
> 用户可以在支持微信支付的商店和商店中亲自体验这种支付方式。便利店：7-11，Guoda36524，Hi-24等连锁药店：LBX药房，国大药店，海王星药店等。百货公司：彩虹等。
 
## 4. 商户流程
> 基于商户环境， 商户可以通过后端访问模式或者实体店直接访问模式来处理付款。付款方案包括了密码支付和免密支付。
 
### 1 访问模式-商户后端访问
 
此模式适用于配备了统一后端的供应商。在这种模式下，收银员首先与商户自己的后端进行通信，然后后端向微信支付系统发送交易请求并从微信支付系统接收结果。
 
![5.4](https://cdn.firstlinkapp.com/real/i_pic/20191212/2246dc5b-510f-40c9-afc1-9c6864222fda.png)

### 2 访问模式-实体店直接访问
 
此模式适用于通过公共网络与微信支付系统进行通信的商户。在这种模式下，收银员发起交易请求直接通过微信支付系统处理返回的结果。但是，商户可以根据其要求处理其物理商店和后端之间的其他交易。
 
![5.5](https://cdn.firstlinkapp.com/real/i_pic/20191212/1213df5a-8823-4304-8c2a-4156228c0f06.png)

### 3 免密支付流程
 
本部分使用商户后端访问模式来说明支付流程，如下流程图所示。
 
![5.6](https://cdn.firstlinkapp.com/real/i_pic/20191212/55904e2c-b531-42ba-84ce-f5fd67d7e6df.png)

免密支付步骤：
> 1. 收银员在其销售终端上创建付款订单，展示付款金额给付款人。
>
> 2. 付款人打开微信进入微信钱包
> 
> 3. 收银员扫描付款人屏幕上的的条形码
> 
> 4. 扫描器读取转发条码数据到收营员的销售终端
> 
> 5. 收到支付信息后，收营员向商户后端发起付款请求
> 
> 6. 商户后端处理实体店销售终端的付款请求，创建签名，然后调用【Submit Quick Pay API】向微信付款系统发起付款请求
> 
> 7. 微信付款系统收到商户支付请求，验证后处理数据，并将支付结果返回给商户后端。 如果交易成功支付， 微信付款系统会给商户发送付款结果, 同时付款人也会收到短信或微信消息。
> 
> 8. 商户后台验证签名处理相关数据，向收银员销售终端发送付款结果
> 
> 9. 确认付款成功后收营员将商品交付给付款人

### 4 密码支付流程
> 密码支付流程和免密支付流程很相似，第1到5步是相同的。 在密码支付流程中，在商户后台调用【Submit Quick Pay API】发起付款请求后， 微信后台会提示付款人输入支付密码。
> 付款人成功验证支付密码后，API会立刻返回USERPAYING状态给商户后端，并且商户后台会与微信付款系统【Query Order API】通信来确认订单是否已经支付成功。
 
密码支付流程如下图所示：
 
![5.7](https://cdn.firstlinkapp.com/real/i_pic/20191212/07e4973f-e95a-41f4-9437-42c55c7b789c.png)

这里只说明一下与先前不同的步骤：
> 1.收银员的销售终端在创建订单后向商户的后端发起付款请求；
>
> 2. 商户后端调用'Submit Quick Pay API'来创建付款交易
> 
> 3. 微信支付系统验证商户的请求并确定是否需要验证密码
> 
> 4. 微信支付系统返回USERPAYING状态，而供应商的后端将包含结果的响应发送到收银员的销售终端
>
> 5. 微信支付系统提示付款人在微信上输入支付密码
> 
> 6. 付款人确认支付并输入支付密码
> 
> 7. 付款人输入密码后提交验证
> 
> 8. 付款人在微信上完成交易后微信支付后端返回支付结果并通过短信或微信通知付款人结果
> 
> 9. 供应商的后端从微信支付系统接收USERPAYING状态，并通过【Query Order API】查询实际支付结果（更多信息，请参见公共API）。
> 
> 10. 如果微信支付系统返回的是USERPAYING状态状态，商户后台应该每隔5秒调用【Query Order API】来确认最终付款状态。如果付款人取消付款或延迟付款超过30秒，
>商户后台应暂停轮询过程，并滴啊用【Revoke Order API】取消交易。

### 5 异常处理
 
请按照以下说明来排除付款异常:
> 1. 当付款人微信上提示付款错误时，如果付款人在交易历史中找不到他们的订单，他们可以要求商户再次生成订单。只要订单已经成功付款，那么商户后台再次调
>用【Query Order API】可以查询到订单实际付款状态。
> 
> 2. 如果被提示余额不足或者卡失效等付款错误，付款人就需要重新支付。
> 
> 3. 无论是交易超时或者付款失败，商户后台都应该调用【Revoke Order API】来取消交易
>
> 4. 由异常银行系统错误，余额不足，银行不受支持或任何其他原因导致的错误消息， 商户后端也应该将其发送到收银员的销售终端。
>
> 5. 依据错误码的返回类型，交易可能会被取消。详情见API返回错误码列表

## 5. 提交Quick Pay API

pass

 
## 6. 请求订单 API
> 更多信息见【Section 9.2 Query Order】

## 7. 提交退款 API
> 更多信息见【Section 9.4 Submit Refund】

## 8. 请求退款 API
> 更多信息见【Section 9.5 Query Refund】

## 9. 撤销订单API
pass

## 10. 下载对账文件API
> 更多信息见【Section 9.6 Download Reconciliation File】

## 11. 报告速度测试

> 为了改善我们的付款服务，我们建议供应商通过Report Speed Testing API向WeChat付款后端报告付款界面延迟。 有关更多信息，请参见【Section 9.8 Report Speed Testing API】.。
 
# 6. 本机扫码支付编程指南

## 1. 使用案例
pass

# 7. 公众号付款
1. 使用案例
pass

<h1 id="8" >8. App付款</h1>

## 1. 使用案例