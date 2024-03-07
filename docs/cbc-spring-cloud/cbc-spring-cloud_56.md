# Spring Cloud Sleuth 与 ELK（日志分析系统）配合使用

> 原文：[`c.biancheng.net/view/5495.html`](http://c.biancheng.net/view/5495.html)

在《Spring Cloud 使用 Sleuth 在应用中进行日志跟踪》教程中的案例，我们已经实现了服务调用之间的链路追踪，但是这些日志是分散在各个机器上的，就算出现问题了，我们想快速定位，也得从各个机器把日志整合起来，再去查问题。

这个时候就需要引入日志分析系统了，比如 ELK，可以将多台服务器上的日志信息统一收集起来，在出问题的时候我们可以轻松根据 traceId 来搜索出对应的请求链路信息。

## ELK 简介

ELK 由三个组件组成：

*   Elasticsearch 是个开源分布式搜索引擎，它的特点有分布式、零配置、自动发现、索引自动分片、索引副本机制、restful 风格接口、多数据源、自动搜索负载等。
*   Logstash 是一个完全开源的工具，它可以对日志进行收集、分析并存储以供以后使用。
*   kibana 是一个开源和免费的工具，它可以为 Logstash 和 ElasticSearch 提供日志分析友好的 Web 界面，可以汇总、分析和搜索重要数据日志。

## 输出 JSON 格式日志

可以通过 logback 来输出 Json 格式的日志，让 Logstash 收集存储到 Elasticsearch 中，然后在 kibana 中查看。想要输入 Json 格式的数据需要加一个依赖，具体代码如下所示。

<!-- 输出 Json 格式日志 -->
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>5.2</version>
</dependency>

然后创建一个 logback-spring.xml 文件。配置 logstash 需要收集的数据格式如下：

```

<!-- Appender to log to file in a JSON format -->
<appender name="logstash"
    class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOG_FILE}.json</file>
    <rollingPolicy
        class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <fileNamePattern>${LOG_FILE}.json.%d{yyyy-MM-dd}.gz</fileNamePattern>
        <maxHistory>7</maxHistory>
    </rollingPolicy>
    <encoder
        class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
        <providers>
            <timestamp>
                <timeZone>UTC</timeZone>
            </timestamp>
            <pattern>
                <pattern>
                    {
                    "severity": "%level",
                    "service": "${springAppName:-}",
                    "trace": "%X{X-B3-TraceId:-}",
                    "span": "%X{X-B3-SpanId:-}",
                    "parent": "%X{X-B3-ParentSpanId:-}",
                    "exportable":
                    "%X{X-Span-Export:-}",
                    "pid": "${PID:-}",
                    "thread": "%thread",
                    "class": "%logger{40}",
                    "rest": "%message"
                    }
                </pattern>
            </pattern>
        </providers>
    </encoder>
</appender>
```

集成好后就能在输出的日志目录中看到有一个以“.json”结尾的日志文件了，里面的数据格式是 Json 形式的，可以直接通过 Logstash 进行收集。

{
    "@timestamp": "2019-11-30T01:48:32.221+00:00",
    "severity": "DEBUG",
    "service": "fsh-substitution",
    "trace": "41b5a575c26eeea1",
    "span": "41b5a575c26eeea1",
    "parent": "41b5a575c26eeea1",
    "exportable": "false",
    "pid": "12024",
    "thread": "hystrix-fsh-house-10",
    "class": "c.f.a.client.fsh.house.HouseRemoteClient",
    "rest": "[HouseRemoteClient#hosueInfo] <--- END HTTP (796-byte body)"
}

日志收集存入 ElasticSearch 之后，就可以用 Kibana 进行展示。需要排查某个请求的问题时，直接根据 traceid 搜索，就可以把整个请求链路相关的日志信息查询出来。