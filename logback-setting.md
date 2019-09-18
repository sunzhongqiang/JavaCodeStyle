# 本文介绍基于spring-boot集成logback的方法和步骤

## 底层依赖

```
<dependency>
  <groupId>org.codehaus.groovy</groupId>
  <artifactId>groovy</artifactId>
</dependency>
```
这个依赖可以让我们使用logback.groovy来进行配置日志配置,logback其他依赖springboot默认已经引入

logback.groovy的配置
位置：src/main/resources/logback.groovy
内容：

```
import ch.qos.logback.classic.db.DBAppender
import ch.qos.logback.classic.encoder.PatternLayoutEncoder
import ch.qos.logback.core.db.DataSourceConnectionSource
import com.zaxxer.hikari.HikariDataSource

import static ch.qos.logback.classic.Level.INFO

appender("CONSOLE", ConsoleAppender) {
    encoder(PatternLayoutEncoder) {
        pattern = "%d{HH:mm:ss} %-5level [%-30logger{0}] :- %msg%n"
    }
}

appender("DATABASE", DBAppender) {
    connectionSource(DataSourceConnectionSource) {
        dataSource(HikariDataSource) {
            driverClassName="com.mysql.cj.jdbc.Driver"
            jdbcUrl="jdbc:mysql://<backhost>[:port]/<logbackdb>?serverTimezone=GMT%2B8"
            username = "<username>"
            password = '<password>'
        }
    }
}

root(INFO, ["CONSOLE", "DATABASE"])

```

线上工具：把logback.xml 转义成 logback.groovy  : https://logback.qos.ch/translator/asGroovy.html
