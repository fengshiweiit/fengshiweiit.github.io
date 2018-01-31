---
title: SpringCloud
date: 2018-01-23 14:41:03
tags:
---
# <center>SpringCloud入门</center>
---
## 服务注册中心Eureka
---
##### 引入依赖
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka-server</artifactId>
    </dependency>

    <dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
##### 配置文件（此处用的是properties）
    server:
      port: 1888
    eureka:
      client:
        //设置不使用客户度，只用eureka提供服务注册中心
        register-with-eureka: false
        fetch-registry: false
        serviceUrl:
        defaultZone: http://localhost:1888/eureka/
---

## 服务提供者provider
---
##### 引入依赖
    同上，配置eureka的maven依赖

##### 配置文件
    server:
        port: 1990
    //配置注册中心的url，将此服务注册到eureka
    eureka:
        client:
            serviceUrl:
                defaultZone: http://127.0.0.1:1888/eureka/
    //配置服务的名称
    spring:
        application:
            name: provider

## 服务消费者consumer
---
##### 引入依赖
    同上，配置eureka的maven依赖
##### 配置文件
    server:
        port: 1990
    //配置注册中心的url，将此服务注册到eureka
    eureka:
        client:
            serviceUrl:
                defaultZone: http://127.0.0.1:1888/eureka/
    //配置服务的名称
    spring:
        application:
            name: consumer
---
## ribbon实现负载均衡
---
##### 添加依赖
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-ribbon</artifactId>
    </dependency>
##### 设置restTemplate的负载均衡
    @Bean
    @LoadBalanced // 注解开启均衡负载能力
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

##### 配置文件(配置连接和处理超时)
    ribbon:
        //连接超时
        ConnectTimeout: 10000
        //处理超时
        ReadTimeout: 30000

## feign默认实现了负载均衡和断路器功能（这里我理解为整合了ribbon,hystrix和restTemplate）
---
##### 添加依赖
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-feign</artifactId>
    </dependency>
##### feign调用微服务
    //配置需要调用的微服务的名称，配置断路器熔断后的默认方法
    @FeignClient(value = "provider", fallback = HystrixDemo.class)
    public interface FeignClientDemo {
        //配置调用的服务的方法
        @RequestMapping(method = RequestMethod.GET,value = "/plus")
        void plus();
    }
---
    //配置熔断的方法
    public class HystrixDemo implements FeignClientDemo{
        @Override
        public void plus() {
            System.out.println(999);
            return;
        }
    }
##### 配置文件
    feign:
        hystrix:
            enabled: true
