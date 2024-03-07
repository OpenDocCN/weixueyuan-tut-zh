# MyBatis 中 settings 属性配置详解

> 原文：[`c.biancheng.net/view/4324.html`](http://c.biancheng.net/view/4324.html)

在 MyBatis 中 settings 是最复杂的配置，它能深刻影响 MyBatis 底层的运行，但是在大部分情况下使用默认值便可以运行，所以在大部分情况下不需要大量配置它，只需要修改一些常用的规则即可，比如自动映射、驼峰命名映射、级联规则、是否启动缓存、执行器（Executor）类型等。settings 配置项说明，如表 1 所示。

表 1 settings 配置项说明

| 配置项 | 作用 | 配置选项 | 默认值 |
| cacheEnabled | 该配置影响所有映射器中配置缓存的全局开关 | true&#124;false | true |
| lazyLoadingEnabled | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。在特定关联关系中可通过设置 fetchType 属性来覆盖该项的开关状态  | true&#124;false | false |
| aggressiveLazyLoading | 当启用时，对任意延迟属性的调用会使带有延迟加载属性的对象完整加载；反之，每种属性将会按需加载 | true&#124;felse | 版本 3.4.1 （不包含） 之前 true，之后 false |
| multipleResultSetsEnabled  | 是否允许单一语句返回多结果集（需要兼容驱动） | true&#124;false | true |
| useColumnLabel | 使用列标签代替列名。不同的驱动会有不同的表现，具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果 | true&#124;false | true |
| useGeneratedKeys | 允许 JDBC 支持自动生成主键，需要驱动兼容。如果设置为 true，则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby） | true&#124;false | false |
| autoMappingBehavior | 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射。
PARTIAL 表示只会自动映射，没有定义嵌套结果集和映射结果集。
FULL 会自动映射任意复杂的结果集（无论是否嵌套） | NONE、PARTIAL、FULL | PARTIAL |
| autoMappingUnkno wnColumnBehavior | 指定自动映射当中未知列（或未知属性类型）时的行为。 默认是不处理，只有当日志级别达到 WARN 级别或者以下，才会显示相关日志，如果处理失败会抛出 SqlSessionException 异常 | NONE、WARNING、FAILING | NONE |
| defaultExecutorType | 配置默认的执行器。SIMPLE 是普通的执行器；REUSE 会重用预处理语句（prepared statements）；BATCH 执行器将重用语句并执行批量更新  | SIMPLE、REUSE、BATCH | SIMPLE |
| defaultStatementTimeout | 设置超时时间，它决定驱动等待数据库响应的秒数 | 任何正整数 | Not Set (null) |
| defaultFetchSize | 设置数据库驱动程序默认返回的条数限制，此参数可以重新设置 | 任何正整数   | Not Set (null) |
| safeRowBoundsEnabled | 允许在嵌套语句中使用分页（RowBounds）。如果允许，设置 false | true&#124;false | false |
| safeResultHandlerEnabled | 允许在嵌套语句中使用分页（ResultHandler）。如果允许，设置 false | true&#124;false | true |
| mapUnderscoreToCamelCase | 是否开启自动驼峰命名规则映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射 | true&#124;false | false |
| localCacheScope | MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速联复嵌套査询。 默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlScssion 的不同调用将不会共享数据   | SESSION&#124;STATEMENT | SESSION |
| jdbcTypeForNull | 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER | NULL、VARCHAR、OTHER   | OTHER |
| lazyLoadTriggerMethods | 指定哪个对象的方法触发一次延迟加载 | — | equals、clone、hashCode、toString |
| defaultScriptingLanguage | 指定动态 SQL 生成的默认语言 | — | org.apache.ibatis .script.ing.xmltags
.XMLDynamicLanguageDriver |
| callSettersOnNulls | 指定当结果集中值为 null 时，是否调用映射对象的 setter（map 对象时为 put）方法，这对于 Map.kcySet() 依赖或 null 值初始化时是有用的。注意，基本类型（int、boolean 等）不能设置成 null | true&#124;false | false |
| logPrefix | 指定 MyBatis 增加到日志名称的前缀  | 任何字符串 | Not set |
| loglmpl | 指定 MyBatis 所用日志的具体实现，未指定时将自动査找 | SLF4J&#124;LOG4J&#124;LOG4J2&#124;JDK_LOGGING &#124;COMMONS_LOGGING
&#124;ST DOUT_LOGGING&#124;NO_LOGGING | Not set |
| proxyFactory | 指定 MyBatis 创建具有延迟加栽能力的对象所用到的代理工具 | CGLIB&#124;JAVASSIST | JAVASSIST （MyBatis 版本为 3.3 及以上的） |
| vfsImpl | 指定 VFS 的实现类   | 提供 VFS 类的全限定名，如果存在多个，可以使用逗号分隔  | Not set |
| useActualParamName | 允许用方法参数中声明的实际名称引用参数。要使用此功能，项目必须被编译为 Java 8 参数的选择。（从版本 3.4.1 开始可以使用） | true&#124;false | true |

settings 的配置项很多，但是真正用到的不会太多，我们把常用的配置项研究清楚就可以了，比如关于缓存的 cacheEnabled，关于级联的 lazyLoadingEnabled 和 aggressiveLazy Loading，关于自动映射的 autoMappingBehavior 和 mapUnderscoreToCamelCase，关于执行器类型的 defaultExecutorType 等。

这里给出一个全量的配置样例，如下所示。

```

<settings>
    <setting name="cacheEnabled" value="true"/>
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="multipleResultSetsEnabled" value="true"/>
    <setting name="useColumnLabel" value="true"/>
    <setting name="useGeneratedKeys" value="false"/>
    <setting name="autoMappingBehavior" value="PARTIAL"/>
    <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
    <setting name="defaultExecutorType" value="SIMPLE"/>
    <setting name="defaultStatementTimeout" value="25"/>
    <setting name="defaultFetchSize" value="100"/>
    <setting name="safeRowBoundsEnabled" value="false"/>
    <setting name="mapUnderscoreToCamelCase" value="false"/>
    <setting name="localCacheScope" value="SESSION"/>
    <setting name="jdbcTypeForNull" value="OTHER"/>
    <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```