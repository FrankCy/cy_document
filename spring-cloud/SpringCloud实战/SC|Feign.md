# SC | Feign #
### 简介 ####
- Feign是一个声明式的Web Service客户端，简化了Web Service的开发。使用Feign只需要创建一个注解在类上，比如：FeignClient注解。
- Feign有```可插拔```注解，包括Feign注解的JAX-RS注解。
- Feign支持编码器和解码器，```Spring Cloud Open Feign```对Feign进行增强支持Spring MVC注解，可以```像Spring Web一样使用HttpMessageConverters```等。
- Feign是一个```声明式，模版化```的HTTP客户端。在Spring Cloud中使用Feign```可以做到使用HTTP请求访问远程服务```，就像调用本地函数一样，更```感知不到在HTTP请求```。

### 都有哪些特性？ ###
- 可插拔
  + 可插拔的注解支持，包括Feign注解和JAX-RS注解。<br/>
- HTTP编码／解码器
  + 支持可插拔的HTTP编码器和HTTP解码器。<br/>
- Hystrix／Fallback
  + 支持Hystrix和它的Fallback。<br/>
- Ribbon
  + 支持Ribbon的负载均衡。<br/>
- HTTP压缩
  + 支持HTTP请求和响应压缩。Feign是一个声明式的Web Servcie客户端，它的目的是让Web Service调用更加简单。```它整合了Ribbon和Hystrix，所以不需要开发者针对Feign对其整合```。Feign还提供了HTTP请求模版，通过编写简单的接口和注解，就可以定义好HTTP请求的参数，格式，地址等信息。Feign会完成代理HTTP的请求，使用时```只需要依赖注入Feign```，调用对应的方法传递参数即可。<br/>

[👋Open Feing](https://github.com/OpenFeign/feign)

