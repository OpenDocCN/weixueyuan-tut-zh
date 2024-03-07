# 服务提供方进行调用认证

> 原文：[`c.biancheng.net/view/5503.html`](http://c.biancheng.net/view/5503.html)

服务提供方就是 provider。服务消费方消费接口时，provider 需要对其进行身份验证，验证通过才可以让它消费接口。这个过程中用到的过滤器可以写在 Common 包中，凡是服务提供方都需要用到。

认证过滤器的代码如下所示。

```

/**
* API 调用权限控制
*/
public class HttpBasicAuthorizeFilter implements Filter {
    JWTUtils jwtUtils = JWTUtils.getInstance();

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        httpResponse.setCharacterEncoding("UTF-8");
        httpResponse.setContentType("application/json; charset=utf-8");
        String auth = httpRequest.getHeader("Authorization");
        // 验证 TOKEN
        if (!StringUtils.hasText(auth)) {
            PrintWriter print = httpResponse.getWriter();
            print.write(JsonUtils
                    .toJson(ResponseData.fail("非法请求【缺少 Authorization 信息 ", ResponseCode.NO_AUTH_CODE.getCode())));
            return;
        }
        JWTUtils.JWTResult jwt = jwtUtils.checkToken(auth);
        if (!jwt.isStatus()) {
            PrintWriter print = httpResponse.getWriter();
            print.write(JsonUtils.toJson(ResponseData.fail(jwt.getMsg(), jwt.getCode())));
            return;
        }
        chain.doFilter(httpRequest, response);
    }

    @Override
    public void destroy() {
    }
}
```

在上述 Filter 类中对所有请求进行拦截，其调用之前写好的 JwtUtils 来检查 Token 是否合法，合法则放行，不合法则拦截并给出友好提示。

验证用的 Filter 类写好了，接下来就是在需要拦截请求进行验证的服务中注册 Filter。如果不需要验证那就不注册，对业务功能无任何影响。在 Spring Boot 中注册 Filter 是非常简单、方便的，代码如下所示。

```

/**
* 过滤器配置
**/
@Configuration
public class FilterConfig {
    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean registrationBean = new FilterRegistrationBean();
        HttpBasicAuthorizeFilter httpBasicFilter = new HttpBasicAuthorizeFilter();
        registrationBean.setFilter(httpBasicFilter);
        List<String> urlPatterns = new ArrayList<String>(1);
        urlPatterns.add("/*");
        registrationBean.setUrlPatterns(urlPatterns);
        return registrationBean;
    }
}
```