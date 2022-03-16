#### Redis

1.内存数据库（缓存数据库），在内存中进行数据操作，支持本地化也就是数据存到硬盘上

2.key-value类型存储，value支持字符串，list， set， sorted set（有序集合可做排行榜之类），  hash

3.与memcache相比， memcache只能在内存操作不持支本地化， 速度差不多memcache

redis速度快，支持事务，操作都为原子性，即对数据的更改要么全部执行，要么全部不执行

4.版本6.0之后支持多线程，

单线程速度快：

io多路复用，内存中存取，单线程不会有线之间切换，

#### python操作

[参考博客](https://www.cnblogs.com/liuqingzheng/articles/9833534.html)

~~~python
# 1安装 pip install redis
form redis import Redis
conn = Redis(host='', port='6379')
# 创建了一个链接，为避免重复创建练剑可以使用连接池，即从创建一个拥有若干个链接的连接池每次使用从里边取链接
conn.get('name') # 从redis中取数据

# 连接池的创建，放在一个包里边使项目加载时就将连接池加载到内存中取
import redis
pool = redis.ConnectionPool(host='', port='', max_connections=100)
# 使用
conn = redis.Redis(connection=poll)
conn.get('name')

##
# 这里报错的去settings 添加下这个试试：    CELERY_IMPORTS = ['comm.tasks']
~~~

