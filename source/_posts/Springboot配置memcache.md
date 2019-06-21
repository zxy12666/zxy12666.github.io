title: Springboot配置memcache
author: 张翔宇
tags:
  - SpringBoot
categories:
  - JAVA
date: 2019-06-21 11:34:00
---
## Memcached 介绍

Memcached 是一个高性能的分布式内存对象缓存系统，用于动态Web应用以减轻数据库负载。它通过在内存中缓存数据和对象来减少读取数据库的次数，从而提高动态、数据库驱动网站的速度。Memcached基于一个存储键/值对的hashmap。其守护进程（daemon ）是用C写的，但是客户端可以用任何语言来编写，并通过memcached协议与守护进程通信。

因为 Spring Boot 没有针对 Memcached 提供对应的组建包，因此需要我们自己来集成。官方推出的 Java 客户端 Spymemcached 是一个比较好的选择之一。

## Spymemcached 介绍

Spymemcached 最早由 Dustin Sallings 开发，Dustin 后来和别人一起创办了 Couchbase (原NorthScale)，职位为首席架构师。2014 加入 Google。

Spymemcached 是一个采用 Java 开发的异步、单线程的 Memcached 客户端， 使用 NIO 实现。Spymemcached 是 Memcached 的一个流行的 Java client 库，性能表现出色，广泛应用于 Java + Memcached 项目中。

## 依赖配置

**添加依赖**

pomx 包中添加 spymemcached 的引用

```ejs
<dependency>
  <groupId>net.spy</groupId>
  <artifactId>spymemcached</artifactId>
  <version>2.12.2</version>
</dependency>
```

