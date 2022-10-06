

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

        //将User对象转为Hash存储------也可以将一整个用户对象存入redis只不过修改性比较差
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
            //判断是否为空白，空白的定义是null,"",不可见字符（如空格）
            Shop shop = JSONUtil.toBean(shopJson, Shop.class);
            return Result.ok(shop);
        }

        //判断命中的是否是空值
        if (shopJson != null){
            //如果存在，在第一个if中就会返回，只剩下空字符串和！=null两种情况
            //返回的是""，就是命中了空对象，因为在下面存储的空对象就是""
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

#### 6 缓存雪崩

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\缓存雪崩.png)

#### 7 缓存击穿

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

##### 2 逻辑过期解决缓存击穿

前提：以前将热门key写入redis缓存中

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\商户查询缓存\逻辑过期时间.png)



代码实现

逻辑过期时间类

```
@Data
public class RedisData {
    private LocalDateTime expireTime;
    private Object data;
}

```

```
//数据预热----提前将热点数据写入Redis缓存中
    public void saveShop2Redis(Long id,Long expireSeconds){
        //1 查询店铺数据
        Shop shop = getById(id);
        try {
            Thread.sleep(200);
        }catch (Exception e){
            e.printStackTrace();
        }
        //封装过期时间
        RedisData redisData = new RedisData();
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(expireSeconds));//在当前时间加上多少秒
        redisData.setData(shop);
        //写入Redis
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,JSONUtil.toJsonStr(redisData));
    }
```





```
//逻辑过期解决缓存穿透
    //不用考虑缓存穿透，因为用户会提前对数据进行预热，所以都能在redis缓存中访问到，没访问到就代表不是热点key
    public Shop queryWithLogicalExpire(Long id){
        //从Redis查询商户缓存
        String shopJson = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //判断是否存在
        if(StrUtil.isBlank(shopJson)){
            //不存在，返回null
            return null;
        }


        //命中，需要先把Json反序列为对象
        RedisData redisData = JSONUtil.toBean(shopJson, RedisData.class);
        JSONObject data = (JSONObject)redisData.getData();
        Shop shop = JSONUtil.toBean(data, Shop.class);
        LocalDateTime expireTime = redisData.getExpireTime();

        //判断是否过期
        if (expireTime.isAfter(LocalDateTime.now())){
            //未过期，直接返回店铺信息
            return shop;
        }



        //已过期，需要缓存重建
        //缓存重建
        //获取互斥锁
        String lockKey = RedisConstants.LOCK_SHOP_KEY + id;
        boolean isLock = tryLock(lockKey);
        //判断是否获取成功
        if (isLock){
            //成功，开启独立线程，实现缓存重建
            CACHE_REBUILD_EXECUTOR.submit(()->{

                try {

                    //重建缓存
                    this.saveShop2Redis(id,20L);
                }catch (Exception e){
                    throw new RuntimeException(e);
                }finally {
                    //释放锁
                    unlock(lockKey);
                }



            });
        }


        //返回过期的商铺信息
        return shop;
    }
```



开启多个线程进行数据重建

```
private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);
```



#### 8 封装Redis工具类



