---
title: shardingshpere5.5.0集成nacos作为配置中心
date: 2024-10-08 11:13:51
tags: java shardingshpere nacos
---

### shardingshpere5.5.0去除了nacos作为配置中心的代码参考源码中相对路径和绝对路径配置进行修改


```
#配置数据源为自定义前缀nacos
spring:
  datasource:
    driver-class-name: org.apache.shardingsphere.driver.ShardingSphereDriver
    url: jdbc:shardingsphere:nacos:${spring.application.name}.yaml

```

```
/**
 *  自定义配置数据源为nacos获取配置信息
 *  shardingsphere中的rules均为!开头，spring不能解析会报错，先将叹号开头的配置以井号开头将其注释掉，这里再替换回来进行解析
 *  nacos读取出来的每行以\n结尾和shardingsphere解析以系统分隔符结尾不一致也替换一下
 */
public final class SpringNacosURLLoader implements ShardingSphereURLLoader {

    private static final Long TIMEOUT = 3000L;

    @Override
    @SneakyThrows(BizException.class)
    public String load(final String configurationSubject, final Properties queryProps) {
        NacosConfigManager nacosConfigManager = SpringContextHolder.getApplicationContext().getBean(NacosConfigManager.class);
        try {
            String content = nacosConfigManager.getConfigService().getConfig(configurationSubject, nacosConfigManager.getNacosConfigProperties().getGroup(), TIMEOUT);
            return content.replace("\n", System.lineSeparator()).replace("#", "");
        } catch (NacosException e) {
            throw new BizException(e);
        }
    }

    @Override
    public String getType() {
        return "nacos:";
    }

}
```

```
mode:
  type: Standalone
  repository:
    type: JDBC
    props:
      path: demo
dataSources:
  ds_0:
    dataSourceClassName: com.zaxxer.hikari.HikariDataSource
    driverClassName: com.mysql.cj.jdbc.Driver
    jdbcUrl: jdbc:mysql://localhost:3306/test?serverTimezone=UTC&useSSL=false&useUnicode=true&characterEncoding=UTF-8&allowPublicKeyRetrieval=true
    username: root
    password: 123456
    maxPoolSize: 10
  ds_1:
    dataSourceClassName: com.zaxxer.hikari.HikariDataSource
    driverClassName: com.mysql.cj.jdbc.Driver
    jdbcUrl: jdbc:mysql://localhost:3306/test?serverTimezone=UTC&useSSL=false&useUnicode=true&characterEncoding=UTF-8&allowPublicKeyRetrieval=true
    username: root
    password: 123456
    maxPoolSize: 10
rules:
    ##注意这里将官方的规则中以叹号开头的前加#以便spring能够不报错
  - #!SINGLE
    tables:
      - "*.*"
    ##注意这里将官方的规则中以叹号开头的前加#以便spring能够不报错
  - #!READWRITE_SPLITTING
    dataSources:
      readwrite_ds:
        writeDataSourceName: ds_0
        readDataSourceNames:
          - ds_1
        loadBalancerName: random
    loadBalancers:
      random:
        type: RANDOM
props:
  sql-show: false
```

### 参考
[https://github.com/0xu8d75/spring-cloud-parent](https://github.com/0xu8d75/spring-cloud-parent)
[https://github.com/apache/shardingsphere/blob/master/infra/url/type/classpath/src/main/java/org/apache/shardingsphere/infra/url/classpath/ClassPathURLLoader.java](https://github.com/apache/shardingsphere/blob/master/infra/url/type/classpath/src/main/java/org/apache/shardingsphere/infra/url/classpath/ClassPathURLLoader.java)