在项目resource->config->dev和pro中新增memcachedconf.properties，
![Markdown](http://i1.fuimg.com/691375/616e1938ee018f68s.png)

这时需要使用@PropertySource注解加载指定的属性文件。

**设置配置对象**

创建 `MemcacheSource` 接收配置信息

```ejs
package olaf.olaf.service;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "memcached")
@PropertySource("classpath:memcachedconf.properties")
public class MemcacheSource {
    private boolean isOpen;

    private String servers;

    private int expires;

    private int port;

    private int connectionPoolSize;

    private String pre;

    public String getPre() {
        return pre;
    }

    public void setPre(String pre) {
        this.pre = pre;
    }

    public boolean getIsOpen() {
        return isOpen;
    }

    public void setIsOpen(boolean open) {
        isOpen = open;
    }

    public String getServers() {
        return servers;
    }

    public void setServers(String servers) {
        this.servers = servers;
    }

    public int getExpires() {
        return expires;
    }

    public void setExpires(int expires) {
        this.expires = expires;
    }

    public int getPort() {
        return port;
    }

    public void setPort(int port) {
        this.port = port;
    }

    public int getConnectionPoolSize() {
        return connectionPoolSize;
    }

    public void setConnectionPoolSize(int connectionPoolSize) {
        this.connectionPoolSize = connectionPoolSize;
    }

}
```

`@ConfigurationProperties(prefix = "memcache")` 的意思会以 `memcache.*` 为开通将对应的配置文件加载到属性中。

## 启动初始化 MemcachedClient

`CommandLineRunner` 接口的 `Component` 会在所有 `Spring Beans `都初始化之后，`SpringApplication.run() `之前执行，非常适合在应用程序启动之初进行一些数据初始化的工作。利用 CommandLineRunner 在项目启动的时候配置好 MemcachedClient 。

```
package olaf.olaf.service;


import net.spy.memcached.MemcachedClient;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;

import javax.annotation.Resource;
import java.io.IOException;
import java.net.InetSocketAddress;
import java.util.Collection;
import java.util.Map;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;


/**
 * Author: yuanhy
 * Time: 2017-9-6  17:04
 * Description:
 */
@Component
public class Memcached implements CommandLineRunner {
    private static Log log = LogFactory.getLog(Memcached.class);

    private MemcachedClient mc;

    public final static int DEFAULT_TIMEOUT = 5;
    /**
     * The constant timeUnitSeconds.
     */
    public final static TimeUnit timeUnitSeconds = TimeUnit.SECONDS;

    @Resource
    private  MemcacheSource memcacheSource;
    /*
    set 无论何时都保存
     */
    public <T> void set(String key, T value) {
        this.set(key, memcacheSource.getExpires(), value);
    }

    /**
     * 设置键值对
     *
     * @param key     key
     * @param expires 单位:秒，0 表示永不过期
     * @param value   必须是一个可序列化的对象, 可以是容器类型如:List，但容器里面保存的对象必须是可序列化的
     */
    public <T> void set(String key, int expires, T value) {
        if (StringUtils.isEmpty(key)) {
            return;
        }
        try {
            if (memcacheSource.getIsOpen() && mc != null) {
                Future<Boolean> rs = mc.set(memcacheSource.getPre() + key, expires, value);
                System.out.println(rs.get());
            }
        } catch (Exception e) {
            log.error(e.getMessage(), e);
        }
    }

    /**
     * 根据key获得值
     *
     * @param key key
     * @return value
     */
    public <T> T get(String key) {
        try {
            if (!StringUtils.isEmpty(key) && memcacheSource.getIsOpen() && mc != null) {
                return (T) mc.get(memcacheSource.getPre()+ key);
            }
        } catch (Exception e) {
            log.error(e.getMessage(), e);
        }
        return null;
    }

    /**
     * 将对象添加到缓存
     *
     * @param key    the key
     * @param value  the value
     * @param expire the expire
     * @return the boolean
     */
    public Boolean add(String key, Object value, int expire) {
        Future<Boolean> f = mc.add(key, expire, value);
        return getResult(f);
    }

    /**
     * 替换某个键值
     *
     * @param key    the 键
     * @param value  the 值
     * @param expire the 过期时间
     * @return the boolean
     */
    public Boolean replace(String key, Object value, int expire) {
        Future<Boolean> f = mc.replace(key, expire, value);
        return getResult(f);
    }

    /**
     * 删除某个特定键
     *
     * @param key the key
     * @return the boolean
     */
    public Boolean delete(String key) {
        Future<Boolean> f = mc.delete(key);
        return getResult(f);
    }

    /**
     * 立即从所有服务器清除所有缓存,慎用。
     *
     * @return the boolean
     */
    @Deprecated
    public Boolean flush() {
        Future<Boolean> f = mc.flush();
        return getResult(f);
    }

    /**
     * 从缓存中获取多个键值。
     *
     * @param keys the 键集合
     * @return the multi
     */
    public Map<String, Object> getMulti(Collection<String> keys) {
        return mc.getBulk(keys);
    }

    /**
     * 从缓存中获取多个键值
     *
     * @param keys the 键数组
     * @return the multi
     */
    public Map<String, Object> getMulti(String[] keys) {
        return mc.getBulk(keys);
    }

    /**
     * 异步地从缓存中获取一组对象并使用它们进行解码
     *
     * @param keys the 键集合
     * @return the map
     */
    public Map<String, Object> asyncGetMulti(Collection<String> keys) {
        Map<String, Object> map = null;
        Future<Map<String, Object>> f = mc.asyncGetBulk(keys);
        try {
            map = getResult(f);
        } catch (Exception e) {
            f.cancel(false);
        }
        return map;
    }

    /**
     * 增加给定的计数器，返回新值。
     *
     * @param key          the key
     * @param by           the 增值
     * @param defaultValue the 默认值(如计时器不存在)，如该key没值，则取默认值
     * @param expire       the 过期时间
     * @return the long
     */
    public long increment(String key, int by, long defaultValue, int expire) {
        return mc.incr(key, by, defaultValue, expire);
    }

    /**
     * 以给定的数量增加给定的键。
     *
     * @param key the key
     * @param by  the 增值
     * @return the long
     */
    public long increment(String key, int by) {
        return mc.incr(key, by);
    }

    /**
     * 减量.
     *
     * @param key          the key
     * @param by           the 减量
     * @param defaultValue the 默认值(如果计数器不存在)
     * @param expire       the 过期时间
     * @return the long
     */
    public long decrement(String key, int by, long defaultValue, int expire) {
        return mc.decr(key, by, defaultValue, expire);
    }

    /**
     * 减量
     *
     * @param key the key
     * @param by  the 要减的值
     * @return the long
     */
    public long decrement(String key, int by) {
        return mc.decr(key, by);
    }

    /**
     * 异步增量，并返回当前值.
     *
     * @param key the key
     * @param by  the 要增加的值
     * @return the long
     */
    public Long asyncIncrement(String key, int by) {
        Future<Long> f = mc.asyncIncr(key, by);
        return getResult(f);
    }

    /**
     * Async decrement long.
     * 异步减量，并返回当前值
     *
     * @param key the key
     * @param by  the 要减少的值
     * @return the long
     */
    public Long asyncDecrement(String key, int by) {
        Future<Long> f = mc.asyncDecr(key, by);
        return getResult(f);
    }

    /**
     * Gets result.
     * 获取返回结果
     *
     * @param <T>    the type parameter
     * @param future the future
     * @return the result
     */
    public <T> T getResult(Future<T> future) {
        try {
            return future.get(DEFAULT_TIMEOUT,
                    timeUnitSeconds);
        } catch (Exception e) {
            log.warn("获取返回结果失败!{}", e);
        }
        return null;
    }

    /**
     * 关闭连接
     */
    public void disConnect() {
        if (mc == null) {
            return;
        }
        mc.shutdown();
    }

    @Override
    public void run(String... args) throws Exception {
        System.out.println("The Runner start to initialize ...");
        try {
            mc = new MemcachedClient(new InetSocketAddress(memcacheSource.getServers(),memcacheSource.getPort()));
        } catch (IOException e) {
            log.error("inint MemcachedClient failed ",e);
        }
    }
}
```

## 测试使用

```ejs
memcached.<UserVo>set(vo.getLoginName(), 43200, vo);
UserVo vo = memcached.get(loginToken);
```