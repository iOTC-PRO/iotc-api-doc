## iOTC-API
### 1. 接口说明
	本文档定义了场外交易平台接入iOTC的API。
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
	本文档中的所有接口如果无特别说明，均使用HTTPS参数POST方式提交，字符集均采用UTF-8.
	参数类型:...todo
#### 1.4 签名和验签
	使用接口前，所有请求都包含签名参数，接收方务必检查签名的正确性，以保证业务数据合法安全。
	签名使用标准签名算法“SHA1withRSA"。

	签名规则：
	1、签名key
	固定不变，key=weroqwetivnd
	2、签名加密后新key
	示例简要说明：
	newkey = md5(sha1(key.toLowerCase()))
	3、所有参数名称做a-z排序后，与key=newkey做拼接，得到sign。
	示例简要说明：
	sign=md5(key=newkey&isReal=true&phone=18611110008&sourceType=abc&userName=章三
#### 1.5 IP白名单
	本文档所有接口均需要合作方提供有效IP地址方可进行访问。

### 2. 场外交易连接接口
#### 2.1 接口说明

接口说明  | ------
------------- | :-------------
接口地址  | /cooperation/connect
接口描述  | 合作方通过该接口建立iOTC的授权登录，<br/>iOTC将提供一个授权hook返回给合作方，<br/>合作方拿到hook后拼装跳转到iOTC的URL。
业务规则  | hook的有效时间是 20s。
接口模式  | 直连

#### 2.2 接口参数
请求：

参数名称  | 比填 | 类型 | 长度 | 参数说明
------------- | ------------- | ------------- | ------------- | :-------------
phone | Y | String | ...todo | 用户手机号
userName | Y | String | ...todo | 用户昵称
isReal | Y | String | ...todo | 是否实名
sourceType | Y | String | ...todo | 合作方简称
sign | Y | String | ...todo | 签名
walletAddr | Y | String | ...todo | 钱包地址

返回：

参数名称  | 比填 | 类型 | 长度 | 参数说明
------------- | ------------- | ------------- | ------------- | :-------------
status | Y | Integer | ...todo | 返回状态：0 失败，1成功
hook | Y | String | ...todo | 授权hook
msg | Y | String | ...todo | 返回消息

### 3. 附录
#### 3.1 通用返回码
...todo
#### 3.2 通用失败错误码
...todo





