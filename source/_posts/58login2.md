---
title: Java 模拟58登录(二) 实现
date: 2018-01-28 14:25:40
categories:
    - Java
tags: Java 
---
# Java 模拟58登录(二) 实现


> 实习期间需要做一个模拟58登录然后爬取简历，之前已经写了一篇分析，现在具体实现过程

<!-- more -->
## 实现

### 流程
根据之前的分析可以得出58登录的大致流程如下
> - 发送Get请求（data？）， 获取 token
-   发送Get请求（getcode？）， 请求发送手机验证码， 同时获得一个tokencode
-   发送Get请求（login？）， 输入手机号，动态码以及其他相关参数来发送登录请求
-   登录成功

### 预处理
除了上述所说的步骤外，还有很多需要处理的底层实现细节，比如解析Cookie， 构造必须的参数

#### 设置必须参数

- FINGERPRINT 和FINGERPRINT2是网页登录的时候直接抓取的，我试过随机修改后面几个数字依旧可以登录
- JQUERYSTR 只是jquery调用时候自动生成的参数，为了不出现什么问题我也加上了
- initTime initTime表示页面加载时间，后面加密时候需要用到的时间戳这个参数，这里所有需要的时间戳都是(需要的时间戳时刻b-initTime+ const time 1411093327735L)
- TOKENCODE 和 TOKEN 是调用的时候获取的参数


``` Java
	private String FRONTEND_URL = "http://passport.58.com/frontend/data?";
	private String TOKENCODE_URL = "http://passport.58.com/mobile/getcode?";
	private String LOGIN_URL = "https://passport.58.com/mobile/pc/login?";
	private String FINGERPRINT = "421A592E9D98DC7C0711A36033A582E84360ED23C621CCE3_011";
	private String FIGNERPRINT2 = "zh-CN|24|1|4|1600_900|1600_860|-480|1|1|1|undefined|undefined|"
			+ "unknown|Win64|unspecified|1|false|false|false|true|false|"
			+ "0_true_true|d41d8cd98f00b204e9800998ecf8427F|b01de87fcefd32c68d348cd9d18b62d9";
	private String JQUERYSTR = "jQuery183025066063002634587_" + getNow();
	private long initTime = (new Date()).getTime();
	private String TOKENCODE = "";
	private String TOKEN = "";
```
#### 解析Cookie
这里实际写的时候遇到了坑点, 最初底层实现Cookie是参考[stackoverflow][1]中的那种实现
但是如果Cookie value中含有"=",解析就是失败，刚好58的session Cookie 中含有这个！！！我也是失败很多次才知道


```
	protected CookieStore cookieStore = new BasicCookieStore();

	public void setCookieStore(HttpResponse httpResponse) {

		Header[] headers = httpResponse.getHeaders("Set-Cookie");
		if (headers != null && headers.length > 0) {
			for (int i = 0; i < headers.length; i++) {
				String setCookie = headers[i].getValue();
				try {
					BasicClientCookie cookie = this.parseRawCookie(setCookie);
					if (!cookie.isExpired(new Date())) {
						this.cookieStore.addCookie(cookie);
					}
				} catch (Exception e) {
					// do nothing
				}
			}
		}
		this.cookieStore.clearExpired(new Date());
	}

//使用java.net.HttpCookie自带的解析，然后手动转换成BasicClientCookie
//这里java.net.HttpCookie会有些默认行为需要注意
	protected BasicClientCookie parseRawCookie(String rawCookie) throws Exception {
		List<HttpCookie> cookies = HttpCookie.parse(rawCookie);
		if (cookies.size() < 1)
			return null;
		HttpCookie httpCookie = cookies.get(0);
		BasicClientCookie cookie = new BasicClientCookie(httpCookie.getName(), httpCookie.getValue());
		if (httpCookie.getMaxAge() >= 0) {
			Date expiryDate = new Date(System.currentTimeMillis() + httpCookie.getMaxAge() * 1000);
			cookie.setExpiryDate(expiryDate);
		}
		if (httpCookie.getDomain() != null)
			cookie.setDomain(httpCookie.getDomain());
		if (httpCookie.getPath() != null)
			cookie.setPath(httpCookie.getPath());
		if (httpCookie.getComment() != null)
			cookie.setComment(httpCookie.getComment());
		cookie.setSecure(httpCookie.getSecure());
		return cookie;
	}
```
#### 解析Response String
返回的Response类似于下面，需要提取其中的键值对
> jQuery18305434571389149673_1515995590691({"code":0,"data":{"remainTime":0,"token":"7pjUnv6fhBONHh7A_4HrN_2rH4is-gC8","rsaModulus":"xxx固定值xxx",
"rsaExponent":"010001","totalTime":60,"mobile":"手机号"},"msg":""})