```
@Slf4j
@Component
public class CacheClient {

    private final StringRedisTemplate stringRedisTemplate;

    private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);

    public CacheClient(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    public void set(String key, Object value, Long time, TimeUnit unit) {
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(value), time, unit);
    }

    public void setWithLogicalExpire(String key, Object value, Long time, TimeUnit unit) {
        // 设置逻辑过期
        RedisData redisData = new RedisData();
        redisData.setData(value);
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(time)));
        // 写入Redis
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData));
    }

    public <R,ID> R queryWithPassThrough(
            String keyPrefix, ID id, Class<R> type, Function<ID, R> dbFallback, Long time, TimeUnit unit){
        //Function<ID, R> dbFallback为查询数据库的方法
        String key = keyPrefix + id;
        // 1.从redis查询商铺缓存
        String json = stringRedisTemplate.opsForValue().get(key);
        // 2.判断是否存在
        if (StrUtil.isNotBlank(json)) {
            // 3.存在，直接返回
            return JSONUtil.toBean(json, type);
        }
        // 判断命中的是否是空值
        if (json != null) {
            // 返回一个错误信息
            return null;
        }

        // 4.不存在，根据id查询数据库
        R r = dbFallback.apply(id);
        // 5.不存在，返回错误
        if (r == null) {
            // 将空值写入redis
            stringRedisTemplate.opsForValue().set(key, "", CACHE_NULL_TTL, TimeUnit.MINUTES);
            // 返回错误信息
            return null;
        }
        // 6.存在，写入redis
        this.set(key, r, time, unit);
        return r;
    }

    public <R, ID> R queryWithLogicalExpire(
            String keyPrefix, ID id, Class<R> type, Function<ID, R> dbFallback, Long time, TimeUnit unit) {
        String key = keyPrefix + id;
        // 1.从redis查询商铺缓存
        String json = stringRedisTemplate.opsForValue().get(key);
        // 2.判断是否存在
        if (StrUtil.isBlank(json)) {
            // 3.存在，直接返回
            return null;
        }
        // 4.命中，需要先把json反序列化为对象
        RedisData redisData = JSONUtil.toBean(json, RedisData.class);
        R r = JSONUtil.toBean((JSONObject) redisData.getData(), type);
        LocalDateTime expireTime = redisData.getExpireTime();
        // 5.判断是否过期
        if(expireTime.isAfter(LocalDateTime.now())) {
            // 5.1.未过期，直接返回店铺信息
            return r;
        }
        // 5.2.已过期，需要缓存重建
        // 6.缓存重建
        // 6.1.获取互斥锁
        String lockKey = LOCK_SHOP_KEY + id;
        boolean isLock = tryLock(lockKey);
        // 6.2.判断是否获取锁成功
        if (isLock){
            // 6.3.成功，开启独立线程，实现缓存重建
            CACHE_REBUILD_EXECUTOR.submit(() -> {
                try {
                    // 查询数据库
                    R newR = dbFallback.apply(id);
                    // 重建缓存
                    this.setWithLogicalExpire(key, newR, time, unit);
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }finally {
                    // 释放锁
                    unlock(lockKey);
                }
            });
        }
        // 6.4.返回过期的商铺信息
        return r;
    }

    public <R, ID> R queryWithMutex(
            String keyPrefix, ID id, Class<R> type, Function<ID, R> dbFallback, Long time, TimeUnit unit) {
        String key = keyPrefix + id;
        // 1.从redis查询商铺缓存
        String shopJson = stringRedisTemplate.opsForValue().get(key);
        // 2.判断是否存在
        if (StrUtil.isNotBlank(shopJson)) {
            // 3.存在，直接返回
            return JSONUtil.toBean(shopJson, type);
        }
        // 判断命中的是否是空值
        if (shopJson != null) {
            // 返回一个错误信息
            return null;
        }

        // 4.实现缓存重建
        // 4.1.获取互斥锁
        String lockKey = LOCK_SHOP_KEY + id;
        R r = null;
        try {
            boolean isLock = tryLock(lockKey);
            // 4.2.判断是否获取成功
            if (!isLock) {
                // 4.3.获取锁失败，休眠并重试
                Thread.sleep(50);
                return queryWithMutex(keyPrefix, id, type, dbFallback, time, unit);
            }
            // 4.4.获取锁成功，根据id查询数据库
            r = dbFallback.apply(id);
            // 5.不存在，返回错误
            if (r == null) {
                // 将空值写入redis
                stringRedisTemplate.opsForValue().set(key, "", CACHE_NULL_TTL, TimeUnit.MINUTES);
                // 返回错误信息
                return null;
            }
            // 6.存在，写入redis
            this.set(key, r, time, unit);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }finally {
            // 7.释放锁
            unlock(lockKey);
        }
        // 8.返回
        return r;
    }

    private boolean tryLock(String key) {
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
        return BooleanUtil.isTrue(flag);
    }

    private void unlock(String key) {
        stringRedisTemplate.delete(key);
    }
}

```

### 3 优惠券秒杀

#### 1 全局ID

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\全局ID生成器.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\全局ID安全性.png)



代码实现

