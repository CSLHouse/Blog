---
title: spring security全局跨域问题
tags:
  - 后端
  - springboot
categories:
  - 后端
comments: false    // 是否开启评论
date: 2019-08-28 19:01:59
---
# 写过滤器
```java
import javax.servlet.*;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class CorsConfig implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletResponse res = (HttpServletResponse) response;
        res.setHeader("Access-Control-Allow-Origin", "*");
        res.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE, PUT");
        res.setHeader("Access-Control-Max-Age", "3600");
        res.setHeader("Access-Control-Allow-Headers", "Authorization, Content-Type, Accept, x-requested-with, Cache-Control");
        chain.doFilter(request, res);
        }
        @Override
    public void destroy() {
    }
}

```
#  将跨域的过滤器置于 spring security filter chain 之前
  在继承WebSecurityConfigurerAdapter的类中configure方法下添加：
  ```java
  @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                ......
                .cors().and()
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                ......
    }
  ```

转载：[Spring Security 环境下 CORS 跨域失效问题](http://www.jetchen.cn/spring-security-cors/)
