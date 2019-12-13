[1.关于文档](#user-content-1-关于文档)<br>
[2.文档术语](#user-content-2-文档术语)<br>
[3.支付账号](#user-content-3-支付账号)<br>
[4.API规则](#user-content-4-api规则)<br>
[5.快速付款编程指南(不全)](#user-content-5-快速付款编程指南)<br>
[6.本机扫码支付编程指南(无)](#user-content-6-本机扫码支付编程指南)<br>
[7.公众号付款(无)](#user-content-7-公众号付款)<br>
[8.App付款](#user-content-8-app付款)<br>
[8.公共API](#user-content-9-公共api)<br>


# 1. 关于文档

> 本文档是微信支付API的官方手册。 因此，它是针对集成微信支付系统的技术架构师，研发工程师，测试工程师和服务工程师的教程，其中包括供应商的系统，例如在线购物平台，收银员系统或自动智能POS系统。 下面的主题旨在按顺序阅读，并且引用了“以前见过的”主题和“尚未出现”的主题。 这些参考文献进行了相应的链接，因此继续阅读其他主题通常不会成为问题。
 
# 2. 文档术语

## 1.付款方式

### 1) 快速支付(Quick Pay)

> 商家扫描付款人微信"付款码付款"页面上的条形码或二维码，直接付款。此模式适用于离线支付场景。
 
### 2) 扫码支付(Native Pay)
 
> 付款人扫描商户微信支付交易二维码以完成付款。此模式适用于在网站，实体店，媒体广告或其他情况下进行的付款。

### 3) App基于web付款(公众号付款Official Account Payment)
 
> 付款人在其微信上打开商户H5页面，并通过JSAPI接口调用微信支付模块进行交易支付。 该模式适用于以下场景：

* 在交易页面上付款人输入商户的公众号付款
* 付款人根据朋友分享商户的付款链接付款
* 付款人扫描商户付款二维码付款

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
 
# 4. API规范

## 1. 协议规范
> 以下是商户访问微信支付时调用的API规则

Table 4.1 API规范

 
| 名称 | 说明 |
| --- | --- |
| 传输模式 | 使用https保证传输安全 |
| 提交方式 | POST |
| 数据格式 | 数据提交返回都是XML格式 |
| 字符编码 | utf-8 |
| 签名算法 | MD5 或 HMAC-SHA256|
| 签名要求 | 请求签名和接收数据需要签名检查。更多信息，参见4.3.1节签名算法 |
| 证书要求 | 调用提交退款API或撤销订单API都需要商户证书 |
| 逻辑判断 | 要确定协议字段，服务字段和交易状态。|
 
## 2. 参数规范
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

### 3) 时间协议

> 本文档中使用中国标准时间（UTC+08）。如果商户不在该时区，则应将其当前时间转换为中国标准时间。例如，如果某个商户在当地时间2014年11月11日0:00在伦敦，那么它将在2014年11月11日8:00AM在北京。

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
| 店家扫付款码 | 快速支付 | 商店名称-产品类别 | Southern store-Super market | 线下商店支付 |
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
 
| 证书附件 | 说明 | 用例 | 备注 |
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
 
#### 4) 商户回调API安全
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

# 5. 快速付款编程指南
## 1. 用例
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

## 3. 商户案例
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
> 6. 商户后端处理实体店销售终端的付款请求，创建签名，然后调用【提交快速付款 API】向微信付款系统发起付款请求
> 
> 7. 微信付款系统收到商户支付请求，验证后处理数据，并将支付结果返回给商户后端。 如果交易成功支付， 微信付款系统会给商户发送付款结果, 同时付款人也会收到短信或微信消息。
> 
> 8. 商户后台验证签名处理相关数据，向收银员销售终端发送付款结果
> 
> 9. 确认付款成功后收营员将商品交付给付款人

### 4 密码支付流程
> 密码支付流程和免密支付流程很相似，第1到5步是相同的。 在密码支付流程中，在商户后台调用【提交快速付款 API】发起付款请求后， 微信后台会提示付款人输入支付密码。
> 付款人成功验证支付密码后，API会立刻返回USERPAYING状态给商户后端，并且商户后台会与微信付款系统【查询下单 API】通信来确认订单是否已经支付成功。
 
密码支付流程如下图所示：
 
![5.7](https://cdn.firstlinkapp.com/real/i_pic/20191212/07e4973f-e95a-41f4-9437-42c55c7b789c.png)

这里只说明一下与先前不同的步骤：
> 1.收银员的销售终端在创建订单后向商户的后端发起付款请求；
>
> 2. 商户后端调用【提交快速付款 API】来创建付款交易
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
> 9. 供应商的后端从微信支付系统接收USERPAYING状态，并通过【查询下单 API】查询实际支付结果（更多信息，请参见公共API）。
> 
> 10. 如果微信支付系统返回的是USERPAYING状态状态，商户后台应该每隔5秒调用【查询下单 API】来确认最终付款状态。如果付款人取消付款或延迟付款超过30秒，
>商户后台应暂停轮询过程，并调用【取消下单 API】取消交易。

### 5 异常处理
 
请按照以下说明来排除付款异常:
> 1. 当付款人微信上提示付款错误时，如果付款人在交易历史中找不到他们的订单，他们可以要求商户再次生成订单。只要订单已经成功付款，那么商户后台再次调
>用【查询下单 API】可以查询到订单实际付款状态。
> 
> 2. 如果被提示余额不足或者卡失效等付款错误，付款人就需要重新支付。
> 
> 3. 无论是交易超时或者付款失败，商户后台都应该调用【取消下单 API】来取消交易
>
> 4. 由异常银行系统错误，余额不足，银行不受支持或任何其他原因导致的错误消息， 商户后端也应该将其发送到收银员的销售终端。
>
> 5. 依据错误码的返回类型，交易可能会被取消。详情见API返回错误码列表

## 5. 提交快速付款 API

pass

 
## 6. 查询下单 API
> 更多信息见【9.2 查询下单】

## 7. 提交退款 API
> 更多信息见【9.4 提交退款】

## 8. 查询退款 API
> 更多信息见【9.5 查询退款】

## 9. 取消下单API
pass

## 10. 下载对账文件API
> 更多信息见【9.6 下载对账文件】

## 11. 报告速度测试

> 为了改善我们的付款服务，我们建议供应商通过【报告速度测试 API】向WeChat付款后端报告付款界面延迟。 有关更多信息，请参见【9.8 报告速度测试 API】.。
 
# 6. 本机扫码支付编程指南

## 1. 用例
pass

# 7. 公众号付款
##1. 用例
pass

# 8. App付款

## 1. 用例
> 此方法适用于商户将其集成到移动App中的微信支付。
>
> 商户的App调用微信提供的SDK来使用微信支付模块，然后重定向到微信以支付交易。完成交易后，微信会重新打开商户的App，并显示包含付款结果的页面。
>
> 目前，微信支持iOS，Android和Windows Phone。
>
> 详细步骤如下：
>
> 步骤1：付款人进入供应商的App，选择产品并确认交易以继续付款。商户的服务后端创建一个付款订单并对其进行签名，然后相关数据被传输到商户的App，如图8.1所示。
>
> 步骤2：付款人单击确认付款，并在微信中打开付款页面来支付订单，如图8.2所示。
>
> 步骤3：付款人确认收款人和金额，然后单击付款。然后显示一个页面，提示付款人输入他们的付款密码。付款人可以选择使用银行卡或通过余额付款，如图8.3所示。
 
<img title="8.1" src="https://cdn.firstlinkapp.com/real/i_pic/20191212/d13f7983-447d-433d-a737-7846eaec539c.png" width="30%">
<img title="8.2" src="https://cdn.firstlinkapp.com/real/i_pic/20191212/e43bdc99-6a8a-42db-945c-f8e9c041f915.png" width="30%">
<img title="8.3" src="https://cdn.firstlinkapp.com/real/i_pic/20191212/a10c4487-55a5-4c8a-aaec-2b4f4ef64ca2.png" width="30%">

> 步骤4：付款人输入其付款密码完成交易。如果支付成功，则在支付者的微信中会显示一个包含支付结果的页面，如图8.4所示。
>
> 步骤5：页面重新打开供应商的应用程序，该应用程序将根据付款结果显示订单处理结果。

<img title="8.4" src="https://cdn.firstlinkapp.com/real/i_pic/20191212/c8af37dd-5bad-4263-9a2a-ad9e2e96e164.png" width="30%">
<img title="8.5" src="https://cdn.firstlinkapp.com/real/i_pic/20191212/521ab94a-94c7-44a1-8838-64f0a69aef6c.png" width="30%">

## 2.商户案例
> 京东和易迅App现在支持此付款方式。

## 3.服务流程
> 下面说明了这种付款方式。 统一下单API，查询订单API和接受订单通知需要签名，这些签名是在供应商的服务后端创建的，如图8.6所示。
 
![8.6](https://cdn.firstlinkapp.com/real/i_pic/20191212/b6d2d341-68a8-4f50-b3d4-5302d057529f.png)

> 下面显示了商户后端如何与微信支付系统进行交互：
>
>步骤1：付款人在商户的App中选择产品，提交订单，然后选择微信支付。
>
>步骤2：商户收到付款人的付款交易，并调用统一下单API。有关更多信息，请参见第9.1节【Unified Order】。
>
>步骤3：统一下单 API返回正常的prepay_id，并根据签名规则。相关数据将传输到商户App。签名中包含的字段包括cappId，partnerId，prepayId，nonceStr，timeStamp和package。
 
**注意：字段格式为Sign = WXPay**

>步骤4：商户App使用SDK在微信中打开微信支付。有关更多信息，请参见第8.5节【App-based Development Guide】。
>
>步骤5：商户后端接收付款通知。有关更多信息，请参见第9.7节【General Payment Result Notification】。
>
>步骤6：商户后端查询付款结果。有关更多信息，请参见第9.2节【Query Order】

## 4. 了解更多关于此API

[https://open.weixin.qq.com/zh_CN/htmledition/res/dev/document/sdk/ios/index.html](https://open.weixin.qq.com/zh_CN/htmledition/res/dev/document/sdk/ios/index.html)

## 5. 从APP调用付款API
> 关于App开发流程，请参考8.6节

从APP调用付款API
 
| 字段名 | ID | 必要性 | 类型 | 例子 | 说明 |
| --- | --- | --- | --- | --- | --- |
| App应用程序ID | appid | 是 | String(32) | wx8888888888888888 | 商户在微信开放平台上注册了自己的APP后，将发放此ID。|
| 商户ID | partnerid | 是 | String(32)| 1900000109 | 微信支付分配的对应商户ID |
| 预付交易ID | prepayid | 是 | String(32) | WX1217752501201407033233368018 | 统一下单API返回的对应参数值(prepay_id)|
| 订单扩充字符串 | package | 是 | String(128) | Sign=WXPay | 对应静态值Sign=WXPay | 
| 随机字符串 | noncestr | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节 随机字符串算法 |
| 时间戳 | timestamp | 是 | String(10) | 1414561699 | 对应当前时间。更多信息见4.2节 参数规范 |
| 签名 | sign | 是 | String(32) | C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
 
返回的数据：
 
| 返回值 | 说明 | 解答 |
| --- | --- | --- |
| 0 | 支付成功 | 展示付款成功页面 |
| -1 | 支付错误 | 可能原因：sign错误， 未注册appid, 不正确的项目appid, 已注册的appid未匹配等等 |
| -2 | 支付取消 | 不需要解决，用户放弃付款等原因 |

## 6. 基于App的开发指南
 
### 1 iOS介绍
> 我们将用Xcode10.0运行iOS7.0环境为例来说明该过程。

#### 1）APPID项目设置

> 商户在微信开放平台上成功申请应用后，该平台将向商户提供唯一的APPID。 使用Xcode创建项目时，开发人员应在"URL Schemes"字段中输入APPID值，如图8.7中红色标记所示。

![8.7](https://cdn.firstlinkapp.com/real/i_pic/20191212/e458e271-66a8-465c-9a09-d0b9f5491619.png)

#### 2）APPID注册

> 微信SDK的"lib"和"head"文件应导入到Xcode项目。 在调用API之前，您应该在微信上注册APPID，如下所示：
```text
[WXApi registerApp:@"wxd930ea5d5a258f4f" withDescription:@"demo 2.0"];
```

#### 3）调用支付

> 商户的服务器调用Unified Order API（有关更多信息，请参见第9.1节"统一下单"）来创建预付款交易。在获得prepay_id并加签相关参数后，将预付款交易数据传输到App以开始付款。 请参阅以下示例，了解如何执行此操作：
```text
PayReq *request = [[[PayReq alloc] init] autorelease]; 
request.partnerId = @"10000100";
request.prepayId= @"1101000000140415649af9fc314aa427"; 
request.package = @"Sign=WXPay";
request.nonceStr= @"a462b76e7436e98e0ed6e13c64b4fd1c"; 
request.timeStamp= @"1397527777";
request.sign= @"582282d72dd2b03ad892830965f428cb16e7a256"; 
[WXApi safeSendReq:request];
```

#### 4）支付结果回调
todo

# 9. 公共API
> 公共API就是被调用以访问各种补充功能的通用API接口。商户可以根据需要选择集成这些功能。公共API包括统一下单，一般通知，查询订单，关闭订单，提交退款，退款查询，下载对帐文件和短URL转换功能，这些功能将在以下小节中详细介绍。

## 1. 统一下单

### 1 用例
> 对于除"快速付款"方法以外的方案，供应商后端调用此API在微信支付服务后端创建预付款交易，并通过二维码付款来启动付款流程。成功提交订单后，接着是JSAPI，App和其他付款方式。

### 2 URL
> URL: [https://api.mch.weixin.qq.com/pay/unifiedorder](https://api.mch.weixin.qq.com/pay/unifiedorder)
 
### 3 证书要求
> 不需要

### 4 请求参数
 
| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 对应微信分配的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 对应微信付款分配的商户ID |
| 设备ID | device_info | 否 | String(32) | 013467007045764 | 对应微信付款分配的终端设备ID。这个字段是商户自定的。注意：如果付款基于PC网页或微信web页执行，该字段值填WEB | 
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 签名类型 | sign_type | 否 | String(32) | HMAC-SHA256 | 当前支持HMAC-SHA256和MD5,默认为MD5。这个字段其实只有加签类型为HMAC-SHA256时才需要 |
| 项目描述 | body | 是 | String(32) | iPad Mini in white with 16G memory | 付款人购买项目的简短说明，请参考4.2.6节 |
| 项目明细 | detail | 否 | String(6000) | {"goods_detail":[{"goods_id":"iphone6s_16G","wxpay_goods_id":"1001","goods_name":"iP hone6s 16G","goods_num":1,"price":528800,"goods_category":"123456","body":"苹果手机"},{"goods_id":"iphon e6s_32G","wxpay_goods_id":"1002","goods_name":"iP hone6s 32G","quantity":1,"price":608800, "goods_category": "123789", "body":"苹果手机" }]} | JSON格式的详细的产品列表说明。生成签名时，请使用CDATA标记保护json字符串。wxpay_goods_id 可选微信设定的统一商品ID。注意：商品总价格不应该高于字段total_fee值且应该为优惠后价格 |
| 附加数据 | attach | 否 | String(128) | 附加说明 | 向查询下单API提交付款后，允许商户在付款通知中返回的附加字段 |
| 商户订单号 | out_trade_no | 是 | String(32) | 1217752501201407033233368018 | 最多32位字符数字。更多信息见4.2.5节 商户订单号 |
| 货币类型 | fee_type | 是 | String(16) | GBP | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 总金额 | total_fee | 是 | Int | 888 | 对应订单的总价。单位以分表示必须为整数。更多信息见4.2.1节 支付金额 |
| 终端IP | spbill_create_ip | 是 | String(16) | 8.8.8.8 | 对于本机快速支付(扫码)对应为调用微信付款API的机器IP。当公众号付款或APP内付款时，对应为客户终端IP |
| 交易开始时间 | time_start | 否 | String(14) | 20091225091010 | 对应为yyyyMMddHHmmss格式的交易创建时间，如Dec 25, 2009 09:10:10 (UTC+08)就是20091225091010。更多信息见4.2.3 时间协议 |
| 交易结束时间 | time_expire | 否 | String(14) | 20091227091010 | 格式同交易开始时间，但是最短交易结束时间至少大于交易开始时间5分钟 |
| 项目标签 | goods_tag | 否 | String(32)| WXG | 对应商品标签，是一个优惠券功能中的参数。详细见第10节 移动优惠券 |
| 通知URL | notify_url | 是 | String(256)| http://www.baidu.com/ | 对应接收微信付款通知的回调地址 | 
| 交易类型 | trade_type | 是 | String(16) | JSAPI | 可设置为JSAPI, NATIVE, 或APP |
| 产品ID | product_id | 否 | String(32) | 12235413214070356458058 | 仅当trade_type为NATIVE时才需要此字段。此ID包含商户设置的产品ID。|
| 限制付款方式 | limit_pay | 否 | String(32) | no_credit | no_credit: 不允许使用信用卡付款 |
| 用户标签 | openid | 否 | String(128) | oUpF8uMuAJO_M2pxb1Q9zNjWeS6o | 仅当trade_type为JSAPI时需要。它是当前appid下的唯一用户身份。关于如何获取openid，请参考[http://admin.wechat.com/wiki/index.php?title=User_Profile_via_Web](http://admin.wechat.com/wiki/index.php?title=User_Profile_via_Web) |

**举例**

```xml
<xml>
    <appid>wx2421b1c4370ec43b</appid>
    <attach>Payment Testing</attach>
    <body>JSAPI Payment Testing</body>
    <mch_id>10000100</mch_id> 
    <nonce_str>1add1a30ac87aa2db72f57a2375d8fec</nonce_str> 
    <notify_url>http://wxpay.weixin.qq.com/pub_v2/pay/notify.v2.php</notify_url> 
    <openid>oUpF8uMuAJO_M2pxb1Q9zNjWeS6o</openid> 
    <out_trade_no>1415659990</out_trade_no> 
    <spbill_create_ip>14.23.150.211</spbill_create_ip>
    <total_fee>1</total_fee>
    <trade_type>JSAPI</trade_type>
    <sign>0CB01533B8C1EF103065174F50BCA001</sign> 
</xml>
```
 
**注意：参数在XML文件中转义，并且使用CDATA标记说明XML的数据为未被XML解析器解析的数据。**

### 5 返回数据

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 状态码 | return_code | 是 | String(16) | SUCCESS | SUCCESS或FAIL。这只是个通信标签而非交易标签。交易的状态其实由result_code字段值确定。|
| 返回数据 | return_msg | 否 | String(128) | Signature failure | 如果非空， 返回的信息都是错误说明，如Signature failure就是参数格式检查错误 |
 
如果return_code为SUCCESS, 返回数据也会包含以下字段：
 
| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 对应微信分配的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 对应微信付款分配的商户ID |
| 设备ID | device_info | 否 | String(32) | 013467007045764 | 对应微信付款分配的终端设备ID。这个字段是商户自定的。注意：如果付款基于PC网页或微信web页执行，该字段值填WEB | 
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 结果码 | result_code | 是 | String(16) | SUCCESS | SUCCESS 或 FAIL |
| 错误码 | err_code | 否 | String(32) | SYSTEMERROR | 更多信息见错误码列表 |
| 错误说明 | err_code_des | 否 | String(128)| System error | 错误数据说明 |
 
如果return_code和result_code都是SUCCESS, 返回数据也会包含以下字段：
 
| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 交易类型 | trade_type | 是 | String(16) | JSAPI | 可设置为JSAPI, NATIVE, 或APP |
| 预先交易ID | prepay_id | 是 | String(64) | wx201410272009395522657a690389285100 | 微信创建的预付款交易ID。它用于稍后调用查询下单API。有效期为2小时。|
| 二维码URL | code_url | 否 | String(64) | URl:weixin://wxpay/ s/An4baqw | 当trade_type为NATIVE时返回该字段。该参数用于创建稍后展示给付款人的二维码 |
 
**举例**
 
```xml
<xml>
<return_code><![CDATA[SUCCESS]]></return_code> 
<return_msg><![CDATA[OK]]></return_msg> 
<appid><![CDATA[wx2421b1c4370ec43b]]></appid> 
<mch_id><![CDATA[10000100]]></mch_id> 
<nonce_str><![CDATA[IITRi8Iabbblz1Jc]]></nonce_str> 
<sign><![CDATA[7921E432F65EB8ED0CE9755F0E86D72F]]></sign> 
<result_code><![CDATA[SUCCESS]]></result_code> 
<prepay_id><![CDATA[wx201411101639507cbf6ffd8b0779950874]]></prepay_id> 
<trade_type><![CDATA[JSAPI]]></trade_type>
</xml>
```
 
### 6 错误码
 
| 名称 | 说明 | 原因 | 解答 |
| --- | --- | --- | --- |
| NOAUTH | 商户不允许使用该API | 商户没有该API授权 | 商户应该申请该API权限 |
| NOTENOUGH | 余额不足 | 付款人支付卡余额不足 | 通知付款人给账户充值或切换支付卡 |
| ORDERPAID | 订单已付款 | 订单已经支付不需要再次付款了 | 订单已支付，不需要采取进一步措施 |
| ORDERCLOSED | 订单被关闭 | 当前订单已经关闭无法再次付款 | 当前订单已经关闭了，付款人应被告知创建新订单 |
| SYSTEMERROR | 系统异常 | 系统超时 | 系统异常，可尝试用同样参数再次调用API |
| APPID_NOT_EXIST | appid不存在 | 缺少appid参数 | 检查并提供正确的appid |
| MCHID_NOT_EXIST | mchid不存在 | 缺少mchid参数 | 检查并提供正确的mchid |
| APPID_MCHID_NOT_MATCH | appid和mchid不匹配 | appid和mchid不匹配 | 检查appid是否属于相关msch_id |
| LACK_PARAMS | 缺少参数 | 必须的参数缺少 | 检查是否参数都满足了 |
| OUT_TRADE_NO_USED | 商户订单号重复 | 不能重复提交相同的交易 | 检查商户订单号先前是否已经被提交或使用过 |
| SIGNERROR | 签名错误 | 不正确的签名结果 | 检查签名参数和方法是否满足签名算法要求 |
| XML_FORMAT_ERROR | 无效的XML格式 | 无效的XML格式 | 检查XML参数是否组成正确的格式 |
| REQUIRE_POST_METHOD | 请使用post方法 | 数据没有使用post传输 | 检查数据是否通过POST提交 |
| POST_DATA_EMPTY | post data为空 | data不能为空 | 检查post data是否为空 |
| NOT_UTF8 | 无效编码格式 | 没有使用对应编码格式 | 请使用UTF8编码格式 |

## 2. 查询下单

### 1 用例

> 此API允许查询从微信发出的所有付款订单。根据此API接收的状态码，商户才能继续服务逻辑的下一步。以下是使用查询下单API的情形：
 
1. 由于商户后台，网络或服务的异常，商户没有收到任何付款。
2. 调用支付接口返回了未知交易状态的系统错误。
3. 调用快速下单API后返回了USERPAYING状态。
4. 调用关闭订单API和取消下单API前确定支付状态。

### 2 URL
[https://api.mch.weixin.qq.com/pay/orderquery](https://api.mch.weixin.qq.com/pay/orderquery)

### 3 证书要求

**不需要**
 
### 4 请求参数

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 对应微信分配的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 对应微信付款分配的商户ID |
| 微信订单号 | transaction_id | 微信商户选其一 | String(32) | 013467007045764 | 微信订单号优先 |
| 商户订单号 | out_trade_no | 微信商户选其一 | String(32) | 1217752501201407033233368018 | 对应由供应商的系统创建的内部订单号。未提供transaction_id时，此字段为必填字段。|
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 签名类型 | sign_type | 否 | String(32) | HMAC-SHA256 | 当前支持HMAC-SHA256和MD5,默认为MD5。这个字段其实只有加签类型为HMAC-SHA256时才需要 |
 
**举例**
 
```xml
<xml>
    <appid>wx2421b1c4370ec43b</appid> 
    <mch_id>10000100</mch_id> 
    <nonce_str>ec2316275641faa3aacf3cc599e8730f</nonce_str> 
    <transaction_id>1008450740201411110005820873</transaction_id> 
    <sign>FDD167FAA73459FD921B144BAF4F4CA2</sign>
</xml>
```

### 5 返回数据

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 状态码 | return_code | 是 | String(16) | SUCCESS | SUCCESS或FAIL。这只是个通信标签而非交易标签。交易的状态其实由result_code字段值确定。|
| 返回数据 | return_msg | 否 | String(128) | Signature failure | 如果非空， 返回的信息都是错误说明，如Signature failure就是参数格式检查错误 |
 
如果return_code为SUCCESS, 返回数据也会包含以下字段：
 
| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 对应微信分配的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 对应微信付款分配的商户ID |
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 结果码 | result_code | 是 | String(16) | SUCCESS | SUCCESS 或 FAIL |
| 错误码 | err_code | 否 | String(32) | SYSTEMERROR | 更多信息见错误码列表 |
| 错误说明 | err_code_des | 否 | String(128)| System error | 错误数据说明 |
 
如果return_code和result_code都是SUCCESS, 返回数据也会包含以下字段：
 
**注意：如果trade_state不为SUCCESS, 则只会返回out_trade_no和attach**
 
| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 设备ID | device_info | 否 | String(32) | 013467007045764 | 对应微信付款分配的终端设备ID。| 
| 用户标签 | openid | 否 | String(128) | wxd930ea5d5a258f4f | 对应微信系统提供的付款人的用户ID（每个appid实例唯一的OpenID格式） |
| 是否关注公众号 | is_subscribe | 否 | String(1) | Y | 对应付款人是否关注了关联的公众号，Y表示关注，N表述未关注 |
| 交易类型 | trade_type | 是 | String(16) | JSAPI | 可设置为JSAPI, NATIVE,MICROPAY. 或APP |
| 交易状态 | trade_state | 是 | String(32) | SUCCESS | SUCCESS: 付款成功。<br>REFUND：订单将被退款。<br>NOTPAY: 订单未支付。<br>CLOSED: 订单被关闭。<br>REVOKED: 订单被取消。<br>USERPAYING: 等待用户支付。<br>PAYERROR: 支付失败（由于银行或其他原因支付状态返回失败）|
| 付款银行 | bank_type | 是 | String(16) | CMC | 银行类别字符串 |
| 总金额 | total_fee | 是 | Int | 888 | 对应订单的总价。单位以分表示必须为整数。更多信息见4.2.1节 支付金额 |
| 货币类型 | fee_type | 是 | String(16) | GBP | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 现金支付金额 | cash_fee | 是 | Int | 100 | 对应交易的现金总金额。更多信息见4.2.1节 支付金额 |
| 现金类型 | cash_fee_type | 否 | String(16) | CNY | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 对应微信支付订单号 | transaction_id | 是 | String(32) | 1217752501201407033233368018 | 对应微信支付订单号 |
| 商户订单号 | out_trade_no | 是 | String(32) | 1217752501201407033233368018 | 由商户系统创建对应订单号码，与下单请求一致 |
| 商户数据包 | attach | 否 | String(128) | 123456 | 对应商户的数据包，原样返回 |
| 支付结束时间 | time_end | 是 | String(14) | 20141030133525 | 对应为yyyyMMddHHmmss格式的交易创建时间，如Dec 25, 2009 09:10:10 (UTC+08)就是20091225091010。更多信息见4.2.3 时间协议 | 
| 汇率 | rate | 是 | String(16) | 650000000 | 该值是外币对人民币汇率的10到8幂次 例如，外币对人民币的汇率为6.5，则价值为650000000, 即6.5*10^8 |
 
**举例：**
 
```xml
<xml>
    <return_code><![CDATA[SUCCESS]]></return_code> 
    <return_msg><![CDATA[OK]]></return_msg> 
    <appid><![CDATA[wx2421b1c4370ec43b]]></appid> 
    <mch_id><![CDATA[10000100]]></mch_id> 
    <device_info><![CDATA[1000]]></device_info> 
    <nonce_str><![CDATA[TN55wO9Pba5yENl8]]></nonce_str> 
    <sign><![CDATA[BDF0099C15FF7BC6B1585FBB110AB635]]></sign> 
    <result_code><![CDATA[SUCCESS]]></result_code> 
    <openid><![CDATA[oUpF8uN95-Ptaags6E_roPHg7AG0]]></openid>
    <is_subscribe><![CDATA[Y]]></is_subscribe> 
    <trade_type><![CDATA[MICROPAY]]></trade_type> 
    <bank_type><![CDATA[CCB_DEBIT]]></bank_type>
    <total_fee>1</total_fee>
    <fee_type><![CDATA[CNY]]></fee_type> 
    <transaction_id><![CDATA[1008450740201411110005820873]]></transaction_id> 
    <out_trade_no><![CDATA[1415757673]]></out_trade_no> 
    <attach><![CDATA[Additional Order description]]></attach> 
    <time_end><![CDATA[20141111170043]]></time_end> 
    <trade_state><![CDATA[SUCCESS]]></trade_state>
</xml>
```

### 6 错误码

| 名称 | 说明 | 原因 | 解答 |
| --- | --- | --- | --- |
| ORDERNOTEXIST | 该订单不存在 | 查询系统不存在改订单号 | 该API仅仅帮助查询支付成功的交易。商户应该检查提供的transaction_id是否正确 |
| SYSTEMERROR | 系统错误 | 数据从后端返回发生异常 | 这种系统异常重试就好了 |

## 3. 关闭订单

### 1. 用例

> 因为付款人无法为订单付款，商户在创建新订单之前必须调用该API。为了避免重复付款，原订单将被关闭。微信支付系统创建订单后，如果付款人未在规定时间内付款，就无法在系统中执行进一步操作。为了防止付款人继续下单，就调用此API来关闭订单。
 
**注意：关闭订单API在创建订单5分钟后才能使用**

### 2. URL

[https://api.mch.weixin.qq.com/pay/closeorder](https://api.mch.weixin.qq.com/pay/closeorder)

### 3. 证书要求

无

### 4. 请求参数

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 对应微信分配的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 对应微信付款分配的商户ID |
| 商户订单号 | out_trade_no | | 微信商户选其一 | 1217752501201407033233368018 | 对应由商户系统创建的内部订单号。未提供transaction_id时，此字段为必填字段。|
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 签名类型 | sign_type | 否 | String(32) | HMAC-SHA256 | 当前支持HMAC-SHA256和MD5,默认为MD5。这个字段其实只有加签类型为HMAC-SHA256时才需要 |

```xml
<xml>
    <appid>wx2421b1c4370ec43b</appid> 
    <mch_id>10000100</mch_id> 
    <nonce_str>ec2316275641faa3aacf3cc599e8730f</nonce_str> 
    <out_trade_no>1008450740201411110005820873</out_trade_no> 
    <sign>FDD167FAA73459FD921B144BAF4F4CA2</sign>
</xml>
```
 
### 5 返回数据

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 状态码 | return_code | 是 | String(16) | SUCCESS | SUCCESS或FAIL。这只是个通信标签而非交易标签。交易的状态其实由result_code字段值确定。|
| 返回数据 | return_msg | 否 | String(128) | Signature failure | 如果非空， 返回的信息都是错误说明，如Signature failure就是参数格式检查错误 |
 
如果return_code为SUCCESS, 返回数据也会包含以下字段：
 
| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 对应微信分配的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 对应微信付款分配的商户ID |
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 错误码 | err_code | 否 | String(32) | SYSTEMERROR | 更多信息见错误码列表 |
| 错误说明 | err_code_des | 否 | String(128)| System error | 错误数据说明 |
 
```xml
<xml>
    <return_code><![CDATA[SUCCESS]]></return_code> 
    <return_msg><![CDATA[OK]]></return_msg> 
    <appid><![CDATA[wx2421b1c4370ec43b]]></appid> 
    <mch_id><![CDATA[10000100]]></mch_id> 
    <nonce_str><![CDATA[BFK89FC6rxKCOjLX]]></nonce_str> 
    <sign><![CDATA[72B321D92A7BFA0B2509F3D13C7B1631]]></sign> 
    <result_code><![CDATA[SUCCESS]]></result_code>
</xml>
```

### 6 错误码

| 名称 | 说明 | 原因 | 解答 |
| --- | --- | --- | --- |
| ORDERPAID | 订单已付款 | 订单已经支付不需要再次付款了 | 订单已支付，不需要采取进一步措施 |
| SYSTEMERROR | 系统异常 | 系统超时 | 系统异常，可尝试用同样参数再次调用API |
| ORDERNOTEXIST | 订单不存在 | 该系统中不存在此订单 | 由于该订单仍处于待处理交易中，请勿尝试关闭该订单 |
| ORDERCLOSED | 订单已关闭 | 订单已被关闭不能再次操作 | 订单已经关闭了，不需要进一步操作了 | 
| SIGNERROR | 签名错误 | 不正确的签名结果 | 检查签名参数和方法是否满足签名算法要求 |
| XML_FORMAT_ERROR | 无效的XML格式 | 无效的XML格式 | 检查XML参数是否组成正确的格式 |
| REQUIRE_POST_METHOD | 请使用post方法 | 数据没有使用post传输 | 检查数据是否通过POST提交 |

## 4. 提交退款

### 1 用例

> 付款交易完成且付款人或商户要求退款后的一段时间内，商户可以通过此API退款给付款人。微信支付系统成功接收并验证了退款请求后，将根据退款规则将原始付款金额退还给付款人。
>
> 注意：
>
> 1. 对于超过3个月之前完成的任何交易，均不支持退款
>
> 2. 交易退款可以以多份退款的形式处理。在这种情况下，必须提供原始订单号，并且必须设置多个退款号。总退款金额不能超过原始付款金额。

**注意：如果退款请求失败，请使用相同的out_refund_no重试**
 
**请求频率限制：150qps**
 
**错误或无效请求频率限制：60qps**

**注意：一笔订单分开退款要低于50次**

### 2 URL
 
[https://api.mch.weixin.qq.com/secapi/pay/refund](https://api.mch.weixin.qq.com/secapi/pay/refund)
 
### 3 证书要求

> 需要双向证书

### 4 请求参数

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 对应微信分配的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 对应微信付款分配的商户ID |
| 设备ID | device_info | 否 | String(32) | 013467007045764 | 对应微信付款分配的终端设备ID。这个字段是商户自定的。注意：如果付款基于PC网页或微信web页执行，该字段值填WEB | 
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 签名类型 | sign_type | 否 | String(32) | HMAC-SHA256 | 当前支持HMAC-SHA256和MD5,默认为MD5。这个字段其实只有加签类型为HMAC-SHA256时才需要 |
| 微信订单号 | transaction_id | 微信商户选其一 | String(32) | 1217752501201407033233368018 | 微信订单号优先 |
| 商户订单号 | out_trade_no | 微信商户选其一 | String(32) | 1217752501201407033233368018 | 对应商户系统创建的内部订单号。未提供transaction_id时，此字段为必填字段。|
| 商户退款号 | out_refund_no | 是 | String(32) | 1217752501201407033233368018 | 系统中是唯一的内部退款号码。 单笔交易可以作为多个部分退款处理，部分退款的总和等于原始交易的总和。|
| 总金额 | total_fee | 是 | Int | 888 | 对应订单的总价。单位以分表示必须为整数。更多信息见4.2.1节 支付金额 |
| 退款金额 | refund_fee | 是 | Int | 100 | 对应一笔交易的退款总金额。单位以分表示必须为整数。更多信息见4.2.1节 支付金额 |
| 货币类型 | refund_fee_type | 否 | String(8) | GBP | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 退款原因 | refund_desc | 否 | String(80) | Products sold out | 商家提交此字段值后，它将通知购物者退款原因。|

**举例：**

```xml

<xml>
    <appid>wx2421b1c4370ec43b</appid> 
    <mch_id>10000100</mch_id> 
    <nonce_str>6cefdb308e1e2e8aabd48cf79e546a02</nonce_str> 
    <op_user_id>10000100</op_user_id>   // 这个值文档有问题？
    <out_refund_no>1415701182</out_refund_no> 
    <out_trade_no>1415757673</out_trade_no> 
    <refund_fee>1</refund_fee>
    <total_fee>1</total_fee>
    <transaction_id>1217752501201407033233368018</transaction_id> 
    <sign>FE56DD4AA85C0EECA82C35595A69E153</sign>
</xml>
```

### 5 返回数据

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 状态码 | return_code | 是 | String(16) | SUCCESS | SUCCESS或FAIL。这只是个通信标签而非交易标签。交易的状态其实由result_code字段值确定。|
| 返回数据 | return_msg | 否 | String(128) | Signature failure | 如果非空， 返回的信息都是错误说明，如Signature failure就是参数格式检查错误 |
 
如果return_code为SUCCESS, 返回数据也会包含以下字段：
 
| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 结果码 | result_code | 是 | String(16) | SUCCESS | SUCCESS 或 FAIL。获取退款状态可以调用查询退款API|
| 错误码 | err_code | 否 | String(32) | SYSTEMERROR | 更多信息见错误码列表 |
| 错误说明 | err_code_des | 否 | String(128)| System error | 错误数据说明 |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 调用该接口提交的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 调用该接口提交的商户ID |
| 设备ID | device_info | 否 | String(32) | 013467007045764 | 对应微信付款分配的终端设备ID。此字段中的值必须与创建订单时使用的device_info值匹配 |
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 微信订单号 | transaction_id | 是 | String(32) | 1217752501201407033233368018 | 对应微信支付订单号 |
| 商户订单号 | out_trade_no | 是 | String(32) | 1217752501201407033233368018 | 对应由商户系统创建的内部订单号。|
| 商户退款号 | out_refund_no | 是 | String(32) | 1217752501201407033233368018 | 商户退款号|
| 微信退款号 | refund_id | 是 | String(32) | 1217752501201407033233368018 | 微信退款号 |
| 退款金额 | refund_fee | 是 | Int | 100 | 对应一笔交易的退款总金额。单位以分表示必须为整数。更多信息见4.2.1节 支付金额 |
| 货币类型 | refund_fee_type | 否 | String(8) | GBP | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 总金额 | total_fee | 是 | Int | 888 | 对应订单的总价。单位以分表示必须为整数。更多信息见4.2.1节 支付金额 |
| 货币类型 | fee_type | 是 | String(16) | GBP | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 现金支付金额 | cash_fee | 是 | Int | 100 | 对应交易的现金总金额。更多信息见4.2.1节 支付金额 |
| 现金类型 | cash_fee_type | 否 | String(16) | CNY | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 现金退款金额 | cash_refund_fee | 是 | Int | 100 | 对应交易的现金退款总金额。更多信息见4.2.1节 支付金额
| 现金退款类型 | cash_refund_fee_type | 否 | String(8) | CNY | 符合ISO-4217标准，默认人民币。更多信息见4.2.2节 货币种类 |
| 汇率 | rate | 是 | String(16) | 650000000 | 该值是外币对人民币汇率的10到8幂次 例如，外币对人民币的汇率为6.5，则价值为650000000, 即6.5*10^8 |

**举例：**
 
```xml
<xml>
    <return_code><![CDATA[SUCCESS]]></return_code> 
    <return_msg><![CDATA[OK]]></return_msg> 
    <appid><![CDATA[wx2421b1c4370ec43b]]></appid> 
    <mch_id><![CDATA[10000100]]></mch_id> 
    <nonce_str><![CDATA[NfsMFbUFpdbEhPXP]]></nonce_str> 
    <sign><![CDATA[B7274EB9F8925EB93100DD2085FA56C0]]></sign> 
    <result_code><![CDATA[SUCCESS]]></result_code> 
    <transaction_id><![CDATA[1008450740201411110005820873]]></transaction_id> 
    <out_trade_no><![CDATA[1415757673]]></out_trade_no> 
    <out_refund_no><![CDATA[1415701182]]></out_refund_no> 
    <refund_id><![CDATA[2008450740201411110000174436]]></refund_id> 
    <refund_channel><![CDATA[]]></refund_channel>
    <refund_fee>1</refund_fee>
    <coupon_refund_fee>0</coupon_refund_fee>
</xml>
```

### 6 错误码

| 名称 | 说明 | 原因 | 解答 |
| --- | --- | --- | --- |
| SYSTEMERROR | 系统异常 | 系统超时 | 系统异常，可尝试用同样参数再次调用API |
| USER_ACCOUNT_ABNORMAL | 退款请求失败 | 用户账户取消 | 该错误代码表示退款请求失败，商家需要自行处理退款 |
| NOTENOUGH | 未结清的资金不足以退款 | 未结清的资金不足以退款 | 此错误代码表示退款请求失败，一旦有足够的未结算资金，商家需要重新调用退款API一次或尝试多次 |
| INVALID_TRANSACTIONID | 无效transaction_id | 请求参数不正确 | 请求参数不正确，检查原参数 |
| PARAM_ERROR | 参数错误 | 请求参数不正确 | 请求参数不正确，检查原参数 |
| APPID_NOT_EXIST | appid不存在 | 缺少appid参数 | 检查并提供正确的appid |
| MCHID_NOT_EXIST | mchid不存在 | 缺少mchid参数 | 检查并提供正确的mchid |
| APPID_MCHID_NOT_MATCH | appid和mchid不匹配 | appid和mchid不匹配 | 检查appid是否属于相关msch_id |
| SIGNERROR | 签名错误 | 不正确的签名结果 | 检查签名参数和方法是否满足签名算法要求 |
| XML_FORMAT_ERROR | 无效的XML格式 | 无效的XML格式 | 检查XML参数是否组成正确的格式 |

## 5. 查询退款

### 1 用例
> 提交"提交退款API"后，可以调用此API来检查退款状态。 提交退款后，退款处理可能会有所延迟：退款至余额需20分钟，退款至银行卡需3个工作日。
>
>注意：一旦一笔订单的部分退款超过20次，请使用供应商退款编号进行退款查询。

### 2 URL
[https://api.mch.weixin.qq.com/pay/refundquery](https://api.mch.weixin.qq.com/pay/refundquery)
 
### 3 证书要求

无

### 4 请求参数
 
**注意：所有下表$n都从0开始**

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 对应微信分配的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 对应微信付款分配的商户ID |
| 设备ID | device_info | 否 | String(32) | 013467007045764 | 对应微信付款分配的终端设备ID。这个字段是商户自定的。注意：如果付款基于PC网页或微信web页执行，该字段值填WEB | 
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 签名类型 | sign_type | 否 | String(32) | HMAC-SHA256 | 当前支持HMAC-SHA256和MD5,默认为MD5。这个字段其实只有加签类型为HMAC-SHA256时才需要 |
| 微信订单号 | transaction_id | 选其一 | String(32) | 1217752501201407033233368018 | 对应微信订单号 |
| 商户订单号 | out_trade_no | 选其一 | String(32) | 1217752501201407033233368018 | 对应由商户系统创建的内部订单号|
| 商户退款号 | out_refund_no | 选其一 | String(32) | 1217752501201407033233368018 | 对应商户退款号|
| 微信退款号 | refund_id | 选其一 | String(32) | 1217752501201407033233368018 | 从提交退款API返回得到的微信退款号。该字段将提供refund_id，out_refund_no，out_trade_no或transaction_id。 它们的优先级如下所示：refund_id>out_refund_no>transaction_id>out_trade_no |

**举例：**
 
```xml
<xml>
<appid>wx2421b1c4370ec43b</appid> 
<mch_id>10000100</mch_id> 
<nonce_str>0b9f35f484df17a732e537c37708d1d0</nonce_str> 
<out_refund_no></out_refund_no> 
<out_trade_no>1415757673</out_trade_no> 
<refund_id></refund_id>
<transaction_id></transaction_id> 
<sign>66FFB727015F450D167EF38CCC549521</sign>
</xml>
```

### 5 返回数据

| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 状态码 | return_code | 是 | String(16) | SUCCESS | SUCCESS或FAIL。这只是个通信标签而非交易标签。交易的状态其实由result_code字段值确定。|
| 返回数据 | return_msg | 否 | String(128) | Signature failure | 如果非空， 返回的信息都是错误说明，如Signature failure就是参数格式检查错误 |
 
如果return_code为SUCCESS, 返回数据也会包含以下字段：
 
| 字段名称 | ID | 必要性 | 类型 | 案例 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 结果码 | result_code | 是 | String(16) | SUCCESS | SUCCESS 或 FAIL。|
| 错误码 | err_code | 否 | String(32) | SYSTEMERROR | 更多信息见错误码列表 |
| 错误说明 | err_code_des | 否 | String(128)| System error | 错误数据说明 |
| 公众号ID | appid | 是 | String(32) |  wx8888888888888888 | 调用该接口提交的公众号ID |
| 商户ID | mch_id | 是 | String(32) | 1900000109 | 调用该接口提交的商户ID |
| 设备ID | device_info | 否 | String(32) | 013467007045764 | 对应微信付款分配的终端设备ID。此字段中的值必须与创建订单时使用的device_info值匹配 |
| 随机字符串 | nonce_str | 是 | String(32) | 5K8264ILTKCH16CQ2502SI8ZNMTM67VS | 最多32位字符。更多信息见4.4.2节随机字符串算法 |
| 签名 | sign | 是 | String(32) |  C380BEC2BFD727A4B6845133519F3AD6 | 对应签名。更多信息见4.4.1 签名算法 |
| 微信订单号 | transaction_id | 是 | String(32) | 1217752501201407033233368018 | 对应微信支付订单号 |
| 商户订单号 | out_trade_no | 是 | String(32) | 1217752501201407033233368018 | 对应由商户系统创建的内部订单号。|
| 总金额 | total_fee | 是 | Int | 888 | 对应订单的总价。单位以分表示必须为整数。更多信息见4.2.1节 支付金额 |
| 货币类型 | fee_type | 是 | String(16) | GBP | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 现金支付金额 | cash_fee | 是 | Int | 100 | 对应交易的现金总金额。更多信息见4.2.1节 支付金额 |
| 现金类型 | cash_fee_type | 否 | String(16) | CNY | 符合ISO-4217标准，基于3个字符。更多信息见4.2.2节 货币种类 |
| 退款金额 | refund_fee_$n | 是 | Int | 100 | 退款可以作为多次部分退款处理。出价货币类型应与出价金额一致。单位以分表示必须为整数。更多信息见4.2.1节 支付金额 |
| 退款计数 | refund_count | 是 | Int | 1 | 退款次数记录 |
| 商户退款号 | out_refund_no_$n | 是 | String(32) | 1217752501201407033233368018 | 商户退款号 |
| 微信退款号 | refund_id_$n | 是 | String(32) | 1217752501201407033233368018 | 微信退款号 |
| 退款渠道 | refund_channel_$n | 否 | String(16)| ORIGINAL | ORIGINAL：退款至原账户。<br>BALANCE: 退款至余额。<br>OTHER_BALANCE:由于原账户异常，退款至其他微信账户余额。<br>OTHER_BANKCARD：由于原银行卡异常，退款到其他银行卡|
| 退款状态 | refund_status_$n | 是 | String(16) | SUCCESS | SUCCESS：退款成功。<br>REFUNDCLOSE：退款失败。<br>PROCESSING：等待退款。<br>NOTSURE：需要商户使用原退款号再次调用提交退款API。<br>CHANGE：由于付款人的银行卡被吊销或冻结无法处理退款。因此，退款将转入商户的现金帐户。在这种情况下，退款必须在商户的客户协助人员的帮助下进行离线处理，也可以通过财付通将退款金额从商户转移到付款人。|
| 接收退款账户| refund_recv_accout_$n | 是 | String(64) | 招商银行信用卡 0403 | 最后退款的账户格式：<br>1.退回银行卡：{银行名}{银行卡类别}{尾号} <br>2.退回余额：支付用户零钱。<br>3. 退回商家：<br>商户基本账户 Merchants’ basic account，<br>商户结算银行账户 Merchants’ bank account |
| 退款成功时间 | refund_success_time_$n | 否 | String(20) | 2016-07-25 15:26:26 | 退款成功时间，仅在退款成功完成后返回。|
| 汇率 | rate | 是 | String(16) | 650000000 | 该值是外币对人民币汇率的10到8幂次 例如，外币对人民币的汇率为6.5，则价值为650000000, 即6.5*10^8 |

**举例：**
 
```xml
<xml>
    <appid><![CDATA[wx2421b1c4370ec43b]]></appid> 
    <mch_id><![CDATA[10000100]]></mch_id> 
    <nonce_str><![CDATA[TeqClE3i0mvn3DrK]]></nonce_str> 
    <out_refund_no_0><![CDATA[1415701182]]></out_refund_no_0> 
    <out_trade_no><![CDATA[1415757673]]></out_trade_no> 
    <refund_count>1</refund_count>
    <refund_fee_0>1</refund_fee_0> 
    <refund_id_0><![CDATA[2008450740201411110000174436]]></refund_id_0> 
    <refund_status_0><![CDATA[PROCESSING]]></refund_status_0> 
    <result_code><![CDATA[SUCCESS]]></result_code> 
    <return_code><![CDATA[SUCCESS]]></return_code> 
    <return_msg><![CDATA[OK]]></return_msg> 
    <sign><![CDATA[1F2841558E233C33ABA71A961D27561C]]></sign> 
    <transaction_id><![CDATA[1008450740201411110005820873]]></transaction_id>
</xml>
```

### 6 错误码

| 名称 | 说明 | 原因 | 解答 |
| --- | --- | --- | --- |
| SYSTEMERROR | 系统异常 | 系统超时 | 系统异常，可尝试用同样参数再次调用API |
| REFUNDNOTEXIST | 查询退款失败 | 退款号错误或订单状态不在退款流程 | 请检查退款号码或交易ID是否正确，或者订单是否已付款或正在退款中。 |
| INVALID_TRANSACTIONID | 无效transaction_id | 请求参数不正确 | 请求参数不正确，检查原参数 |
| PARAM_ERROR | 参数错误 | 请求参数不正确 | 请求参数不正确，检查原参数 |
| APPID_NOT_EXIST | appid不存在 | 缺少appid参数 | 检查并提供正确的appid |
| MCHID_NOT_EXIST | mchid不存在 | 缺少mchid参数 | 检查并提供正确的mchid |
| APPID_MCHID_NOT_MATCH | appid和mchid不匹配 | appid和mchid不匹配 | 检查appid是否属于相关msch_id |
| REQUIRE_POST_METHOD | 请使用post方法 | 数据没有使用post传输 | 检查数据是否通过POST提交 |
| SIGNERROR | 签名错误 | 不正确的签名结果 | 检查签名参数和方法是否满足签名算法要求 |
| XML_FORMAT_ERROR | 无效的XML格式 | 无效的XML格式 | 检查XML参数是否组成正确的格式 |

## 6. 下载对账文件 

## 7. 通常支付结果通知

## 8. 报告速度测试

## 9. 短URL转换

# 10. 查询已结算的资金

# 12.下载付款SDK