```
	protected String getResponseParameter(String response, String parameterName) {
		String result = response.replaceAll(",|\"|:", " ");
		StringTokenizer tokenizer = new StringTokenizer(result, " ");
		while (tokenizer.hasMoreTokens() && !tokenizer.nextToken().equals(parameterName)) {
		}
		String token = tokenizer.nextToken();
		return token;
	}
```

#### 发送Get请求模块
因为后面很多都用到get 请求，所以这里封装一下
``` Java
	public String doGet(String url, String host) throws Exception {
		CloseableHttpClient client = this.createHttpClient();
		HttpGet get = new HttpGet(url);
		get.addHeader("Connection", "keep-alive");
		get.addHeader("accept",
				"text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8");
		get.addHeader("accept-encoding", "gzip, deflate, br");
		get.addHeader("accept-language", "en-US,en;q=0.9");
		get.addHeader("upgrade-insecure-requests", "1");
		get.addHeader("user-agent",
				"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36");
		get.addHeader("Host", host);
		CloseableHttpResponse response = client.execute(get);
		if (response == null) {
			throw new Exception("network error, unable to connect to 51job");
		}
		setCookieStore(response);
		HttpEntity entity = response.getEntity();
		InputStream stream = entity.getContent();
		String result = IOUtils.toString(stream, "utf-8");

		IOUtils.closeQuietly(stream);
		EntityUtils.consumeQuietly(entity);
		HttpClientUtils.closeQuietly(client);
		return result;
	}
```

#### 加密字符串
QuanzhouJSUtil是一个调用js的类
``` Java
	protected String encryptStr(String str) {
		Long timeSpan = 1411093327735L;
		Long randomLong = timeSpan + (new Date()).getTime() - initTime;
		String timeSign = String.valueOf(randomLong);
		String passwd = timeSign + str;
		String encryptPwd = QuanzhouJSUtil.getEncryptString(passwd);
		return encryptPwd;
	}
```
### 发送动态码

```
	public void getPhoneCode(String phoneNumber) throws Exception {

		// 获得token,然后用token作为参数获取tokenCode, 然后发送验证码登录时候需要用到tokenCode和token
		String result = doGet(FRONTEND_URL + getJQueryCallBackStr(), "passport.58.com");
		TOKEN = getResponseParameter(result, "token");

		// 获取token code
		String enPhone = encryptStr(phoneNumber);
		String tokenCodeURL = TOKENCODE_URL + "mobile=" + enPhone + "&validcode=&source=pc-login&vcodekey=&token="
				+ TOKEN + "&voicetype=0&codetype=0&fingerprint=" + FINGERPRINT + "&" + getJQueryCallBackStr() + "&path="
				+ "http://my.58.com";
		result = doGet(tokenCodeURL, "passport.58.com");
		TOKENCODE = getResponseParameter(result, "tokencode");

	}
```

### 发送动态码

```
	public void getPhoneCode(String phoneNumber) throws Exception {

		// 获得token,然后用token作为参数获取tokenCode, 然后发送验证码登录时候需要用到tokenCode和token
		String result = doGet(FRONTEND_URL + getJQueryCallBackStr(), "passport.58.com");
		TOKEN = getResponseParameter(result, "token");

		// 获取token code
		String enPhone = encryptStr(phoneNumber);
		String tokenCodeURL = TOKENCODE_URL + "mobile=" + enPhone + "&validcode=&source=pc-login&vcodekey=&token="
				+ TOKEN + "&voicetype=0&codetype=0&fingerprint=" + FINGERPRINT + "&" + getJQueryCallBackStr() + "&path="
				+ "http://my.58.com";
		result = doGet(tokenCodeURL, "passport.58.com");
		TOKENCODE = getResponseParameter(result, "tokencode");

	}
	
```

### 测试登录
```
	@Test
	public void testLogin58(){
		preLogin();
		String phoneNum = "";
		String mobileCode = "";
		System.out.println("请输入你的手机号码:");
		Scanner scanner = new Scanner(System.in);
		phoneNum = scanner.nextLine();
		try {
			getPhoneCode(phoneNum);
			System.out.println("请输入你的手机验证码:");
			mobileCode = scanner.nextLine();
			loginJob58(phoneNum, mobileCode);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		//add some code to do
		
		
		scanner.close();
		
	}
```


  [1]: https://stackoverflow.com/questions/5574530/how-to-parse-a-cookie-string
