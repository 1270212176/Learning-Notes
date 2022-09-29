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

### 1 短信登录

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\短信登录\短信登录流程.png)



#### 1 发送短信验证码

service层代码

```
public Result sendCode(String phone, HttpSession session) {
        //校验手机号
        if (RegexUtils.isPhoneInvalid(phone)){
            //如果不符合，返回错误信息
            return Result.fail("手机格式错误");
        }

        //符合生成验证码
        String code = RandomUtil.randomNumbers(6);//生成6为随机数，import cn.hutool.core.util.RandomUtil;下的工具类，需要导入依赖

        //将验证码保存在session中
        session.setAttribute("code",code);

        //发送验证码
        log.info("验证码发送成功，验证码{}",code);
        //需要调用第三方短信平台，如阿里云，京东万象，详情见优选商城，这里使用日志打印code

        //返回ok
        return Result.ok();
    }
```



#### 2 短信验证码登录与注册功能

```
 public Result login(LoginFormDTO loginForm, HttpSession session) {
        //校验手机号

        if (RegexUtils.isPhoneInvalid(loginForm.getPhone())){
            //如果不符合，返回错误信息
            return Result.fail("手机格式错误");
        }


        //校验验证码
        Object objectCode = session.getAttribute("code");
        String code = loginForm.getCode();
        if (objectCode == null ||!objectCode.toString().equals(code)){
            //不一致报错
            return Result.fail("验证码错误");
        }

        //一致根据手机号查询用户
        User user = query().eq("phone", loginForm.getPhone()).one();


        //判断用户是否存在
        if (user == null){
            //不存在，创建新用户并写入数据库
            user = createUserWithPhone(loginForm.getPhone());
        }

        //将用户保存到session中
        session.setAttribute("user",user);
        return Result.ok();
    }


```

```
    private User createUserWithPhone(String phone) {
        User user = new User();
        user.setPhone(phone);
        user.setNickName(SystemConstants.USER_NICK_NAME_PREFIX + RandomUtil.randomString(6));
        save(user);
        return user;
    }
```

#### 3 登录校验功能



登录过滤器

```
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //获取session
        HttpSession session = request.getSession();
        //获取session中的用户
        Object user = session.getAttribute("user");
        //判断用户是否存在
        if (user == null){
            //不存在拦截
            response.setStatus(401);
            return false;
        }

        //存在，保存用户信息到ThreadLocal
       UserHolder1.saveUser((User)user);

        //放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
            //移除用户
        UserHolder1.removeUser();
    }
}
```



添加过滤器

```
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
        .excludePathPatterns(
                "/shop/**",
                "/voucher/**",
                "/shop-type/**",
                "/upload/**",
                "/blog/hot",
                "/user/code",
                "/user/login"
        );
    }
}
```



#### 4 集群的session共享问题

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\短信登录\集群session共享问题.png)

#### 5 Redis代替session业务

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\短信登录\redis代替session1.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\短信登录\redis代替session2.png)



发送验证码

```
public Result sendCode(String phone, HttpSession session) {
        //校验手机号
        if (RegexUtils.isPhoneInvalid(phone)){
            //如果不符合，返回错误信息
            return Result.fail("手机格式错误");
        }

        //符合生成验证码
        String code = RandomUtil.randomNumbers(6);//生成6为随机数，import cn.hutool.core.util.RandomUtil;下的工具类，需要导入依赖

        //将验证码保存在redis中
        stringRedisTemplate.opsForValue().set(RedisConstants.LOGIN_CODE_KEY + phone,code,RedisConstants.LOGIN_CODE_TTL,
                TimeUnit.MINUTES);//设置有效期为两分钟

        //发送验证码
        log.info("验证码发送成功，验证码{}",code);
        //需要调用第三方短信平台，如阿里云，京东万象，详情见优选商城，这里使用日志打印code

        //返回ok
        return Result.ok();
    }
```



登录与注册

```
 public Result login(LoginFormDTO loginForm, HttpSession session) {
        //校验手机号

        if (RegexUtils.isPhoneInvalid(loginForm.getPhone())){
            //如果不符合，返回错误信息
            return Result.fail("手机格式错误");
        }


        //从Redis中拿到校验码
        String stringCode = stringRedisTemplate.opsForValue().get(RedisConstants.LOGIN_CODE_KEY + loginForm.getPhone());
        String code = loginForm.getCode();
        if (stringCode == null ||!stringCode.equals(code)){
            //不一致报错
            return Result.fail("验证码错误");
        }

        //一致根据手机号查询用户
        User user = query().eq("phone", loginForm.getPhone()).one();


        //判断用户是否存在
        if (user == null){
            //不存在，创建新用户并写入数据库
            user = createUserWithPhone(loginForm.getPhone());
        }



        //随机生成token作为登录令牌
        String token = UUID.randomUUID().toString(true);

        //将User对象转为Hash存储
        UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
        Map<String, Object> map = BeanUtil.beanToMap(userDTO,new HashMap<>(),
        CopyOptions.create()
                .setIgnoreNullValue(true)
                .setFieldValueEditor((fileName,fileValue)->fileValue.toString())
        );


        //将用户信息保存到redis中
        stringRedisTemplate.opsForHash().putAll(RedisConstants.LOGIN_USER_KEY + token,map);

        //设置token有效期
        stringRedisTemplate.expire(RedisConstants.LOGIN_USER_KEY + token,RedisConstants.LOGIN_USER_TTL,TimeUnit.MINUTES);

        //返回token
        return Result.ok(token);
    }
```

