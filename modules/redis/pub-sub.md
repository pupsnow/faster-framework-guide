# 发布/订阅(pub/sub)

PubSub 模式，是 Publish/Subscribe 的缩写，意为“发布/订阅”模式。

订阅者（Sub）通过SUBSCRIBE命令向redis 服务订阅频道（channel），当发布者通过PUBLISH 命令向channel发布命令时，订阅该频道的客户端都会受到此消息。（请不要将发布/订阅与点对点混淆）

> JMS规范目前支持两种消息模型：点对点（point to point， queue）和发布/订阅（publish/subscribe，topic）。其中点对点模式中，一个Queue可以有多个消费者注册等待消费，但消息由一个消费者从Queue中取出后其它消费者将无法消费，即一个消息仅可由一个消费者消费，多个消费者注册。而发布/订阅模式发布到topic的消息，所有订阅者均会消费。

> 点对点实现了一个可靠的负载均衡，而发布订阅则是消息的拷贝。

spring-data-redis所提供的发布/订阅使用方式复杂切繁琐，而且需要手动配置在类中，无法做到解耦。故我们优化了其配置使用方式。

## 快速使用

### 订阅
您仅需要新建一个实现MessageListener（SpringDataRedis提供）的类，并在其类上方添加@RedisListener（Faster提供）注解，以此来监听channel即可。

```
@RedisListener("channel1")
public class ChannelTestListener implements MessageListener {
    @Override
    public void onMessage(Message message, byte[] pattern) {
        System.out.println(message);
    }
}
```

@RedisListener注解支持监听多个channel：@RedisListener({"channel1","channel2"})

### 发布

发布时仍使用SpringDataRedis提供的方式:

```
redisTemplate.convertAndSend("channel1","hello");
```
