---
title: SpringBoot
date: 2018-12-28 21:18:12
tags: [java]
category: java基础
---


# Spring Java 配置方式

## @Configuration 和 @Bean

1. `@Configuration` 作用于类，相当于 xml 的配置文件
2. `@Bean` 作用在方法上，相当于 xml 中的 bean 标签

java 配置:

```java
@Configuration  // 表示这个是一个 Spring 配置，相当于 xml
@ComponentScan(basePackages = "com.springboot.hello")   // 扫描包
public class SpringConfig {

    @Bean // bean 标签, 函数名对应 xml 中的 id
    public UserDao userDao() {
        return new UserDao();
    }

}
```

读取外部资源文件：

```java
@Configuration  // 表示这个是一个 Spring 配置，相当于 xml
@ComponentScan(basePackages = "com.springboot.hello")
@PropertySource(value = {"classpath:jdbc.properties"})
public class SpringConfig {
    @Value("${jdbc.url}")
    private String jdbcUrl;
}
// 忽略配置文件不存在
//@PropertySource(value = {"classpath:jdbc.properties"}, ignoreResourceNotFound = true)
```

# Spring Boot

作用：简化 java 开发过程中的配置

优点：

1. 快速构建项目
2. 对主流开发框架无配置集成
3. 项目可独立运行，无须外部依赖 Servlet 容器
4. 提供运行时的应用监控
5. 极大地提高了开发，部署效率
6. 与云计算的天然集成

# SpringBoot

## 配置

`parent`: 内部包含大量默认的配置

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

`web` 支持：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

程序运行插件：

```xml
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
```

>上面都没有版本号，版本号由 parent 中定义。

## 入口类，@SpringBootApplication

`@SpringBootApplication` 作为核心注解，内部包含一系列注解。

包含：

1. `@SpringBootConfiguration`
2. `@EnableAutoConfiguration`: 根据项目中的依赖包，自动设置相关配置
3. `@ComponentScan`: 默认扫描当前包与子包

### 依赖包自动化配置

查看 SpringBoot 支持哪些自动化设置可以从 `spring-boot-autoconfigure-2.1.1.RELEASE.jar` 下找。

设置排除指定包的自动化设置：

```java
@SpringBootApplication(exclude = {RedisAutoConfiguration.class})
```

## 自定义 Banner

找个网址，生成 Banner.txt, 放在 `resources` 下。

## 全局配置文件

`application.properties`:

```
# 设置默认端口
server.port=8088

# 修改进入 DispatcherServlet 规则
```

## Starter pom

例子：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

`starter` 配置，由官方提供，包含相关的各种配置


## xml 配置

在必须使用 xml 配置的时候：

```java
@ImportResource({"classpath:some-content.xml","classpath:somanother-content.xml"})
```

## 日志

设置日志级别：

```
logging.level.org.springframework=DEBUG
```

## Spring Boot 自动配置的原理

SpringBoot 在 SpringApplication 对象实力化时加载 `META-INF/spring.factories` 文件，将配置文件中的配置载入到 Spring 容器。

通过代码读取配置文件，实力化各种对象。

## 静态文件配置

## 设置字符集

>默认就是 UTF-8， 通常不用设置

```java
    @Bean
    public StringHttpMessageConverter stringHttpMessageConverter(){
        StringHttpMessageConverter stringHttpMessageConverter = new StringHttpMessageConverter(Charset.forName("UTF-8"));
        return stringHttpMessageConverter;
    }
```

## 添加拦截器

扩展配置

```java
@Configuration
@EnableWebMvc
public class MvcConfig implements WebMvcConfigurer {

    /**
     * 添加自定义拦截器
     *
     * @param registry
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new HandlerInterceptor() {
            @Override
            public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
                return false;
            }

            @Override
            public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

            }

            @Override
            public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

            }
        }).addPathPatterns("/**");
    }
}
```

## 事务

添加依赖：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
```

使用 `@Transactional`, 加在 类 或者 方法上。

## SpringMVC 配置












