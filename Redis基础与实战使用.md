#  Redis

## 基础篇

### 1 通用命令

keys-----符合模板要求的所有key（keys *  查询所有key）

del key------删除一个key

del key1 key2 ------删除多个key(返回值为删除key的数量)

exists key ------判断key是否存在

expire  key  seconds---------给一个key设置有效期

ttl key----------查看一个key的剩余有效时间（-1表示永久有效，-2表示已过期）



### 2 String类型

Redis中的String类型是key-value形式。value可以有多种表现形式。

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\Sting-value类型.png)

String常用命令

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\String常用命令.png)

### 3 key的层级格式

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\key的层级结构.png)

在可视化界面即可看出层级关系



如果value是一个对象，则将对象序列化成Json字符串存储

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\对象序列化成字符串.png)

修改对象的某个字段很不方便





### 4 Hash类型

key-value形式，但是这里的value是一个HashMap

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\Hash类型结构.png)



常见命令

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\Hash类型常见命令.png)

### 5 List类型

**结构**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\List类型结构.png)

 **特征**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\List特征.png)

**命令**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\List常用命令.png)

List模拟栈和队列

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\List模拟栈和队列.png)

### 6 Set类型

结构

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\set类型.png)

单个集合常用命令

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\set常用命令.png)

多个集合常用命令

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\set多个集合操作.png)

### 7 SortedSet类型

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\Sortedset类型结构.png)

常用命令

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\Sortedset常用命令.png)

所有的排名默认都是升序，如果要降序则在命令的Z后面添加REV即可

### 8 Redis的java客户端

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\redis的java客户端.png)



jedis、lettuce、Redisson



### 9 Jedis

步骤

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\jedis步骤1.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\jedis步骤2.png)

### 10 SpringDataRedis

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\SpringDataRedis.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\SpringDataRedis快速入门.png)

### 11 SpringDataRedis快速入门

引入依赖

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\引入SpringDataRedis依赖.png)

配置文件

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\SpringDataRedis配置文件.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\RedisTemplate注入.png)



```
@Resource
    private RedisTemplate redisTemplate;

    @Test
    public void redisTest(){
        redisTemplate.opsForValue().set("name","hh");
        System.out.println(redisTemplate.opsForValue().get("name"));
    }
    
    
          <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```



### 12 RedisTemplate序列化

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\RedisTemplate序列化.png)





自定义序列化格式



引入jackson依赖

```
<dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
```



```
@Bean
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
        //创建RedisTemplate对象
        RedisTemplate<String,Object> template = new RedisTemplate<>();

        //设置连接工厂
        template.setConnectionFactory(redisConnectionFactory);

        //创建Json序列化工具
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
        //设置key的序列化
        template.setKeySerializer(RedisSerializer.string());
        template.setHashKeySerializer(RedisSerializer.string());
        //设置Value序列化
        template.setValueSerializer(jsonRedisSerializer);
        template.setHashValueSerializer(jsonRedisSerializer);
       
        //返回
        return template;
    }
```



### 13 SpringRedisTemplate

RedisTemplate自定义序列化的不足

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\自定义序列化的不足.png)



解决方法

使用SpringRedisTemplate

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\SpringRedisTemplate.png)



```
public class StringRedisTemplateTest {

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    private static final ObjectMapper objectMapper = new ObjectMapper();//SpringMvc默认使用的json序列化工具

    @Test
    public void redisTest() throws JsonProcessingException {
        //创建对象
        User user = new User("张三",20);

        //手动序列化
        String json = objectMapper.writeValueAsString(user);

        //写入数据
        stringRedisTemplate.opsForValue().set("user",json);

        //获取数据
        String userJson = stringRedisTemplate.opsForValue().get("user");

        //手动反序列化
        User user1 = objectMapper.readValue(userJson, User.class);
        System.out.println(user1);
    }
}
```



### 14 序列化总结

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\序列化总结.png)

##  实战篇

 