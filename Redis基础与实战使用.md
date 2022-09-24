# Redis

### 1 通用命令

keys-----符合模板要求的所有key（keys *  查询所有key）

del key------删除一个key

del key1 key2 ------删除多个key(返回值为删除key的数量)

exists key ------判断key是否存在

expire  key  seconds---------给一个key设置有效期

ttl key----------查看一个key的剩余有效时间（-1表示永久有效，-2表示已过期）