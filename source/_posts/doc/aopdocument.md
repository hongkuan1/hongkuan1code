---
title: AOP 切面编程
category: 文档
tag: 学习
sticky: true
abbrlink: 61498
date: 2024-10-07 10:00:00
---


# AOP 切面的问题

1. AOP 切面是基于代理机制的。确保 SvgAnalysisService 的 handleProcess 方法确实是通过代理对象调用的。问题在于，startAnalysis 直接调用了 handleProcess，这意味着 AOP 可能不会生效，因为同一个类中的自调用不会触发 AOP。


分析逻辑 SvgAnalysisService.java
```java
package com.example.aopdemo.service;

import com.example.aopdemo.annotation.ReplaceMethod;
import com.example.aopdemo.util.AnalysisTreeUtil;
import org.springframework.aop.framework.AopContext;
import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.Map;

@Service
public class SvgAnalysisService {



    public void startAnalysis(String code, String message) {

        HashMap<String, String> hashMap = new HashMap<>();
        // 使用代理对象来调用 handleProcess，从而触发 AOP
        handleProcess(code, message,hashMap);
    }

    @ReplaceMethod
    public void handleProcess(String code, String message, Map<String,String> map)
    {
        String codeValue = "hello";
        String messageValue = ",world!";

        map.put(code,codeValue);
        map.put(message,messageValue );

        AnalysisTreeUtil.analysis(code,message,map);
    }
}
```
分析切面 AnalysisAspect.java
```java
package com.example.aopdemo.aspect;

import com.example.aopdemo.util.AnalysisContext;
import com.example.aopdemo.util.AnalysisTreeUtilSZ;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

import java.util.Map;

@Aspect
@Component
public class AnalysisAspect {

    @Around("@annotation(com.example.aopdemo.annotation.ReplaceMethod)")
    public Object useSZAnalysis(ProceedingJoinPoint joinPoint) throws Throwable {
        Object[] args = joinPoint.getArgs();  // 获取方法的参数

        try {
            // 检查是否有三个参数，第三个参数是 Map<String, String>
            if (args != null && args.length == 3 &&
                    args[0] instanceof String &&
                    args[1] instanceof String &&
                    args[2] instanceof Map) {

                String code = (String) args[0];
                String message = (String) args[1];
                Map<String, String> map = (Map<String, String>) args[2];

                // 从 ThreadLocal 中获取中间结果
                String param1 = (String) AnalysisContext.get("codeValue");
                String param2 = (String) AnalysisContext.get("messageValue");

                // 使用苏州分析方法
                System.out.println("使用苏州分析方法");
                AnalysisTreeUtilSZ.analysis(code, message, map);

                // 返回 map，结束方法的执行，不再执行原始方法
                return map;
            }

            // 如果参数不符合要求，继续执行原方法
            return joinPoint.proceed();
        } finally {
            // 在最终执行时，清理 ThreadLocal
            AnalysisContext.clear();
        }
    }
}
```
自定义注解 ReplaceMethod.java
```java
package com.example.aopdemo.annotation;


import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)  // 该注解将用于方法上
@Retention(RetentionPolicy.RUNTIME)  // 运行时保留，便于 AOP 拦截
public @interface ReplaceMethod {
}

```

- 遇到的问题是典型的 自调用问题，即在类的内部直接调用了另一个标记了 AOP 注解的方法。因为 Spring AOP 的代理机制是在 Spring 容器管理的代理对象上进行的，而在同一个类中自调用（如你图片中所示的 handleProcess 调用）并不会通过 Spring 代理，因此不会触发 AOP 切面。

## 解决方案：

1. 将 AOP 方法移动到另一个 Service 类
    - 创建新的 HelperService 类：
```java
    import org.springframework.stereotype.Service;
import java.util.Map;

@Service
public class SvgAnalysisHelperService {

    @ReplaceMethod
    public void handleProcess(String code, String message, Map<String,String> map) {
        String codeValue = "hello";
        String messageValue = ",world!";

        map.put(code, codeValue);
        map.put(message, messageValue);

        AnalysisTreeUtil.analysis(code, message, map);
    }
}
```
2.	在原来的 SvgAnalysisService 中注入 SvgAnalysisHelperService：
    - SvgAnalysisService
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.HashMap;
import java.util.Map;

@Service
public class SvgAnalysisService {

    @Autowired
    private SvgAnalysisHelperService svgAnalysisHelperService;

    public void startAnalysis(String code, String message) {
        HashMap<String, String> hashMap = new HashMap<>();
        // 通过新服务调用 AOP 方法
        svgAnalysisHelperService.handleProcess(code, message, hashMap);
    }
}
```