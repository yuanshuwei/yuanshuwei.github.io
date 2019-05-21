---
title: apollo实时更新问题
date: 2019-05-21 17:36:41
tags: apollo
categories:
---
### 大概介绍
分布式配置, 统一管理不同环境，不同集群的配置。  
- 配置修改时服务端实时推送到客户端，客户端数据存在内存(不用自己管理),并缓存在本地文件中；同时客户端一段时间(默认5分钟)也会主动去服务端拉数据，防止推送失败
- 分为configService、adminService、portalService
- [部署文档](https://github.com/ctripcorp/apollo/wiki/%E5%88%86%E5%B8%83%E5%BC%8F%E9%83%A8%E7%BD%B2%E6%8C%87%E5%8D%97)、[使用文档](https://github.com/ctripcorp/apollo/wiki/Java客户端使用指南)
  
### 问题
配置的更新推送，更新的是本地内存, 我们希望接收到更新到spring context中去。甚至重新初始化bean，重新建立数据库连接等，做到通过apollo集中管理配置更新，无需程序重新启动的完成配置更新。
<!-- more -->
  
### 实现示例
① apollo提供了changeListner监听事件，可以在监听到更新后，对已有的Properties的属性完成更新
```
ConfigService.getAppConfig().addChangeListener(changeEvent -> {
            System.out.println("Changes for namespace " + changeEvent.getNamespace());
            for (String key : changeEvent.changedKeys()) {
                ConfigChange change = changeEvent.getChange(key);
                System.out.println(String.format("Found change - key: %s, oldValue: %s, newValue: %s, changeType: %s", change.getPropertyName(), change.getOldValue(), change.getNewValue(), change.getChangeType()));
            }
        });
```
② 将对于的更改更新到spring中去，Spring cloud 提供了`RefreshScope`，示例演示`@ConfigurationProperties`配置的bean更新

```
@Data
public class Testconfig {
    private String name;
    private int age;
}
```
```
public class App {
    @Bean
    @ConfigurationProperties(prefix = "test")
    public Testconfig testconfig() {
        return new Testconfig();
    }

    @Bean
    public SpringBootApolloRefreshConfig springBootApolloRefreshConfig() {
        return new SpringBootApolloRefreshConfig();
    }
}
```
```
@Component
public class SpringBootApolloRefreshConfig {

    private static final Logger logger = LoggerFactory.getLogger(SpringBootApolloRefreshConfig.class);

    @Autowired
    private Testconfig testconfig;
    @Autowired
    private RefreshScope refreshScope;
    @Autowired
    private ContextRefresher contextRefresher;

    public SpringBootApolloRefreshConfig() {
    }

    @ApolloConfigChangeListener(value = ConfigConsts.NAMESPACE_APPLICATION)
    public void onChange(ConfigChangeEvent configChangeEvent) {

        logger.info("before refresh: {}", testconfig.toString());
        refreshScope.refreshAll();
        contextRefresher.refresh();
        logger.info("after refresh: {}", testconfig.toString());
    }
}
```
③ 完成如dataSource, 更新配置后，完成新建数据库连接，新的请求转到新的连接，旧连接逐步销毁 // TODO
[参考这个讨论](https://github.com/ctripcorp/apollo/issues/773)