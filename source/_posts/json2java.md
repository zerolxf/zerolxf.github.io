---
title: JSON 格式转换成Java对象
date: 2018-01-28 14:06:53
tags: jackson java
---

# JSON 格式转换成Java对象

---

<!-- more -->
[源代码][1]
添加依赖到pom.xml
```
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.6.3</version>
</dependency>
```

需要转换的对象是`ObjectC`和`ObjectD`,其中`ObjectD`的一个成员变量是`List<ObjectC>`.
jackson能够递归转换,让json中包含json的这种也能够转为相应的Java对象

``` Java
//ObjectC.java
package com.workstation;

import com.fasterxml.jackson.annotation.JsonProperty;

public class ObjectC {

	@JsonProperty ("Message")
	String message;

	public String getMessage() {
		return message;
	}

	public void setMessage(String message) {
		this.message = message;
	}
	
	//JSON 中message也会绑定到message成员变量
	@JsonProperty ("message")
	public String _getMessage() {
		return message;
	}

	@JsonProperty ("message")
	public void _setMessage(String message) {
		this.message = message;
	}
	
}
```
如上所写，ObjectC对象和Json中的"Message"和"message"都绑定到了message成员变量
```
//ObjectD.java
package com.workstation;

import java.util.List;

import com.fasterxml.jackson.annotation.JsonProperty;

public class ObjectD {

	List<ObjectC> list;

	@JsonProperty ("List")
	public List<ObjectC> getList() {
		return list;
	}

    //能够像spring一样,自动完成ObjectC的转换
	@JsonProperty ("List")
	public void setList(List<ObjectC> list) {
		this.list = list;
	}
	
}

```
测试代码
```
package com.workstation;

import java.io.IOException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Map.Entry;

import org.junit.Test;

import com.fasterxml.jackson.core.JsonParseException;
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.JsonMappingException;
import com.fasterxml.jackson.databind.ObjectMapper;

public class testJsonToObject {

	@Test
	public void test() throws JsonParseException, JsonMappingException, IOException {
		String jsonString = "{    \"message\":\"Hello World\"}";
		ObjectC objectC = (new ObjectMapper()).readValue(jsonString, ObjectC.class);
		System.out.println(objectC.getMessage());
		
		
//		{
//			"List": [
//			{"message":"hello A"},
//			{"Message":"hello B"}
//			]
//			}
		String jsonListString = "{\r\n" + 
				"\"List\": [\r\n" + 
				"{\"message\":\"hello A\"},\r\n" + 
				"{\"Message\":\"hello B\"}\r\n" + 
				"]\r\n" + 
				"}";
		ObjectD objectD = (new ObjectMapper()).readValue(jsonListString, ObjectD.class);
		List<ObjectC> list = objectD.getList();
		for(int i = 0; i < list.size(); ++i) {
			System.out.println(list.get(i).getMessage());
		}
		
		
		//jackson结合TypeReference
		Map<String, String> map = new HashMap<String, String>();
		map = (new ObjectMapper()).readValue(jsonString, new TypeReference<Map<String, String>>(){});
		for(Entry<String, String> entry : map.entrySet()) {
			System.out.println(entry.getKey()+":"+entry.getValue());
		}
		
	}
	
}


}

```
输出
```
Hello World
hello A
hello B
message:Hello World
```
如上所示,jackson成功读取了含有list的json对象
jsackson还可以和TypeReference一起使用,这样对某些特定情况下就不用专门新建一个类来读取相关东西了

[1]: https://github.com/zerolxf/MyDemo

