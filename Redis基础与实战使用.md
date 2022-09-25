# Redis

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