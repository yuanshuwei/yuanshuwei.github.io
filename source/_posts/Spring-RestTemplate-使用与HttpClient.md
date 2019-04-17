---
title: Spring RestTemplate 使用与HttpClient
date: 2018-03-12 17:58:30
tags: RestTemplate
categories: Spring
---
## 1.引入
java请求网络资源通常用`HttpClient`等，Spring封装了库，提供更为简洁的资源请求方式`RestTemplate`。

<!--more-->
## 2.简单GET请求(HttpClient/RestTemplate)
 
### HttpClient

```java
 private static final CloseableHttpClient httpclient = HttpClients.createDefault();
 HttpGet httpget = new HttpGet(url);
 CloseableHttpResponse response = httpclient.execute(httpget);

 String result = null;
 HttpEntity entity = response.getEntity();
 if (entity != null) {
     result = EntityUtils.toString(entity);
 }
 System.out.println(result);
```
### RestTemplate

```java
//建议以注入方式使用
RestTemplate restTemplate=new RestTemplate();
String result = restTemplate.getForObject(url, String.class);
System.out.println(result);
```
对比下，明显RestTemplate方式更加优雅简洁；并提供方法将返回值映射为指定对象。

## 3.Spring注入方式使用

```java
@Configuration  
public class RestClientConfig {  
  
    @Bean  
    public RestTemplate restTemplate() {  
        RestTemplate restTemplate = new RestTemplate();
        return restTemplate;  
    }  
  
}  
```

```java
@Service  
public class UserService {  
  
    @Autowired  
    private RestTemplate restTemplate;  
  
    // ...  
  
}  
```

## 4.RestTemplate的其他方法
![这里写图片描述](http://img.blog.csdn.net/20180312173132182?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXN3MTEzMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
- `getForObject`: 发送get请求，结果封装为指定对象。 提供class指定
- `getForEntity`: 发送get请求，结果为Entity类型。
- `postForObject`: 发送post请求，结果封装为指定对象
- `put`:
- `delete`:
- `exchange`:通用执行方法
...
#### exchange发送POST，APPLICATION_JSON示例
```java
 HttpHeaders headers = new HttpHeaders();
 headers.setContentType(MediaType.APPLICATION_JSON);
 HttpEntity<String> entity = new HttpEntity<>(new Gson().toJson(requestData), headers);
 ResponseEntity<String> response = restTemplate
                .exchange(url, HttpMethod.POST, entity, String.class);
```

## 5.RestTemplate的请求301，302问题
#### 描述： 
使用过程发现，遇到301请求不能自动转发问题。

#### 解决：
想起了使用HttpClient过程，实现请求自动转发需要创建HttpClient时设置重定向策略。
翻阅资料发现了这一段。

```java
HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory();
HttpClient httpClient = HttpClientBuilder.create()
         .setRedirectStrategy(new LaxRedirectStrategy())
         .build();
 factory.setHttpClient(httpClient);
 restTemplate.setRequestFactory(factory);
```
于是问题解决。

#### 分析：
查阅源码可见，
`RestTemplate`执行请求时。创建(createRequest)了一个`HttpUriRequest`来完成请求。`createRequest`具体在`ClientHttpRequestFactory` 实现，而上面代码中的`HttpComponentsClientHttpRequestFactory`，这个工厂在创建请求时正好用的apache下 `httpClient`的参数。由此可见。上面代码底层是以HttpClient方式发送的请求。

## 6.RestTemplate全部是以HttpClient方式请求的吗？
通过源码不难看到，默认`RestTemplate`使用的是`SimpleClientHttpRequestFactory`工厂。
追踪可见，默认它是以java.net下的`HttpURLConnection`方式发起的请求。
所以`RestTemplate`是支持多种方式发起请求的。
查看该工程的实现类可知，支持包括HttpClient, OkHttp等方式，如下图
![这里写图片描述](http://img.blog.csdn.net/20180312175218751?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXN3MTEzMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)