```
@Component
public class RedisIdWork {

    //开始时间戳
    private static final long BEGIN_TIMESTAMP = 1640995200L;

    //序列号的位数
    private static final long COUNT_BITS = 32;


    @Resource
    private StringRedisTemplate stringRedisTemplate;

    public long nexId(String prefixKey){
        //生成时间戳
        LocalDateTime localDateTime = LocalDateTime.now();
        long nowSeconds = localDateTime.toEpochSecond(ZoneOffset.UTC);
        long timeStamp = nowSeconds-BEGIN_TIMESTAMP;


        //生成序列号
        //获取当前日期，精确到天
        String date = localDateTime.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
        //自增长
        long count = stringRedisTemplate.opsForValue().increment("icr" + prefixKey + date);//incrby 方法如果key为空会自动创建key


        //拼接并返回
        //timeStamp向左移动32位, | 表示或运算
        //long类型是64位的,前32为时间戳，后32则补0，此时再将后32位与count进行或运算
        return timeStamp<<COUNT_BITS | count;
    }

    //生成开始时间戳示例方法
    public void currentTime() {
        LocalDateTime time = LocalDateTime.of(2022,1,1,0,0,0);
        long seconds = time.toEpochSecond(ZoneOffset.UTC);
        System.out.println(seconds);
    }
}
```



全局ID生成方法

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\全局ID生成方法.png)



#### 2 实现优惠券秒杀下单

流程

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\秒杀下单实现流程.png)



代码实现

```
  @Transactional
    public Result seckillVoucher(Long voucherId) {

        //查询优惠券
        SeckillVoucher voucher = iSeckillVoucherService.getById(voucherId);

        //判断秒杀是否开始
        if (voucher.getBeginTime().isAfter(LocalDateTime.now())){
            //尚未开始
            return Result.fail("秒杀尚未开始");
        }

        //判断秒杀是否结束
        if (voucher.getBeginTime().isBefore(LocalDateTime.now())){
            //已经结束
            return Result.fail("秒杀已经结束");
        }

        //判断库存是否充足
        if (voucher.getStock()<1){
            return Result.fail("库存不足");
        }

        //减库存
        boolean success = iSeckillVoucherService.update()
                .setSql("stock=stock-1")
                .eq("voucher_id",voucherId)
                .update();

        if (!success){
            return Result.fail("库存不足");
        }

        //创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        //订单id
        long orderId = redisIdWork.nexId("order");
        voucherOrder.setId(orderId);
        //用户id
        Long userId = UserHolder.getUser().getId();
        voucherOrder.setUserId(userId);
        //代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);

        //返回订单id
        return Result.ok(orderId);
    }
```



#### 3 超卖问题

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\超卖问题.png)



##### 1 乐观锁

什么是乐观锁？

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\乐观锁.png)



**版本号法**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\版本号法.png)

**CAS法（用修改的数据代替版本号）**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\CAS法.png)

##### 2 CAS乐观锁法解决超卖问题

代码实现

```
 @Transactional
    public Result seckillVoucher(Long voucherId) {

        //查询优惠券
        SeckillVoucher voucher = iSeckillVoucherService.getById(voucherId);

        //判断秒杀是否开始
        if (voucher.getBeginTime().isAfter(LocalDateTime.now())){
            //尚未开始
            return Result.fail("秒杀尚未开始");
        }

        //判断秒杀是否结束
        if (voucher.getBeginTime().isBefore(LocalDateTime.now())){
            //已经结束
            return Result.fail("秒杀已经结束");
        }

        //判断库存是否充足
        if (voucher.getStock()<1){
            return Result.fail("库存不足");
        }

        //减库存
        boolean success = iSeckillVoucherService.update()
                .setSql("stock=stock-1")
                .eq("voucher_id",voucherId).gt("stock",0)//修改前先判断库存是否还大于0
                .update();

        if (!success){
            return Result.fail("库存不足");
        }

        //创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        //订单id
        long orderId = redisIdWork.nexId("order");
        voucherOrder.setId(orderId);
        //用户id
        Long userId = UserHolder.getUser().getId();
        voucherOrder.setUserId(userId);
        //代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);

        //返回订单id
        return Result.ok(orderId);
    }
```



##### 3 解决方案优缺点

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\超卖解决方案优缺点.png)



#### 4 一人一单

