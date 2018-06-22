# Cache

缓存是大型项目中不可缺少的一部分。

对于缓存的解决方案也也因人而异，所以我们对缓存提供封装，而不做限定。

您可以自行封装您喜欢的缓存嵌入到系统中，当然我们也提供默认的缓存。

## 使用

### application.yml

您可以在配置文件中对缓存进行配置

```
/**
 * @author zhangbowen
 */
@ConfigurationProperties(prefix = "faster.cache")
@Data
public class CacheProperties {
    /**
     * 是否开启缓存
     */
    private boolean enabled = true;
    /**
     * 是否为本地模式
     */
    private boolean local = true;
}
```

### CacheFacade

我们使用外观模式对缓存进行封装，以达到对外一致。

您可以通过CacheFacade操作管理缓存。

```
   /**
     * 设置缓存
     *
     * @param key 缓存键
     * @param value 缓存value
     * @param exp   失效时间(秒)
     * @param <V>  泛型
     */
    public static <V> void set(String key, V value, long exp) {
        cacheService.set(key, value, exp);
    }

    /**
     * 删除缓存数据
     * @param <V>  泛型
     * @param key 缓存键
     * @return V 泛型
     */
    public static <V> V delete(String key) {
        return (V) cacheService.delete(key);
    }

    /**
     * 清空以cachePrefix开头的缓存
     * @param cachePrefix 缓存前缀
     */
    public static void clear(String cachePrefix) {
        cacheService.clear(cachePrefix);
    }

    /**
     * 获取以cachePrefix开头的缓存数量
     *
     * @param cachePrefix 缓存前缀
     * @return 缓存数量
     */
    public static int size(String cachePrefix) {
        return cacheService.size(cachePrefix);
    }

    /**
     * 获取以cachePrefix开头的缓存键列表
     * @param cachePrefix 缓存前缀
     * @param <K>  泛型
     * @return 返回缓存列表
     */
    public static <K> Set<K> keys(String cachePrefix) {
        return cacheService.keys(cachePrefix);
    }

    /**
     * 获取以cachePrefix开头的缓存值
     * @param cachePrefix 缓存前缀
     * @param <V>  泛型
     * @return 返回缓存列表
     */
    public static <V> Collection<V> values(String cachePrefix) {
        return cacheService.values(cachePrefix);
    }
```

## 默认支持

系统为您内置了两种缓存，LocalCacheService与RedisCacheService。

通常您无需关心其创建过程。下面我们来介绍两者。

### LocalCacheService

默认情况下，我们使用本地缓存LocalCacheService，它将缓存存储在本地。我们使用ConcurrentReferenceHashMap保证其并发情况下的安全引用与资源释放。

### RedisCacheService

我们同样提供了RedisCacheService作为外部缓存存储。您可以通过配置进行切换(local=false)。

您可以对redis进行额外配置，通过spring所提供的redis配置。

## 自定义缓存

如果您不习惯使用redis作为缓存，您也可以集成其他缓存。

### ICacheService

您需要实现ICacheService所有方法，并在Spring中注册您的缓存。

ICacheService代码如下：

```
public interface ICacheService<V> {
    /**
     * 设置缓存
     *
     * @param key 缓存键
     * @param value 缓存值
     * @param exp   失效时间(秒)
     */
    void set(String key, V value, long exp);

    /**
     * 删除缓存数据
     *
     * @param key 缓存键
     * @return V 泛型
     */
    V delete(String key);

    /**
     * 获取缓存数据,如果关键字不存在返回null
     *
     * @param key 缓存键
     * @return 缓存实体
     */
    V get(String key);

    /**
     * 清空以cacheService开头的缓存
     * @param cachePrefix 缓存前缀
     */
    void clear(String cachePrefix);

    /**
     * 查询以cachePrefix开头的cache数量
     * @param cachePrefix 缓存前缀
     * @return 数量
     */
    int size(String cachePrefix);

    /**
     * 查询以cachePrefix开头的keys
     * @param cachePrefix 缓存前缀
     * @return 缓存列表
     */
    Set<String> keys(String cachePrefix);

    /**
     * 查询以cachePrefix开头的值列表
     * @param cachePrefix 缓存前缀
     * @return 缓存列表
     */
    Collection<V> values(String cachePrefix);
}
```


我们依然建议您在本地开发环境时使用本地缓存，如果您认为这是多余的，您可以去除@ConditionalOnProperty注解。

注册缓存代码如下：

```
    @Bean("cacheService")
    @ConditionalOnProperty(prefix = "faster.cache", name = "local", havingValue = "false")
    public ICacheService customCacheService() {
        return new CustomCacheService();
}
```
