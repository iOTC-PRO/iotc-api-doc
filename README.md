## iOTC-OpenAPI
### 1. 接口说明
	本文档定义了场外交易平台接入iOTC的OpenAPI。
#### 1.1 接口模式
	本文档定义的接口模式为：直连接口。
##### 1.1.1 直连接口
	请求：场外交易平台直接组装HTTPS报文发送至iOTC,iOTC完成处理后同步返回结果。
	返回：所有直连接口都同步返回。
#### 1.2 接口地址
	iOTC的测试环境请求地址：
	直连请求地址： https://api.aiwith.com
	iOTC的生产环境请求地址：
	直连请求地址： ...todo
#### 1.3 通用参数类型
	本文档中的所有接口如果无特别说明，均使用HTTPS参数POST方式提交，使用JSON格式作为报文传输。字符集均采用UTF-8.	
#### 1.4 消息签名
	使用接口前，所有请求都包含签名参数，接收方务必检查签名的正确性，以保证业务数据合法安全。
	签名使用签名算法“SHA1"。
	
	签名规则：
	1、获取签名的密钥appSecret（iOTC申请）。
	
	2、[org.apache.commons.codec.digest.DigestUtils]
	newAppSecret=DigestUtils.md5Hex(DigestUtils.sha1Hex(appSecret).toLowerCase()).toLowerCase();
	
	3、除sign之外的所有参数名称做a-z排序后,与key=newAppSecret做拼接，然后md5，得到结果content。
	结果示例说明：isReal=true&partnerId=100001&phone=90003450001&userName=niuniu&walletAddress={"BTC":"38mH5oT82URX3Ayq1s2334ZVv9Rkjob8m5Vo","ETH":"45mH5oT82URsdfq1s2334ZVv9Rkjob8m5Vo","USDT":"76mH5oT82URsdfq1s2334ZVv9Rkjob8m5Vo"}&key=5473631659d8f9dcc691a27901052803
	
	4、得到签名
	sign=DigestUtils.md5Hex(content.toString()).toLowerCase();
#### 1.5 IP白名单
	本文档所有接口均需要合作方提供有效IP地址方可进行访问。

### 2. 场外交易连接接口
#### 2.1 接口说明

接口说明  | ------
------------- | :-------------
接口地址  | /cooperation/connect
接口描述  | 合作方通过该接口建立iOTC的授权登录，<br/>iOTC将提供一个授权hook返回给合作方，<br/>合作方拿到hook后拼装跳转到iOTC的URL。<br/>URL地址[强制]约束规范：<br/>https://otc.***.com?hook=uckDPmq1hpbR716349549b58&source=coinx。
业务规则  | hook的有效时间是 20s。
接口模式  | 直连

#### 2.2 接口参数
请求：

参数名称  | 必填 | 类型 | 长度 | 参数说明
------------- | ------------- | ------------- | ------------- | :-------------
partnerId | Y | String | 50 | 合作方ID（iOTC申请）
sign | Y | String | 50 | 签名
phone | Y | String | 11 | 用户手机号
userName | Y | String | 50 | 用户昵称
isReal | Y | String | 10 | 是否实名
walletAddress | Y | String | | 钱包地址,JSON数据,示例说明:<br/>{"BTC":"38mH5oT82URX3Ayq1s2334ZVv9Rkjob8m5Vo",<br/>"ETH":"45mH5oT82URsdfq1s2334ZVv9Rkjob8m5Vo",<br/>"USDT": "76mH5oT82URsdfq1s2334ZVv9Rkjob8m5Vo"}<br/>当前支持的币种见【币种类型】

返回：

参数名称  | 必填 | 类型 | 长度 | 参数说明
------------- | ------------- | ------------- | ------------- | :-------------
status | Y | Integer |  | 请求响应码，见【通用返回码】
msg   | Y | String |  | 请求响应消息
hook | Y | String | | 请求hook

### 3. 附录
#### 3.1 通用返回码
枚举值  | 枚举描述 
------------- | ------------- 
0 | 调用成功
1 | 调用失败，失败原因请查看【调用失败错误码】
#### 3.2 通用失败错误码
枚举值  | 枚举描述 
------------- | ------------- 
100001 | 系统错误
100002 | json 参数格式错误
100003 | 签名验证失败
100004 | 合作商户ID不存在

#### 3.3 业务枚举
##### 3.3.1 币种类型
枚举值  | 枚举描述 
------------- | ------------- 
BTC  | BTC
ETH  | ETH
USDT | USDT






