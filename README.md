## iOTC-OpenAPI
### 1. 接口说明
<a>本文档定义了场外交易平台接入iOTC的OpenAPI。</a>
#### 1.1 接口模式
<a>本文档定义的接口模式为：直连接口。</a>
##### 1.1.1 直连接口
<a>请求：场外交易平台直接组装HTTPS报文发送至iOTC,iOTC完成处理后同步返回结果。<br/>
   返回：所有直连接口都同步返回。</a><br/>
   
1、请求参数格式约定如下：

参数名称  | 必填 | 参数说明 
------------- | ------------- | :-------------
reqData | Y | 业务数据报文，JSON 格式，具体见各接口定义
sign | Y | 对reqData参数的签名，签名算法见下方“签名和验签”章节


reqData 请求参数格式约定如下：

参数名称  | 必填 | 参数说明 
------------- | ------------- | :-------------
partnerId | Y | 合作方ID（iOTC申请）
nonce | Y | 请求唯一标识，一般使用UUID来标示
timestamp | Y | 时间戳,精确到毫秒，时区采用北京时间 (GMT+8:00)。<br/><a>如果此时间和iOTC时间相差过大(10分钟)，<br/>则拒绝执行操作。</a>

附加：请求报文示例

	{
    	"sign":"9D55CFAF9BFEA98B090C34F26DBFD529",
    	"reqData":
    	   {
    	     "partnerId":"9000023456",
    	     "nonce":"EA98B090CSF4534F26DBFD5SDF",
    	     "timestamp":"1534136312618",
    	     "mobile":"18600000001",
    	     "verifyUser":"true"
    	   }
  	}


2、返回参数格式约定如下：

参数名称  | 必填 | 类型 | 长度 | 参数说明
------------- | ------------- | ------------- | ------------- | :-------------
code | Y | S | | 响应码，见【调用返回码】
message | N | S | 50 | 响应信息描述
data | Y | S | | 响应数据

data 响应参数格式约定如下：

参数名称  | 必填 | 类型 | 长度 | 参数说明
------------- | ------------- | ------------- | ------------- | :-------------


附加：响应报文示例

	{
    	"code":"0",
   		"message":"操作成功",
    	"data":
    	   {
    	     "hook":"9D55CFAF9BFEA98B090C34F26DBFD529"
    	   }
  	}


#### 1.2 接口地址
<a>iOTC的测试环境请求地址<br/>
   直连请求地址： http://api.aiwith.com/open/service<br/>
   页面请求地址：http://plugin.aiwith.com<br/>
   
   iOTC的生产环境请求地址<br/>
	直连请求地址： https://api.iotc.com/open/service<br/>
	页面请求地址：https://plugin.iotc.com<br/></a>
	
#### 1.3 通用参数类型
<a>本文档中的所有接口如果无特别说明，均使用HTTPS参数POST方式提交，使用JSON格式作为报文传输。字符集均采用UTF-8。</a><br/>

<a>本文档中所有参数和参数值都是String类型。</a>

#### 1.4 签名和验签
<a>	使用接口前，所有请求都包含签名参数。签名使用标准签名算法”SHA1withRSA”。<br/>
签名私钥由iOTC提供！</a>

生成签名JavaDemo（main方法直接运行），引用SignatureUtils中的部分防范见下方
	
	//1、生成密钥对
	KeyPair keyPair = null;
	keyPair = SignatureUtils.generateRsaKeyPair(1024);
	//2、生成私钥
	String privateKeyStr = Base64.encodeBase64String(keyPair.getPrivate()
				.getEncoded());
	//3、组装需要签名的数据
	Map<String,String> resqData = new HashMap<String,String>();
	resqData.put("partnerId","1000001");
	//4、将数据生成签名
	PrivateKey privateKey = SignatureUtils.getRsaPkcs8PrivateKey(Base64
				.decodeBase64(privateKeyStr));
	byte[] sign = SignatureUtils.sign(SignatureAlgorithm.SHA1WithRSA,
				privateKey, resqData);
	String signStr = Base64.encodeBase64String(sign);

验证签名JavaDemo（main方法直接运行），引用SignatureUtils中的部分防范见下方

	//1、生成公钥
	String publicKeyStr = Base64.encodeBase64String(keyPair.getPublic()
					.getEncoded());
	//2、验证签名
	byte[] keyByte = Base64.decodeBase64(publicKeyStr);
	PublicKey publicKey = SignatureUtils.getRsaX509PublicKey(keyByte);
	//signStr 需要验证的签名串
	byte[] signByte = Base64.decodeBase64(signStr);
	boolean b = SignatureUtils.verify(SignatureAlgorithm.SHA1WithRSA, 
	              publicKey, resqData, signByte);
	
SignatureUtils（main方法直接运行）

生成密钥对方法

	public static KeyPair generateRsaKeyPair(int keySize)
			throws NoSuchAlgorithmException {
		KeyPairGenerator keyPairGen = null;
		keyPairGen = KeyPairGenerator.getInstance("RSA");
		keyPairGen.initialize(keySize, new SecureRandom());
		KeyPair keyPair = keyPairGen.generateKeyPair();
		return keyPair;
	}
	