获得动态代理对象需要添加依赖和在启动类加@EnableAspectJAutoProxy(exposeProxy = true)注解

```
<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
        </dependency>
        
        @EnableAspectJAutoProxy(exposeProxy = true)
```



```
public Result seckillVoucher(Long voucherId) {

        //查询优惠券
        SeckillVoucher voucher = iSeckillVoucherService.getById(voucherId);

        //判断秒杀是否开始
        if (voucher.getBeginTime().isAfter(LocalDateTime.now())){
            //尚未开始
            return Result.fail("秒杀尚未开始");
        }

        //判断秒杀是否结束
        if (voucher.getBeginTime().isBefore(LocalDateTime.now())){
            //已经结束
            return Result.fail("秒杀已经结束");
        }

        //判断库存是否充足
        if (voucher.getStock()<1){
            return Result.fail("库存不足");
        }
        Long userId = UserHolder.getUser().getId();
        synchronized (userId.toString().intern()) {
            //intern方法是保证每个userId字符串是同一个，保证同一个用户是同一把锁
            IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
            //获得代理对象来开启事务，因为Spring是通过动态代理来进行事务管理的，如果用this调用，用的是当前对象而不是代理对象
            return proxy.createVoucher(voucherId);
        }

    }
```



```
@Transactional
    public  Result createVoucher(Long voucherId){
        Long userId = UserHolder.getUser().getId();
        //一人一单
        //查询订单

        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();

        //判断是否存在
        if (count>0){
            //用户已经购买过
            return Result.fail("用户已购买过该优惠券");
        }


        //减库存
        boolean success = iSeckillVoucherService.update()
                .setSql("stock=stock-1")
                .eq("voucher_id",voucherId).gt("stock",0)//修改前先判断库存是否还大于0
                .update();

        if (!success){
            return Result.fail("库存不足");
        }

        //创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        //订单id
        long orderId = redisIdWork.nexId("order");
        voucherOrder.setId(orderId);
        //用户id

        voucherOrder.setUserId(userId);
        //代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);
        //返回订单id
            return Result.ok(orderId);
        }
```



#### 5 集群下的线程并发安全问题

每一个端口都对应的一个jvm虚拟机，每个jvm中的锁只对自己监控下的线程起作用，所以集群下的synchronized就不起作用



#### 6 分布式锁

##### 1 什么是分布式锁？

满足分布式系统或集群模式下多进程可见并且互斥的锁



##### 2 分布式锁流程

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\分布式锁过程.png)



##### 3 分布式锁实现方式

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\互斥锁实现方式.png)

##### 4 Redis实现分布式锁

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\基于Redis实现分布式锁.png)

代码实现

```
public interface ILock {
    public boolean tryLock(long timeoutSec);
    public void unlock();
}

```



```
public class SimpleRedisLock implements ILock{


    private StringRedisTemplate stringRedisTemplate;
    private String name;
    private static final String KEY_PREFIX = "lock:";

    public SimpleRedisLock(StringRedisTemplate stringRedisTemplate, String name) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.name = name;
    }

    @Override
    public boolean tryLock(long timeoutSec) {
        //获取线程标识
        long threadId = Thread.currentThread().getId();

        //获取锁
        Boolean success = stringRedisTemplate.opsForValue().setIfAbsent(KEY_PREFIX + name, threadId + "", timeoutSec, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(success);
    }

    @Override
    public void unlock() {
        //释放锁
        stringRedisTemplate.delete(KEY_PREFIX + name);
    }
}
```



