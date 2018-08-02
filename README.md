##导读：
	场外交易面板在合作方的嵌入连接是 标题1 中的入口链接，用户在合作方页面点击入口链接时，合作方先通过后端调用 标题2 中的API建立授权登录接口 ,场外交易服务将提供一个授权hook返回给合作方，合作方通过 授权hook 和 source类型发送跳转请求 ，即请求标题3 中的跳转URL



1 场外交易面板入口连接
   

 URL:https://otc.***.com

* 号处为合作方的根域名



2 建立场外交易面板连接接口
URL: https://api.aiwith.com/cooperation/connect


签名规则

1、key固定不变
key = weroqwetivnd

2、将key全部小写后，先用sha1加密，再用md5加密
newkey = md5(sha1(key.toLowerCase()))

3、所有参数名称做a-z排序后，与key=newkey做拼接（注意！这里的newkey不是字符串，是上面 2 中加密过的key）
sign = md5(key=newkey&isReal=true&phone=18600004444&sourceType=coinx&userName=robin)

4、得到签名sign


备注：

1 返回的hook 的有效时间是 20s

2 请求会有ip 白名单限制，后续沟通添加

3 用户在交易所面板登录状态下点击场外入口时由交易所后台发起调用接口

3 跳转场外交易接口
    交易所提供场外的二级域名 CNAME 到 iOTC 交易面板服务，场外交易的最终链接格式如下：

https://otc.***.com?hook=uckDPmq1hpbR716349549b58&source=coinx

