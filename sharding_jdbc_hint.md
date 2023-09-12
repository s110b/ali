sharing-jdbc hint 基于Hint的强制路由分库，只分库，不分表

     Hint方式主要使用场景：
    1.分片字段不存在SQL中、数据库表结构中，而存在于外部业务逻辑。
    2.强制在主库进行某些数据操作。

1. 第一种方式，java代码
```java
/**
 * @author xiaofeng.song
 * @date 2019/6/04
 */
@Configuration
public class DataSourceConfig {
    @Autowired
    private Database1Config database1Config;
    @Autowired
    private Database0Config database0Config;
 
    @Bean
    public DataSource dataSource() throws Exception {
        ShardingRuleConfiguration shardJdbcConfig = new ShardingRuleConfiguration();
        shardJdbcConfig.getTableRuleConfigs().add(getTableRule01());
        shardJdbcConfig.setDefaultDataSourceName(database1Config.getDatabaseName());
        shardJdbcConfig.setDefaultDatabaseShardingStrategyConfig(new HintShardingStrategyConfiguration(new ModuloHintShardingAlgorithm()));
        Map<String, DataSource> dataMap = new LinkedHashMap<>();
        dataMap.put(database0Config.getDatabaseName(), database0Config.createDataSource());
        dataMap.put(database1Config.getDatabaseName(), database1Config.createDataSource());
        Properties properties = new Properties();
        properties.put("sql.show", "true");
        return ShardingDataSourceFactory.createDataSource(dataMap, shardJdbcConfig, properties);
    }

    /**
     * Shard-JDBC 分表配置
     */
    private static TableRuleConfiguration getTableRule01() {
        TableRuleConfiguration result = new TableRuleConfiguration("t_order");
        result.setDatabaseShardingStrategyConfig(new HintShardingStrategyConfiguration(new ModuloHintShardingAlgorithm()));
        return result;
    }

}

```
2. yaml配置文件方式
```xml
dataSources:
  ds_0: !!com.zaxxer.hikari.HikariDataSource
    driverClassName: com.mysql.jdbc.Driver
    jdbcUrl: jdbc:mysql://localhost:3306/database0?characterEncoding=utf8&useSSL=false&serverTimezone=GMT
    username: root
    password: root
  ds_1: !!com.zaxxer.hikari.HikariDataSource
    driverClassName: com.mysql.jdbc.Driver
    jdbcUrl: jdbc:mysql://localhost:3306/database0?characterEncoding=utf8&useSSL=false&serverTimezone=GMT
    username: root
    password: root

shardingRule:
  defaultDatabaseStrategy:
    hint:
      algorithmClassName: com.thextrader.dlcms.config.ModuloHintShardingAlgorithm
  defaultTableStrategy:
    none:

props:
  sql.show: true
```
3，分库策略类： 通过传入的数据库名称，选则对应的数据源
```java
public final class ModuloHintShardingAlgorithm implements HintShardingAlgorithm<String> {
    
    @Override
    public Collection<String> doSharding(final Collection<String> availableTargetNames, final HintShardingValue<String> shardingValue) {
        Collection<String> result = new ArrayList<>();
            for (String value : shardingValue.getValues()) {
                if (availableTargetNames.contains(value)) {
                    result.add(value);
                }
            }
        return result;
    }
}
```

4，使用方式：

```java
       HintManager hintManager = HintManager.getInstance();
       hintManager.setDatabaseShardingValue("databaseName");//此处变量填写数据库名称
```

可以把这段代码放在filter中，用传值选择对应的数据库。

最后记得调用：

```java
        hintManager.close();
```

清理中threadlocal中的数据。

[ShardingSphere官网](https://shardingsphere.apache.org/document/current/cn/manual/sharding-jdbc/usage/hint/)