```
public Result seckillVoucher(Long voucherId) {

        //查询优惠券
        SeckillVoucher voucher = iSeckillVoucherService.getById(voucherId);

        //判断秒杀是否开始
        if (voucher.getBeginTime().isAfter(LocalDateTime.now())){
            //尚未开始
            return Result.fail("秒杀尚未开始");
        }

        //判断秒杀是否结束
        if (voucher.getBeginTime().isBefore(LocalDateTime.now())){
            //已经结束
            return Result.fail("秒杀已经结束");
        }

        //判断库存是否充足
        if (voucher.getStock()<1){
            return Result.fail("库存不足");
        }
        Long userId = UserHolder.getUser().getId();

        //创建锁对象
        SimpleRedisLock simpleRedisLock = new SimpleRedisLock( stringRedisTemplate,"order:" + userId);

        //获取锁
        boolean isLock = simpleRedisLock.tryLock(1200);
        //判断锁是否获取成功
        if (!isLock){
            //不成功，返回错误信息
            return Result.fail("不允许重复下单");

        }

        try{
            //intern方法是保证每个userId字符串是同一个，保证同一个用户是同一把锁
            IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
            //获得代理对象来开启事务，因为Spring是通过动态代理来进行事务管理的，如果用this调用，用的是当前对象而不是代理对象
            return proxy.createVoucher(voucherId);
        }finally {
            simpleRedisLock.unlock();
        }




    }

    @Transactional
    public  Result createVoucher(Long voucherId){
        Long userId = UserHolder.getUser().getId();
        //一人一单
        //查询订单

        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();

        //判断是否存在
        if (count>0){
            //用户已经购买过
            return Result.fail("用户已购买过该优惠券");
        }


        //减库存
        boolean success = iSeckillVoucherService.update()
                .setSql("stock=stock-1")
                .eq("voucher_id",voucherId).gt("stock",0)//修改前先判断库存是否还大于0
                .update();

        if (!success){
            return Result.fail("库存不足");
        }

        //创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        //订单id
        long orderId = redisIdWork.nexId("order");
        voucherOrder.setId(orderId);
        //用户id

        voucherOrder.setUserId(userId);
        //代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);
        //返回订单id
            return Result.ok(orderId);
        }

```



##### 5 Redis分布式锁误删问题

解决方案每次释放锁的时候判断当前锁的标识是否一致

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\锁误删解决方案.png)

代码实现

```
public class SimpleRedisLock implements ILock{


    private StringRedisTemplate stringRedisTemplate;
    private String name;
    private static final String KEY_PREFIX = "lock:";
    private static final String ID_PREFIX = UUID.randomUUID().toString(true) + "-";

    public SimpleRedisLock(StringRedisTemplate stringRedisTemplate, String name) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.name = name;
    }

    @Override
    public boolean tryLock(long timeoutSec) {
        //获取线程标识
        String threadId = ID_PREFIX + Thread.currentThread().getId();

        //获取锁
        Boolean success = stringRedisTemplate.opsForValue().setIfAbsent(KEY_PREFIX + name, threadId, timeoutSec, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(success);
    }

    @Override
    public void unlock() {
        //获取线程标识
        String threadId = ID_PREFIX + Thread.currentThread().getId();

        //获取锁中的标识
        String id = stringRedisTemplate.opsForValue().get(KEY_PREFIX + name);

        //判断标识是否一致
        if (threadId.equals(id)){
            //一致
            //释放锁
            stringRedisTemplate.delete(KEY_PREFIX + name);
        }

    }
}
```



##### 6 Redis的Lua脚本确保多条命令执行的原子性

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\redis的Lua.png)



执行Lua脚本

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\执行Lua脚本.png)



释放锁的Lua脚本

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\释放锁的Lua脚本.png)



在Resource创建Lua文件--unlock.lua

```
--比较线程标识与锁的标识是否一致
if(redis.call('get',KEYS[1])==ARGV[1]) then
return redis.call('del',KEYS[1])
end
return 0
```



修改unlock函数

```
public class SimpleRedisLock implements ILock{


    private StringRedisTemplate stringRedisTemplate;
    private String name;
    private static final String KEY_PREFIX = "lock:";
    private static final String ID_PREFIX = UUID.randomUUID().toString(true) + "-";

    //初始化Lua脚本
    private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;

    static {
        UNLOCK_SCRIPT = new DefaultRedisScript<>();
        UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
        UNLOCK_SCRIPT.setResultType(Long.class);
    }


    public SimpleRedisLock(StringRedisTemplate stringRedisTemplate, String name) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.name = name;
    }

    @Override
    public boolean tryLock(long timeoutSec) {
        //获取线程标识
        String threadId = ID_PREFIX + Thread.currentThread().getId();

        //获取锁
        Boolean success = stringRedisTemplate.opsForValue().setIfAbsent(KEY_PREFIX + name, threadId, timeoutSec, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(success);
    }

//基于Lua脚本的释放锁.能够满足原子性
    @Override
    public void unlock() {
        //调用Lua脚本
        stringRedisTemplate.execute(
                UNLOCK_SCRIPT,
                Collections.singletonList(KEY_PREFIX + name),
                ID_PREFIX + Thread.currentThread().getId()
                );

    }

}
```