生成签名方法
 
	/**
	 * 进行签名
	 * @param algorithm
	 * @param privateKey
	 * @param resqData
	 * @return
	 * @throws GeneralSecurityException
	 * @throws IOException
	 */
	public static byte[] sign(SignatureAlgorithm algorithm, PrivateKey privateKey,
	                          Map<String,String> resqData)
			throws GeneralSecurityException,IOException {
		String sortData = sort(resqData);
		byte[] data = sortData.getBytes(Charset.forName("utf-8"));
		ByteArrayInputStream input = new ByteArrayInputStream(data);
		try {
			Signature signature = Signature.getInstance(algorithm
					.getSignAlgorithm());

			signature.initSign(privateKey);
			doUpdate(signature, input);
			return signature.sign();
		} catch (IOException e) {
			throw new RuntimeException(e);
		} finally {
			IOUtils.closeQuietly(input);
		}
	}
	/**
	 * 对签名数据排序
	 * @param data
	 * @return
	 */
	private static String sort(Map<String,String> data) {
		StringBuilder resqData = new StringBuilder();
		List<String> keys = new ArrayList<String>(data.keySet());
		Collections.sort(keys);
		for (int i = 0; i < keys.size(); i++) {
			String key = keys.get(i);
			String value = data.get(key);
			if (StringUtils.isNotBlank(value)) {
				resqData.append(key).append("=").append(value).append("&");
			}
		}
		return resqData.substring(0, resqData.lastIndexOf("&"));
	}
	private static void doUpdate(Signature signature, InputStream input)
			throws IOException, SignatureException {

		byte[] buf = new byte[4096];

		int c = 0;
		do {
			c = input.read(buf);

			if (c > 0) {
				signature.update(buf, 0, c);
			}
		} while (c != -1);
	}
  

#### 1.5 IP白名单
<a>本文档所有接口均需要合作方提供有效IP地址方可进行访问。</a>

### 2. 场外交易接口
#### 2.1 场外授权连接接口
##### 2.1.1 接口说明

接口说明  | ------
------------- | :-------------
接口地址  | /authConnect
接口描述  | 合作方通过该接口建立iOTC的授权登录，<br/>iOTC将提供一个授权hook返回给合作方，<br/>合作方拿到hook后拼装跳转到iOTC的URL。<br/>URL地址[强制]约束规范：<br/>https://plugin.***.com?hook=uckDPmq1hpbR716349549b58&source=foxone。
业务规则  | hook的有效时间是 20s。
接口模式  | 直连
异步通知  | 否

##### 2.1.2 接口参数
请求：

参数名称  | 必填 | 是否唯一 | 长度 | 参数说明
------------- | ------------- | ------------- | ------------- | :-------------
mobile | Y |  是 |  |  手机号
code | N |  是 |  |  国际区号,默认86
verifyUser | Y |  否 | 10 | 是否实名认证

返回：

参数名称  | 必填 |  参数说明
------------- | ------------- | :-------------
hook | Y |  授权连接接口

##### 2.1.3 请求响应报文示例

1、请求报文示例

	{
    	"sign":"9D55CFAF9BFEA98B090C34F26DBFD529",
    	"reqData":
    	   {
    	     "partnerId":"9000023456",
    	     "code":"86",
    	     "nonce":"EA98B090CSF4534F26DBFD5SDF",
    	     "timestamp":"1534136312618",
    	     "mobile":"18600000001",
    	     "verifyUser":"true"
    	   }
  	}
  	
  	
 2、响应报文示例

	{
    	"code":"0",
   		"message":"操作成功",
    	"data":
    	   {
    	     "hook":"9D55CFAF9BFEA98B090C34F26DBFD529"
    	   }
  	}


#### 2.2 场外用户更新接口
##### 2.2.1 接口说明

接口说明  | ------
------------- | :-------------
接口地址  | /updateUserInfo
接口描述  | 更新用户信息
业务规则  | 1、用户已在iOTC中存在；
接口模式  | 直连
异步通知  | 否

##### 2.2.2 接口参数
请求：

参数名称  | 必填 | 是否唯一 | 长度 | 参数说明
------------- | ------------- | ------------- | -------------  | :-------------
mobile | Y |  是 | 11 | 用户手机号
btcAddr | N |  否 | | BTC地址
ethAddr | N |  否 | | ETH地址
usdtAddr | N | 否 | | USDT地址

##### 2.2.3 请求响应报文示例

1、请求报文示例

	{
		"sign": "9D55CFAF9BFEA98B090C34F26DBFD529",
		"reqData": {
			"partnerId": "9000023456",
			"nonce": "EA98B090CSF4534F26DBFD5SDF",
			"timestamp": "1534136312618",
			"mobile": "18600000001",
			"btcAddr": "38mH5oT82URX3Ayq1s2334ZVv9Rkjob8m5Vo",
			"ethAddr": "45mH5oT82URsdfq1s2334ZVv9Rkjob8m5Vo",
			"usdtAddr": "76mH5oT82URsdfq1s2334ZVv9Rkjob8m5Vo"
		}
	}
  	
  	
 2、响应报文示例

	{
    	"code":"0",
   		"message":"操作成功",
    	"data":
    	   {
    	     "status":"SUCCESS"
    	   }
  	}


返回：

参数名称  | 必填  | 参数说明
------------- | ------------- | :-------------
status | Y | 更新状态:成功SUCCESS,失败FAIL


### 3. 附录
#### 3.1 通用返回码
枚举值  | 枚举描述 
------------- | ------------- 
0 | 调用成功
100001 | 系统错误
100002 | json 参数格式错误
100003 | 签名验证失败
100004 | 合作商户ID不存在

#### 3.2 业务枚举
##### 3.2.1 币种类型
枚举值  | 枚举描述 
------------- | ------------- 
BTC  | BTC
ETH  | ETH
USDT | USDT






