# Spring 整合 Redis 详细步骤

> 原文：[`c.biancheng.net/view/4582.html`](http://c.biancheng.net/view/4582.html)

教程前面大部分使用的是 XML 的方式整合 Redis，现在用注解驱动的方式来使用 Redis。和数据库事务一样，Spring 提供了缓存的管理器和相关的注解来支持类似于 Redis 这样的键值对缓存。

## 准备测试环境

首先，定义一个简单的角色 POJO，代码如下所示。

```

package com.pojo;
import java.io.Serializable;
public class Role implements Serializable {
    private static final long serialVersionUID = 3447499459461375642L;

    private long id;
    private String roleName;
    private String note;
    // 省略 setter 和 getter 方法
}
```

注意，该类实现了 Serializable 接口，这说明这个类支持序列化，这样就可以通过 Spring 的序列化器，将其保存为对应的编码，缓存到 Redis 中，也可以通过 Redis 读回那些编码，反序列化为对应的 Java 对象。

接下来是关于 MyBatis 的开发环境，这样我们就可以操作数据库了。创建 RoleMapper.xml，代码如下所示。

```

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.dao.RoleDao">
    <select id="getRole" resultType="com.pojo.Role">
        select id, role_name as
        roleName, note from t_role where id = #{id}
    </select>
    <delete id="deleteRole">
        delete from t_role where id=#{id}
    </delete>
    <insert id="insertRole" parameterType="com.pojo.Role"
        useGeneratedKeys="true" keyProperty="id">
        insert into t_role (role_name,
        note) values(#{roleName}, #{note})
    </insert>
    <update id="updateRole" parameterType="com.pojo.Role">
        update t_role set
        role_name = #{roleName}, note = #{note} where id = #{id}
    </update>
    <select id="findRoles" resultType="com.pojo.Role">
        select id, role_name as roleName, note from t_role
        <where>
            <if test="roleName != null">
                role_name like concat('%', #{roleName}, '%')
            </if>
            <if test="note != null">
                note like concat ('%', #{note},'%')
            </if>
        </where>
    </select>
</mapper>
```

然后，需要一个 MyBatis 角色接口，以便使用这样的一个映射文件，代码如下所示。

```

package com.dao;

import java.util.List;
import org.apache.ibatis.annotations.Param;
import com.pojo.Role;

public interface RoleDao {
    public Role getRole(Long id);

    public int deleteRole(Long id);

    public int insertRole(Role role);

    public int updateRole(Role role);

    public List<Role> findRoles(@Param("roleName") String roleName, @Param("note") String note);
}
```

注解 @Repository 表示它是一个持久层的接口。通过扫描和注解联合定义 DAO 层，就完成了映射器方面的内容。定义角色服务接口（RoleService），代码如下所示，不过服务接口实现类会在后面谈起，因为它需要加入 Spring 缓存注解，以驱动不同的行为。

```

package com.service;

import java.util.List;
import com.pojo.Role;

public interface RoleService {
    public Role getRole(Long id);

    public int deleteRole(Long id);

    public Role insertRole(Role role);

    public int updateRole(Role role);

    public List<Role> findRoles(String roleName, String note);
}
```

通过 Java 配置定义数据库和相关的扫描内容，代码如下所示。

```

package com.config;

import java.util.Properties;

import javax.sql.DataSource;

import org.apache.commons.dbcp.BasicDataSourceFactory;
import org.aspectj.apache.bcel.Repository;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.Resource;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;
import org.springframework.transaction.annotation.TransactionManagementConfigurer;

@Configuration
// 定义 Spring 扫描的包
@ComponentScan("com.*")
// 使用事务驱动管理器
@EnableTransactionManagement
// 实现接口 TransactionManagementConfigurer，这样可以配置注解驱动事务
public class RootConfig implements TransactionManagementConfigurer {
    private DataSource dataSource = null;

    /**
     * 配置数据库
     *
     * @return 数据连接池
     */
    @Bean(name = "dataSource")
    public DataSource initDataSource() {
        if (dataSource != null) {
            return dataSource;
        }
        Properties props = new Properties();
        props.setProperty("driverClassName", "com.mysql.jdbc.Driver");
        props.setProperty("url", "jdbc:mysql://localhost:3306/redis");
        props.setProperty("username", "root");
        props.setProperty("password", "1128");
        try {
            dataSource = BasicDataSourceFactory.createDataSource(props);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return dataSource;
    }

    /**
     * 配置 SqlSessionFactoryBean
     *
     * @return SqlSessionFactoryBean
     */
    @Bean(name = "SqlSessionFactory")
    public SqlSessionFactoryBean initSqlSessionFactory() {
        SqlSessionFactoryBean SqlSessionFactory = new SqlSessionFactoryBean();
        SqlSessionFactory.setDataSource(initDataSource());
        // 配置 MyBatis 配置文件
        Resource resource = new ClassPathResource("mybatis/mybatis-config.xml");
        SqlSessionFactory.setConfigLocation(resource);
        return SqlSessionFactory;
    }

    /**
     * 通过自动扫描，发现 MyBatis Mapper 接口
     *
     * @return Mapper 扫描器
     */
    @Bean
    public MapperScannerConfigurer initMapperScannerConfigurer() {
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        // 扫描包
        msc.setBasePackage("com.*");
        msc.setSqlSessionFactoryBeanName("SqlSessionFactory");
        // 区分注解扫描
        msc.setAnnotationClass(Repository.class);
        return msc;
    }

    /**
     * 实现接口方法，注册注解事务，当@Transactional 使用的时候产生数据库事务
     */
    @Override
    @Bean(name = "annotationDrivenTransactionManager")
    public PlatformTransactionManager annotationDrivenTransactionManager() {
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(initDataSource());
        return transactionManager;
    }
}
```

在 SqlSessionFactoryBean 的定义中引入了关于 MyBatis 的一个配置文件——mybatis-config.xml，它放在源码的 mybatis 目录之下，它的作用是引入 RoleMapper.xml，这里笔者放在目录 com/dao 下，代码如下所示。

```

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <mappers>
        <mapper resource="com/dao/RoleMapper.xml" />
    </mappers>
</configuration>
```

这样测试只要一个 RoleService 实现类就可以了，这个类的实现就是我们后面所需要讨论的主要的内容，不过在此之前要先了解 Spring 的缓存管理器。

## Spring 的缓存管理器

在 Spring 项目中它提供了接口 CacheManager 来定义缓存管理器，这样各个不同的缓存就可以实现它来提供管理器的功能了，而在 spring-data-redis.jar 包中实现 CacheManager 接口的则是 RedisCacheManager，因此要定义 RedisCacheManager 的 Bean，不过在此之前要先定义 RedisTemplate。

下面使用注解驱动 RedisCacheManager 定义，代码如下所示。

```

package com.config;

import java.util.ArrayList;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.JdkSerializationRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import redis.clients.jedis.JedisPoolConfig;

@Configuration
@EnableCaching
public class RedisConfig {
    @Bean(name = "redisTemplate")
    public RedisTemplate initRedisTemplate() {
        JedisPoolConfig poolConfig = new JedisPoolConfig();
        // 最大空闲数
        poolConfig.setMaxIdle(50);
        // 最大连接数
        poolConfig.setMaxTotal(100);
        // 最大等待亳秒数
        poolConfig.setMaxWaitMillis(20000);
        // 创建 Jedis 连接工厂
        JedisConnectionFactory connectionFactory = JedisConnectionFactory(poolConfig);
        connectionFactory.setHostName("localhost");
        connectionFactory.setPort(6379);
        // 调用后初始化方法，没有它将抛出异常
        connectionFactory.afterPropertiesSet();
        // 自定 Redis 序列化器
        RedisSerializer jdkSerializationRedisSerializer = new JdkSerializationRedisSerializer();
        RedisSerializer stringRedisSerializer = new StringRedisSerializer();
        // 定义 RedisTemplate，并设置连接工程
        RedisTemplate redisTemplate = new RedisTemplate();
        redisTemplate.setConnectionFactory(connectionFactory);
        // 设置序列化器
        redisTemplate.setDefaultSerializer(stringRedisSerializer);
        redisTemplate.setKeySerializer(stringRedisSerializer);
        redisTemplate.setValueSerializer(jdkSerializationRedisSerializer);
        redisTemplate.setHashKeySerializer(stringRedisSerializer);
        redisTemplate.setHashValueSerializer(jdkSerializationRedisSerializer);
        return redisTemplate;
    }

    @Bean(name = "redisCacheManager")
    public CacheManager initRedisCacheManager(@Autowired RedisTemplate redisTempate) {
        RedisCacheManager cacheManager = new RedisCacheManager(redisTempate);
        // 设置超时时间为 10 分钟，单位为秒
        cacheManager.setDefaultExpiration(600);
        // 设置缓存名称
        List<String> cacheNames = new ArrayList<String>();
        cacheNames.add("redisCacheManager");
        cacheManager.setCacheNames(cacheNames);
        return cacheManager;
    }
}
```

@EnableCaching 表示 Spring IoC 容器启动了缓存机制。对于 RedisTemplate 的定义实例和 XML 的方式差不多。注意，在创建 Jedis 连接工厂（JedisConnectionFactory）后，要自己调用其 afterPropertiesSet 方法，因为这里不是单独自定义一个 Spring Bean，而是在 XML 方式中是单独自定义的。

这个类实现了 InitializingBean 接口，按照 Spring Bean 的生命周期，我们知道它会被 Spring IoC 容器自己调用，而这里的注解方式没有定义 SpringBean，因此需要自己调用，这也是使用注解方式的不便之处——需要了解其内部的实现。

字符串定义了 key（包括 hash 数据结构），而值则使用了序列化，这样就能够保存 Java 对象了。

缓存管理器 RedisCacheManager 定义了默认的超时时间为 10 分钟，这样就可以在一定的时间间隔后重新从数据库中读取数据了，而名称则定义为 redisCacheManager，名称是为了方便后面注解引用的。

如果在 XML 中使用缓存管理器，那也是可行的，首先要定义 RedisTemplate，这些已经在前面的章节中谈论过了，所以关于 RedisTemplate 的 XML 配置就不再重复阐述了，这里只定义 RedisCacheManager，使用 XML 定义缓存管理器代码如下所示。

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:cache="http://www.springframework.org/schema/cache"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/cache
    http://www.springframework.org/schema/cache/spring-cache.5.0.xsd
    ">
    <!--使用注解驱动，其中属性 cache-manager 默认值为 cacheManager, 所以如果你的缓存管理器名称也是 cacheManager 则无需重新定义 -->
    <cache:annotation-driven cache-manager="redisCacheManager" />
    <!-- 定义缓存管理器，如果你使用 id="cacheManager"，则驱动不需要显式配置 cache-manager 属性 -->
    <bean id="redisCacheManager"
        class="org.springframework.data.redis.cache.RedisCacheManager">
        <!--通过构造方法注入 RedisTemplate -->
        <constructor-arg index="0" ref="redisTemplate" />
        <!-- 定义默认超时时间，单位秒 -->
        <property name="defaultExpiration" value="600" />
        <!--缓存管理器名称 -->
        <property name="cacheNames">
            <list>
                <value>redisCacheManager</value>
            </list>
        </property>
    </bean>
</beans>
```

这样也可以配置好对应的缓存管理器。

## 缓存注解简介

配置了缓存管理器之后，Spring 就允许用注解的方式使用缓存了，这里的注解有 4 个。XML 也可以使用它们，但是用得不多，我们就不再介绍了，还是以注解为主。首先简介一下缓存注解，如表 1 所示。

表 1 缓存注解

| 注   解 | 描   述 |
| @Cacheable | 表明在进入方法之前，Spring 会先去缓存服务器中査找对应 key 的缓存值，如果找到缓存值，那么 Spring 将不会再调用方法，而是将缓存值读出，返回给调用者；如果没有找到缓存值，那么 Spring 就会执行你的方法，将最后的结果通过 key 保存到缓存服务器中 |
| @CachePut | Spring 会将该方法返回的值缓存到缓存服务器中，这里需要注意的是，Spring 不会事先去缓存服务器中查找，而是直接执行方法，然后缓存。换句话说，该方法始终会被 Spring 所调用 |
| @CacheEvict | 移除缓存对应的 key 的值 |
| @Caching | 这是个分组注解，它能够同时应用于其他缓存的注解 |

注解 @Cacheable 和 @CachePut 都可以保存缓存键值对，只是它们的方式略有不同，请注意二者的区别，它们只能运用于有返回值的方法中，而删除缓存 key 的 @CacheEvict 则可以用在 void 的方法上，因为它并不需要去保存任何值。

上述注解都能标注到类或者方法之上，如果放到类上，则对所有的方法都有效；如果放到方法上，则只是对方法有效。在大部分情况下，会放置到方法上。因为 @Cacheable 和 @CachePut 可以配置的属性接近，所以把它们归为一类去介绍，而 @Caching 因为不常用，就不介绍了。

一般而言，对于查询，我们会考虑使用 @Cacheable；对于插入和修改，我们会考虑使用 @CachePut；对于删除操作，我们会考虑使用 @CacheEvict。

## 注解@Cacheable 和@CachePut

因为 @Cacheable 和 @CachePut 两个注解的配置项比较接近，所以这里就将这两个注解一并介绍了，它们的属性，如表 2 所示。

表 2 @Cacheable 和 @CachePut 配置属性

| 属   性 | 配置类型 | 描   述 |
| value | String[] | 使用缓存的名称 |
| condition | String | Spring 表达式，如果表达式返回值为 false，则不会将缓存应用到方法上，true 则会 |
| key | String | Spring 表达式，可以通过它來计算对应缓存的 key |
| unless | String | Spring 表达式，如果表达式返回值为 true，则不会将方法的结果放到缓存上 |

其中，因为 value 和 key 这两个属性使用得最多，所以先来讨论这两个属性。value 是一个数组，可以引用多个缓存管理器。

比如使用注解驱动 RedisCacheManager 定义代码中所定义的 RedisCacheManager，就可以引用它了，而对于 key 则是缓存中的键，它支持 Spring 表达式，通过 Spring 表达式就可以自定义缓存的 key。编写剩下的 RoleService 接口的实现类——RoleServiceImpl 的方法。

先了解一些 Spring 表达式和缓存注解之间的约定，通过这些约定去引用方法的参数和返回值的内容，使得其注入 key 所定义的 Spring 表达式的结果中，表达式值的引用如表 3 所示。

表 3 表达式值的引用

| 表达式 | 描   述 | 备   注 |
| #root.args | 定义传递给缓存方法的参数 | 不常用，不予讨论 |
| #root.caches | 该方法执行是对应的缓存名称，它是一个数组 | 同上 |
| #root.target | 执行缓存的目标对象 | 同上 |
| #root.targetClass | 目标对象的类，它是 #root.target.class 的缩写 | 同上 |
| #root.method | 缓存方法 | 同上 |
| #root.methodName | 缓存方法的名称，它是 #root.method.name 的缩写 | 同上 |
| #result | 方法返冋结果值，还可以使用 Spring 表达式进一步读取其属性 | 请注意该表达式不能用于注解 @Cacheablc，因为该注解的方法可能不会被执行，这样返回值就无从谈起了 |
| #Argument | 任意方法的参数，可以通过方法本身的名称或者下标去定义 | 比如 getRole(Long id) 方法，想读取 id 这个参数，可以写为 #id，或者 #a0、#p0，笔者建议写为 #id，这样可读性高 |

这样就方便使用对应的参数或者返回值作为缓存的 key 了。

RoleService 接口的实现类——RoleServiceImpl，它有 3 个方法，使用这个缓存可以启动缓存管理器来保存数据，代码如下所示。

```

package com.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.CachePut;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

import com.dao.RoleDao;
import com.pojo.Role;

@Service
public class RoleServiceImpl implements RoleService {
//角色 DAO，方便执行 SQL
    @Autowired
    private RoleDao roleDao = null;

    /**
     * 使用@Cacheable 定义缓存策略 当缓存中有值，则返回缓存数据，否则访问方法得到数据 通过 value 引用缓存管理器，通过 key 定义键
     *
     * @param id 角色编号
     * @return 角色
     */
    @Override
    @Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.REQUIRED)
    @Cacheable(value = "redisCacheManager", key = "'redis_role_'+#id")
    public Role getRole(Long id) {
        return roleDao.getRole(id);
    }

    /**
     * 使用@CachePut 则表示无论如何都会执行方法，最后将方法的返回值再保存到缓存中 使用在插入数据的地方，则表示保存到数据库后，会同期插入 Redis 缓存中
     *
     * @param role 角色对象 @return 角色对象(会回填主键)
     */
    @Override
    @Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.REQUIRED)
    @CachePut(value = "redisCacheManager", key = "'redis_role_'+#resuIt.id")
    public Role insertRole(Role role) {
        roleDao.insertRole(role);
        return role;
    }

    /**
     * 使用@CachePut，表示更新数据库数据的同时，也会同步更新缓存
     *
     * @param role 角色对象
     * @return 影响条数
     */

    @Override
    @Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.REQUIRED)
    @CachePut(value = "redisCacheManager", key = "'redis_role_' +#role. id")
    public Role updateRole(Role role) {
        roleDao.updateRole(role);
        return role;
    }
    ......
}
```

因为 getRole 方法是一个查询方法，所以使用 @Cacheable 注解，这样在 Spring 的调用中，它就会先查询 Redis，看看是否存在对应的值，那么采用什么 key 去查询呢？

注解中的 key 属性，它配置的是 'redis_role_'+#id，这样 Spring EL 就会计算返回一个 key，比如参数 id 为 1L，其 key 计算结果就为 redis_role_1。以一个 key 去访问 Redis，如果有返回值，则不再执行方法，如果没有则访问方法，返回角色信息，然后通过 key 去保存数据到 Redis 中。

先执行 insertRole 方法才能把对应的信息保存到 Redis 中，所以采用的是注解 @CachePut。由于主键是由数据库生成，所以无法从参数中读取，但是可以从结果中读取，那么 #result.id 的写法就会返回方法返回的角色 id。而这个角色 id 是通过数据库生成，然后由 MyBatis 进行回填得到的，这样就可以在 Redis 中新增一个 key，然后保存对应的对象了。

对于 updateRole 方法而言，采用的是注解 @CachePut，由于对象有所更新，所以要在方法之后更新 Redis 的数据，以保证数据的一致性。这里直接读取参数的 id，所以表达式写为 #role.id，这样就可以引入角色参数的 id 了。在方法结束后，它就会去更新 Redis 对应的 key 的值了。

为此可以提供一个 log4j.properties 文件来监控整个过程：

# Global logging configuration
log4j.rootLogger=DEBUG,stdout
# MyBatis logging configuration...
log4j.logger.com.mybatis=DEBUG
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n

然后通过以下代码来测试缓存注解。

```

// 使用注解 Spring IoC 容器
ApplicationContext ctx = new AnnotationConfigApplicationContext(RootConfig.class, RedisConfig.class);
// 获取角色服务类
RoleService roleService = ctx.getBean(RoleService.class);
Role role = new Role();
role.setRoleName("role_name_1");
role.setNote("role_note_1");
// 插入角色
roleService.insertRole(role);
// 获取角色
Role getRole = roleService.getRole(role.getId());
getRole.setNote("role_note_1_update");
// 更新角色
roleService.updateRole(getRole);
```

这里将关于数据库和 Redis 的相关配置通过注解 Spring IoC 容器加载进来，这样就可以用 Spring 操作这些资源了，然后执行插入、获取、更新角色的方法。