```
private User createUserWithPhone(String phone) {
        User user = new User();
        user.setPhone(phone);
        user.setNickName(SystemConstants.USER_NICK_NAME_PREFIX + RandomUtil.randomString(6));
        save(user);
        return user;
    }
```



登录过滤器

```
public class LoginInterceptor implements HandlerInterceptor {


    private StringRedisTemplate stringRedisTemplate;//LoginInterceptor类没有交给Spring管理，所以不会被自动装配

    public LoginInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //获取请求头中的token
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)){//判断是否为空
            //不存在拦截
            response.setStatus(401);
            return false;
        }


        //通过token获取redis中的用户
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(RedisConstants.LOGIN_USER_KEY + token);
        //判断用户是否存在
        if (userMap.isEmpty()){
            //不存在拦截
            response.setStatus(401);
            return false;
        }


        //将查询到的Hash对象转为UserDto对象
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);//false表示不忽略错误



        //存在，保存用户信息到ThreadLocal
       UserHolder.saveUser(userDTO);

        //刷新token有效期
        stringRedisTemplate.expire(RedisConstants.LOGIN_USER_KEY + token,RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);

        //放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
            //移除用户
        UserHolder.removeUser();
    }
}
```



添加过滤器到Spring

```
@Configuration
public class MvcConfig implements WebMvcConfigurer {

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor(stringRedisTemplate))
        .excludePathPatterns(
                "/shop/**",
                "/voucher/**",
                "/shop-type/**",
                "/upload/**",
                "/blog/hot",
                "/user/code",
                "/user/login"
        );
    }
}
```

#### 6 优化登录刷新问题

新增一个刷新状态过滤器

```
public class RefreshTokenInterceptor implements HandlerInterceptor {


    private StringRedisTemplate stringRedisTemplate;//LoginInterceptor类没有交给Spring管理，所以不会被自动装配

    public RefreshTokenInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //获取请求头中的token
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)){//判断是否为空
           return true;
        }


        //通过token获取redis中的用户
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(RedisConstants.LOGIN_USER_KEY + token);
        //判断用户是否存在
        if (userMap.isEmpty()){
            return true;
        }


        //将查询到的Hash对象转为UserDto对象
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);//false表示不忽略错误



        //存在，保存用户信息到ThreadLocal
       UserHolder.saveUser(userDTO);

        //刷新token有效期
        stringRedisTemplate.expire(RedisConstants.LOGIN_USER_KEY + token,RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);

        //放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
            //移除用户
        UserHolder.removeUser();
    }
}

```

**这个过滤器会拦截所有请求，因为前端每次访问服务器都会携带token，所以如果是已处于登录状态，每次发起请求都会刷新token**

此时过滤登录器改为

```
public class LoginInterceptor implements HandlerInterceptor {


//    private StringRedisTemplate stringRedisTemplate;//LoginInterceptor类没有交给Spring管理，所以不会被自动装配
//
//    public LoginInterceptor(StringRedisTemplate stringRedisTemplate) {
//        this.stringRedisTemplate = stringRedisTemplate;
//    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
       //判断是否需要拦截(ThreadLocal中是否有用户)
        if (UserHolder.getUser() == null){
            //没有，需要拦截
            response.setStatus(401);
            return false;
        }
        //有用户，放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
            //移除用户
        UserHolder.removeUser();
    }
}
```



**添加过滤器到过滤链中并设置过滤顺序**

```
@Configuration
public class MvcConfig implements WebMvcConfigurer {

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
        .excludePathPatterns(
                "/shop/**",
                "/voucher/**",
                "/shop-type/**",
                "/upload/**",
                "/blog/hot",
                "/user/code",
                "/user/login"
        ).order(1);//order是设置拦截器的执行顺序的，小的先执行

        registry.addInterceptor(new RefreshTokenInterceptor(stringRedisTemplate)).addPathPatterns("/**").order(0);
    }
}
```

### 2 商户查询缓存

#### 1 什么是缓存

缓存就是数据交换的缓冲区，，是存贮数据的临时地方，一般读写性能高

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存的作用与成本.png)

#### 2 添加商户缓存

流程图

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\商户缓存流程图.png)



代码



```
 public Result queryById(Long id) {
        //从Redis查询商户缓存
        String shopJson = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //判断是否存在
        if(StrUtil.isNotBlank(shopJson)){
            //存在，直接返回
            Shop shop = JSONUtil.toBean(shopJson, Shop.class);
            return Result.ok(shop);
        }



        //不存在，根据id查询数据库
        Shop shop = getById(id);

        //判断是否存在
        if(shop == null){
            //不存在，直接返回错误
            return Result.fail("店铺不存在");
        }

        //存在，将信息写入redis，返回
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,JSONUtil.toJsonStr(shop));

        return Result.ok(shop);
    }
```



