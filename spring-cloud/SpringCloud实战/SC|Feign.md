# SC | Feign #
### 简介 ####
- Feign是一个声明式的Web Service客户端，简化了Web Service的开发。使用Feign只需要创建一个注解在类上，比如：FeignClient注解。
- Feign有```可插拔```注解，包括Feign注解的JAX-RS注解。
- Feign支持编码器和解码器，```Spring Cloud Open Feign```对Feign进行增强支持Spring MVC注解，可以```像Spring Web一样使用HttpMessageConverters```等。
- Feign是一个```声明式，模版化```的HTTP客户端。在Spring Cloud中使用Feign```可以做到使用HTTP请求访问远程服务```，就像调用本地函数一样，更```感知不到在HTTP请求```。

### 都有哪些特性？ ###
- 可插拔
  + 可插拔的注解支持，包括Feign注解和JAX-RS注解。
- HTTP编码／解码器
  + 支持可插拔的HTTP编码器和HTTP解码器。
- Hystrix／Fallback
  + 支持Hystrix和它的Fallback。
- Ribbon
  + 支持Ribbon的负载均衡。
- HTTP压缩
  + 支持HTTP请求和响应压缩。Feign是一个声明式的Web Servcie客户端，它的目的是让Web Service调用更加简单。```它整合了Ribbon和Hystrix，所以不需要开发者针对Feign对其整合```。Feign还提供了HTTP请求模版，通过编写简单的接口和注解，就可以定义好HTTP请求的参数，格式，地址等信息。Feign会完成代理HTTP的请求，使用时```只需要依赖注入Feign```，调用对应的方法传递参数即可。<br/>

[👋 Open Feing](https://github.com/OpenFeign/feign) <br/>
[👋 Spring Cloud Open Feing](https://github.com/spring-cloud/spring-cloud-openfeign)

### 注解 ###
- @EnableFeignClients
```
@EnableFeignClients
@SpringBootApplication
public class CloudClientApplication {

	public static void main(String[] args) {
		SpringApplication.run(CloudClientApplication.class, args);
	}
}

```
@EnableFeignClients表示当程序启动时，会进行包扫描，扫描所有带@FeignClient的注解的类并进行处理。

- @FeignClient
```
@FeignClient(name = "cloud-data-server", fallback= UserClientFallback.class)
public interface DataService {

    @RequestMapping(value = "/insertCompany", method = RequestMethod.GET)
    String insertCompany(@RequestParam String par1);

}

```

@FeignClient注解指定请求服务名为"cloud-data-server"的服务器，根据指定的RequestMapping将请求转换为```http://localhost:8099/cloud-data-server/insertCompany?par1=xxxx```


### Feign 工作原理 ###

### Feign 实战 ###

#### Feign Post ／ Get 多参数传递 ####
- 传递参数方式
  + 将POJO拆成一个一个独立的属性放在方法参数里 如：```testMethod(String para1, String para2 ... )```
  + 把方法参数放入Map传递 如：```testMethod(Map<String, String> map)```
  + 使用GET传递@RequestBody，但```此方法违反Restful规范```

好那我们该怎么解决呢😖

- Feign最佳实践<br/>
（通过Feign拦截器方式处理）
  +