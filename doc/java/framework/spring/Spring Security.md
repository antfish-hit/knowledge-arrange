# Spring Security

文档基于5.7.6版本撰写。

## Features

### Authentication

- `PasswordEncoder`接口用于将明文转换成密文。

- 推荐用单向的哈希函数对密码进行加密，且使用`salt`来避免[Rainbow Tables](https://zhuanlan.zhihu.com/p/105578739)问题。推荐的哈希函数有以下几种:
    - [bcrypt](https://docs.spring.io/spring-security/reference/5.7/features/authentication/password-storage.html#authentication-password-storage-bcrypt)
    - [PBKDF2](https://docs.spring.io/spring-security/reference/5.7/features/authentication/password-storage.html#authentication-password-storage-pbkdf2)
    - [scrypt](https://docs.spring.io/spring-security/reference/5.7/features/authentication/password-storage.html#authentication-password-storage-scrypt)
    - [argon2](https://docs.spring.io/spring-security/reference/5.7/features/authentication/password-storage.html#authentication-password-storage-argon2)

- Spring Security 5.0以前默认的`PasswordEncoder`是`NoopPasswordEncoder`(不对明文密码做任何处理)，考虑到密码的历史迁移问题，推荐使用`DelegatingPasswordEncoder`(5.0以后默认的`PasswordEncoder`来应对存在多种加密方式的情况:

```java
// 获取默认的DelegatingPasswordEncoderPasswordEncoder passwordEncoder =
PasswordEncoderFactories.createDelegatingPasswordEncoder();
// 源码
public static PasswordEncoder createDelegatingPasswordEncoder() (
    String encodingId = "bcrypt";
    Map<String，PasswordEncoder> encoders = new HashMap();
    encoders.put(encodingid, new BCryptPasswordEncoder());
    encoders.put("ldap",new LdapShaPasswordEncoder());
    encoders.put("MD4", new Md4PasswordEncoder());
    encoders.put("MD5", new MessageDigestPasswordEncoder("MD5"));
    encoders.put("noop", NoOpPasswordEncoder .getInstance());
    encoders.put("pbkdf2", new Pbkdf2PasswordEncoder());
    encoders.put("scrypt", new SCryptPasswordEncoder());
    encoders.put("SHA-1", new MessageDigestPasswordEncoder("SHA-1"));
    encoders.put("SHA-256"， new MessageDigestPasswordEncoder("SHA256"))
    encoders.put("sha256"，new StandardPasswordEncoder());
    encoders.put("argon2", new Argon2PasswordEncoder());
    return new DelegatingPasswordEncoder(encodingId, encoders);
}
```

- 使用`DelegatingPasswordEncoder`加密后的密码格式为`{id}encodedPassword`,`id`是用户自定义的加密函数类型标识:

```
# Example
{bcrypt}$2a$10$dXJ3SW6G7P50lGmMkkmwe.20cQQubK3.HZWzG3YB1tlRy.fqvM/BG
{noop}password
{pbkdf2}5d923b44a6d129f3ddf3e3c8d29412723dcbde72445e8ef6bf3b508fbf17fa4ed4d6b99ca763d8dc
{scrypt}$e0801$8bWJaSu2IKSn9Z9kM+TPXfOc/9bdYSrN1oD9qfVThWEwdRTnO7re7Ei+fUZRJ68k9lTyuTeUp4of4g24hHnazw==$OAOec05+bXxvuu/1qZ6NUR+xQYvYv7BeL1QxwRpY5Pc=
{sha256}97cde38028ad898ebc02e690819fa220e88c62e0699403e94fff291cfffaf8410849f27605abcbc0
```

### Protection Against Exploits

#### CSRF

- [Cross Site Request Forgery (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery)，跨站请求伪造，可通过以下两种方式防护:
- [Synchronizer Token Pattern](https://docs.spring.io/spring-security/reference/5.7/features/exploits/csrf.html#csrf-protection-stp)，添加token验证的机制。
- [Samesite Attribute](https://docs.spring.io/spring-security/reference/5.7/features/exploits/csrf.html#csrf-protection-ssa)，在Http response header中配置`samesite`以约束客户端在请求中对cookie的外理。

#### HTTP Headers

- Spring Security默认`HTTP Response Headers`如下:

```
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Content-Type-Options: nosniff
Strict-Transport-Security: max-age=31536000 ; includeSubDomains
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
```

- Spring Security默认禁用`Cache-Control`以避免用户敏感信息被窃取。
- `X-Content-Type-Options: nosniff`: 关闭资源嗅探以防范XSS攻击。
- `Strict-Transport-Security: max-age=3153600 ;includeSubDomains`: 要求浏览器只能通过HTTPS访问。
- `X-Frame-Options: DENY`: 禁止浏览器以frame的方式加载页面。
- `X-Xss-Protection: 1; mode=block`: 要求浏览器(仅限支持过滤XSS攻击的)打开XSS攻击过滤。
- `Content-Security-Policy: script-srchttps://trustedscripts.example.com; report-uri /csp-reportendpoint/`: 只允许加载指定来源的脚本，并将违规行为报告至指定服务接口。
- `Content-Security-Policy-Report-Only: script-src 'self'https://trustedscripts.example.com; report-uri /csp-reportendpoint/`: 将违规行为报告至指定服务接口但不限制加载违规脚本。
- `Referrer-Policy: same-origin`: 当用户在浏览器上点击一个链接时，会产生一个 HTTP 请求，用于获取新的页面内容，而在该请求的报头中，会包含一个`Referrer`，用以指定该请求是从哪个页面跳转页来的，常被用于分析用户来源等信息。[^1]
- `Clear-site-Data: "cache"，"cookies""storage"executionContexts"`: 用于当返回的响应包含该响应头时，清理浏览器存储的数据，例如cookie，local storage。

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throwsException
        http.
            headers(headers -> headers
                // 配置cache control(默认enable)
                .cacheControl(cache -> cache.disable())
                // 配置content type options((默认enable))
                .contentTypeOptions(contentTypeOptions -> contentTypeOptions.disable())
                // 配置HSTS(除preload以外别的与默认一样)
                .httpStrictTransportSecurity(hsts -> hsts
                    .includeSubDomains(true)
                    .preload(true)
                    .maxAgeInSeconds(31536009)
                )
                // 配置frame加载策略(默认DENY)
                .frameOptions(frameOptions -> frameOptions
                    .sameOrigin()
                )
                // 配置XSS防护(默认block为true)
                .xssProtection(xss -> xss
                    .block(false)
                )
                // 配置CSP(默认不启用)
                .contentSecurityPolicy(csp -> csp
                    .policyDirectives("script-src 'self' https://trustedscripts.example.com; object-src https://trustedplugins.example.com; report-uri /csp-report-endpoint/")
                    .reportOnly()
                )
                // 配置Referrer Policy(默认不启用)
                .referrerPolicy(referrer -> referrer
                    .policy(ReferrerPolicy.SAME_ORIGIN)
                )
                // 配置自定义响应头
                .addHeaderWriter(new StaticHeaderswriter("X-Custom-Security-Header", "header-value"))
                .logout((logout) -> logout
                    // 配置clear site Data(默认不启用)
                    .addLogoutHandler(new HeaderWriterLogoutHandler(newClearsiteDataHeaderwriter(CACHE，COOKIES)))
                );
        return http.build();
    }
}
```

- 可以使用`DelegatingRequestMatcherHeaderWriter`针对匹配的路径返回Header。

```java
@EnableWebSecurity
public class WebSecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception (
        RequestMatcher matcher = new AntPathRequestMatcher("/login");
        DelegatingRequestMatcherHeaderWriter headerwriter = new DelegatingRequestMatcherHeaderwriter(matcher, new XFrame0ptionsHeaderwriter());
        http
            .headers(headers -> headers
                .frameOptions(frameOptions -> frameOptions.disable())
                .addHeaderWriter(headerWriter)
            );
        return http.build();
    }
}
```

### Integrations

#### Cryptography

- `Encryptors`用于提供构建对称加密的工厂方法。
- 使用`Encryptors.stronger`


[^1]: [Referrer Policy 介绍](https://www.cnblogs.com/caixw/p/referrer-policy.html)