##### 7 redis分布式锁实现思路与特性

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\redis分布锁.png)

##### 8 Redisson实现分布式锁

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\锁的优化问题.png)



Redisson用来实现分布式锁的一种框架

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\Redisson.png)



1 添加依赖和配置

```
<dependency>
            <groupId>org.redisson</groupId>
            <artifactId>redisson</artifactId>
            <version>3.13.6</version>
        </dependency>
```

```
@Configuration
public class RedissonConfig {

    @Bean
    public RedissonClient redissonClient(){
        // 配置
        Config config = new Config();
        config.useSingleServer().setAddress("redis://127.0.0.1:6379");
        // 创建RedissonClient对象
        return Redisson.create(config);
    }
}
```

使用Redisson的分布式锁

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\使用Redisson的分布式锁.png)

```
 public Result seckillVoucher(Long voucherId) {

        //查询优惠券
        SeckillVoucher voucher = iSeckillVoucherService.getById(voucherId);

        //判断秒杀是否开始
        if (voucher.getBeginTime().isAfter(LocalDateTime.now())){
            //尚未开始
            return Result.fail("秒杀尚未开始");
        }

        //判断秒杀是否结束
        if (voucher.getBeginTime().isBefore(LocalDateTime.now())){
            //已经结束
            return Result.fail("秒杀已经结束");
        }

        //判断库存是否充足
        if (voucher.getStock()<1){
            return Result.fail("库存不足");
        }
        Long userId = UserHolder.getUser().getId();

        //创建锁对象
        //使用Redisson的分布式锁
        RLock lock = redissonClient.getLock("lock:order:" + userId);

        //获取锁
        boolean isLock = lock.tryLock();
        //判断锁是否获取成功
        if (!isLock){
            //不成功，返回错误信息
            return Result.fail("不允许重复下单");

        }

        try{
            //intern方法是保证每个userId字符串是同一个，保证同一个用户是同一把锁
            IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
            //获得代理对象来开启事务，因为Spring是通过动态代理来进行事务管理的，如果用this调用，用的是当前对象而不是代理对象
            return proxy.createVoucher(voucherId);
        }finally {
            lock.unlock();
        }




    }
```



##### 9 Redisson可重入锁、锁重试、watchDog实现方式

重入锁：记录重入次数

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\Redisson可重入锁.png)







![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\Redisson可重入、重试、watchDog实现方式.png)

##### 10 redis分布式锁总结

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\优惠券\分布式锁总结.png)

### 4 异步秒杀

思路：将减库存与一人一单操作提取出来

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\异步秒杀\异步秒杀思路.png)



代码实现

```
 @Transactional
    //在新增优惠券的同时将库存写入Redis
    public void addSeckillVoucher(Voucher voucher) {
        // 保存优惠券
        save(voucher);
        // 保存秒杀信息
        SeckillVoucher seckillVoucher = new SeckillVoucher();
        seckillVoucher.setVoucherId(voucher.getId());
        seckillVoucher.setStock(voucher.getStock());
        seckillVoucher.setBeginTime(voucher.getBeginTime());
        seckillVoucher.setEndTime(voucher.getEndTime());
        seckillVoucherService.save(seckillVoucher);

        //保存优惠券库存到Redis中
        stringRedisTemplate.opsForValue().set(RedisConstants.SECKILL_STOCK_KEY +voucher.getId(),voucher.getStock().toString());
    }
```



Lua脚本实现

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\异步秒杀\异步秒杀Lua脚本1.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\异步秒杀\异步秒杀Lua脚本2.png)



**了解思路即可**



### 5 Redis的消息队列

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\队列\redis的队列.png)



#### 1 List类型消息队列

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\队列\List类型消息队列.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\队列\List消息队列优缺点.png)

#### 2 PubSub队列

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\队列\PubSub队列.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\redis\队列\PubSub优缺点.png)

3 Stream队列