#### 3 缓存更新策略

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\更新缓存策略.png)



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\主动更新.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\先操作缓存还是先操作数据库.png)



#### 4 给查询商铺的缓存添加超时剔除和主动更新的策略（缓存与数据库双写一致）

步骤

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\查询商户缓存步骤.png)



```
public Result queryById(Long id) {
        //从Redis查询商户缓存
        String shopJson = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //判断是否存在
        if(StrUtil.isNotBlank(shopJson)){
            //存在，直接返回
            Shop shop = JSONUtil.toBean(shopJson, Shop.class);
            return Result.ok(shop);
        }



        //不存在，根据id查询数据库
        Shop shop = getById(id);

        //判断是否存在
        if(shop == null){
            //不存在，直接返回错误
            return Result.fail("店铺不存在");
        }

        //存在，将信息写入redis,设置过期时间，返回
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,JSONUtil.toJsonStr(shop),RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

        return Result.ok(shop);
    }
```



```
@Transactional
    public Result updateShop(Shop shop) {
        Long id = shop.getId();
        if (id == null){
            return Result.fail("商铺id不能为空");
        }
        //更新数据库
        updateById(shop);

        //删除缓存
        stringRedisTemplate.delete(RedisConstants.CACHE_SHOP_KEY + id);

        return Result.ok();
    }
```



#### 5 缓存穿透解决方案

##### 1 什么是缓存穿透

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存穿透.png)

##### 2 两种解决方案

######  	1 缓存空对象

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存空对象.png)



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存空对象流程图.png)

###### 2 布隆过滤（基于二进制实现）

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\布隆过滤.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\布隆过滤器流程图.png)

##### 3 缓存空对象实现步骤

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存空对象解决步骤.png)



代码实现

```
 public Result queryById(Long id) {
        //从Redis查询商户缓存
        String shopJson = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //判断是否存在
        if(StrUtil.isNotBlank(shopJson)){
            //存在，直接返回
            Shop shop = JSONUtil.toBean(shopJson, Shop.class);
            return Result.ok(shop);
        }

        //判断命中的是否是空值
        if (shopJson != null){
            //如果存在，在第一个if中就会返回，只剩下空字符串和！=null两种情况
            //返回一个错误信息
            return Result.fail("店铺不存在");
        }


        //不存在，根据id查询数据库
        Shop shop = getById(id);

        //判断是否存在
        if(shop == null){
            //不存在，直接返回错误,并将空值写入redis中
            stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,""
                    ,RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);

            return Result.fail("店铺不存在");
        }

        //存在，将信息写入redis,设置过期时间，返回
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,JSONUtil.toJsonStr(shop),
                RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

        return Result.ok(shop);
    }
```



##### 4 归纳缓存穿透

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\穿透归纳.png)

#### 3 缓存雪崩

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存雪崩.png)

#### 4 缓存击穿

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存击穿.png)



两种解决方案流程图

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存击穿解决方案.png)



优缺点

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存击穿解决方案优缺点.png)



##### 1 基于互斥锁解决

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\互斥锁流程.png)



实现

```
    public Result queryById(Long id) throws InterruptedException {

        //互斥锁解决缓存击穿
        Shop shop = queryWithMutex(id);
        if (shop==null){
            return Result.fail("店铺不存在");
        }
        return Result.ok(shop);
    }
```



```
//互斥锁
    public Shop queryWithMutex(Long id) throws InterruptedException {
        //从Redis查询商户缓存
        String shopJson = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //判断是否存在
        if(StrUtil.isNotBlank(shopJson)){
            //存在，直接返回
            Shop shop = JSONUtil.toBean(shopJson, Shop.class);
            return shop;
        }

        //判断命中的是否是空值
        if (shopJson != null){
            //如果存在，在第一个if中就会返回，只剩下空字符串和！=null两种情况
            //返回一个错误信息
            return null;
        }


        //实现缓存重建
        //获取互斥锁
        String lockKey = RedisConstants.LOCK_SHOP_KEY + id;
        boolean isLock = tryLock(lockKey);

        //判断是否获取成功
        if (!isLock){
            //失败，休眠一段时间继续获取
            Thread.sleep(50);
            return queryWithMutex(id);
        }


        //成功根据id查询数据库
        Shop shop = getById(id);

        //模拟重建延时
        Thread.sleep(200);

        //判断是否存在
        if(shop == null){
            //不存在，直接返回错误,并将空值写入redis中
            stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,""
                    ,RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);

            return null;
        }

        //存在，将信息写入redis,设置过期时间，返回
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,JSONUtil.toJsonStr(shop),
                RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

        //释放互斥锁
        unlock(lockKey);

        return shop;
    }
```



```
public boolean tryLock(String key){
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);//类似于setnx方法
        return BooleanUtil.isTrue(flag);
    }
```



```
public void unlock(String key){
        stringRedisTemplate.delete(key);
    }
```

