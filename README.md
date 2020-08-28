2 CentOS7这个版本的防火墙默认使用的是firewall，与之前的版本使用iptables不一样。按如下方便配置防火墙：
　　1、关闭防火墙：systemctl stop firewalld.service
　　2、关闭开机启动：systemctl disable firewalld.service
　　　　查看是否开机启动：systemctl is-enabled firewalld.service
启动samba
service smb restart
关闭防火墙
[root@localhost redis]# systemctl stop firewalld.service
没有将redis 端口规则添加到 iptables 里面
[root@localhost redis]#  iptables -I INPUT -p tcp --dport 6379 -j ACCEPT
REmote DIctionary Server(远程字典服务器) 非关系型数据库
NoSQL 这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。
Redis 作为一个独立的分布式的缓存服务器，为多个web服务器提供了一个共享的高性能缓存服务。
然后又出现了一致性hash来解决增加或减少缓存服务器导致重新hash带来的大量缓存失效的弊端。
一、Why？主从复制 读写分离
由于数据库的写入压力增加，Memcached只能缓解数据库的读取压力。
读写集中在一个数据库上让数据库不堪重负，大部分网站开始使用主从复制技术来达到读写分离，以提高读写性能和读库的可扩展性。
Mysql的 master-slave 模式成为这个时候的网站标配了。
 
分表分库 + 水平拆分 + mysql 集群
在Memcached的高速缓存，MySQL的主从复制，读写分离的基础之上，这时MySQL主库的写压力开始出现瓶颈。
而数据量的持续猛增，由于 MyISAM 使用表锁，在高并发下会出现严重的锁问题，大量的高并发MySQL应用开始使用InnoDB引擎代替MyISAM。
 
1、MyISAM：默认表类型，它是基于传统的ISAM类型，ISAM是有索引的顺序访问方法，它是存储记录和文件的标准方法。
不支持事务、不具备AICD特性、而且不支持外键。
读写相互阻塞(不仅会在写入的时候阻塞读取、还会在读取的时候阻塞写入、但是读取不会阻塞读取);
读取速度快、占用资源比较少。如果执行大量的select MyISAM比较适合。
MyISAM支持全文类型索引。
2、InnoDB：支持事务安全的引擎，支持外键、行锁、事务是他的最大特点。
而InnoDB不支持全文索引。
如果有大量的update和insert，建议用InnoDB，特别是针对多个并发和QPS较高的情况。
Innodb的行锁模式有以下几种：
共享锁，排他锁，意向共享锁(表锁)，意向排他锁(表锁)，间隙锁。
同时，开始流行使用分表分库来缓解写压力和数据增长的扩展问题。
MySQL推出了还不太稳定的表分区，虽然MySQL推出了 MySQL Cluster 集群，但性能也不能满足互联网的要求，只是在高可靠性上提供了非常大的保证。
Mysql 扩展瓶颈
MySQL数据库也经常存储一些大文本字段，导致数据库表非常的大，在做数据库恢复的时候就导致非常的慢，不容易快速恢复数据库。
比如1000万4KB大小的文本就接近40GB，如果能把这些数据从MySQL省去，MySQL将变得非常的小。关系数据库很强大，但是它并不能很好的应付所有的应用场景。
MySQL的扩展性差（需要复杂的技术来实现），大数据下IO压力大，表结构更改困难，正是当前使用MySQL的开发人员面临的问题。
二、NoSQL
当然，目前还是 SQL和NoSQL 一起使用。
NoSQL：不仅仅是SQL、没有声明性查询语言、没有预定义模式、非结构化和不可预知的数据
键：值对存储。 列存储，文档存储，图形数据库
最终一致性，而非ACID属性
CAP定理
高性能，高可用性，可伸缩性
容易扩展
NoSQL数据库种类繁多，但是一个共同的特点都是去掉关系数据库的关系型特性。
数据之间无关系，这样就非常容易扩展。无形之间，在架构层面上带来了可扩展的能力。
大数据量高性能
NoSQL数据库都具有非常高的读写性能，尤其在大数据量下，同样表现优秀。这得益于它的无关系性，数据库的结构简单。
一般MySQL使用 Query Cache，每次表的更新Cache就失效，是一种大粒度的Cache。
NoSQL的 Cache 是记录级的，是一种细粒度的Cache。
多样灵活的数据模型
NoSQL无需事先为要存储的数据建立字段，随时可以存储自定义的数据格式。
NoSQL 数据库分类
K/V 键值对：Redis 高性能的(key/value)分布式内存数据库，基于内存运行。
列存储数据库： HBase
文档数据库：MongDB。基于分布式文件存储、多文字信息描述类(文档数据库 BSON 格式比较多)，IO读写性能变差。
BSON：是一种类json的一种二进制形式的存储格式。
简称Binary JSON，它和JSON一样，支持内嵌的文档对象和数组对象。
图形数据库：Graph。
Mysql：冷数据(名称、价格，出厂日期，生产厂商等)
TFS：文件系统
ISearch：搜索引擎、关键字
Redis：热点高频信息
三、CAP定理 + BASE
C：Consistency（强一致性）
A：Availability（可用性）
P：Partition tolerance（分区容错性）
CAP的 3 进 2 —— 相当于(CA 2进1) P必占
CAP理论就是在分布式存储系统中，最多只能实现上面的两点。由于网络硬件肯定会出现延迟丢包等问题，分区容错性是我们必须需要实现的。
只能在一致性 C 和 可用性 A 之间进行权衡，没有NoSQL系统能同时保证这三点。
CAP理论的核心：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，最多只能同时较好的满足两个。因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三 大类：
CA - 传统数据库。单点集群，满足一致性，可用性的系统，在可扩展性上不太强。
CP - Redis、Mongodb。 满足一致性，分区容忍必的系统，性能不是特别高。
AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。(大多数网站架构的选择)
 
BASE
基本可用 Basically Available
软状态 Soft State
最终一致 Eventually Consistent
分布式系统（distributed system）
由多台计算机和通信的软件组件通过计算机网络连接（本地网络或广域网）组成。
分布式：不同的多台服务器上面部署不同的服务模块（工程），他们之间通过Rpc/Rmi之间通信和调用，对外提供服务和组内协作。
集群：不同的多台服务器上面部署相同的服务模块，通过分布式调度软件进行统一的调度，对外提供服务和访问。
四、Redis
Redis支持数据的持久化，可以将内存中的数据(支持异步)保持在磁盘中，重启的时候可以再次加载进行使用。
Redis不仅仅支持简单的 key-value 类型的数据，同时还提供 string，list，set，zset，hash 等数据结构的存储。
Redis支持数据的备份，即 master-slave 模式的数据备份。
getconf LONG_BIT 查看 (Linux是 32位还是64位)
安装 Redis
1、下载获得 redis-x.x.x.tar.gz 后将它放入我们的Linux目录 /opt
2、在 /opt 目录下，解压命令： tar -zxvf redis-x.x.x.tar.gz
3、解压完成后出现文件夹：redis-x.x.x
4、进入目录：cd redis-x.x.x
5、在redis-x.x.x 目录下执行 make 命令
make命令出错：yum install gcc-c++ (安装gcc)
然后运行 make distclean 之后再 make。
(错误：jemalloc/jemalloc.h：没有那个文件或目录)
6、make 完成后继续执行 make install

五、安装成功后 Hello Redis
1、查看默认安装目录：cd /usr/local/bin —> ls -l
 
redis-benchmark： 性能测试工具，可以在自己本子运行，看看自己本子性能如何。 (服务启动起来后执行)
redis-check-aof： 修复有问题的AOF文件。
redis-check-dump： 修复有问题的dump.rdb文件。
redis-cli： 客户端，操作入口。
redis-sentinel： redis集群使用，监控。
redis-server： Redis服务器启动命令。
2、cd / ->在根目录创建一个文件夹 myredis(任意)。
3、cd /opt/redis-x.x.x —> 复制 Redis 配置文件。cp redis.conf /myredis
4、修改 /myredis/redis.conf 下的配置文件。vim 编辑，将 daemonize no 改成 yes。
查一下后台 Redis —> ps -ef|grep redis 有没有启动服务。 lsof -i：6379
5、cd /usr/local/bin —>启动服务。redis-server /myredis/redis.conf 运行copy的conf文件。
6、redis-cli -p 6379 (Hello Redis完成) ping pong
查一下后台 Redis —> ps -ef|grep redis 有没有启动服务。 lsof -i：6379
set k1 a； get k1 —> “a”
7、单实例关闭： redis-cli shutdown。exit(退出)
多实例关闭，指定端口关闭：redis-cli -p 6379 shutdown
六、Redis 基础知识
Redis 是单进程
默认16个数据库，类似数组下表从 0 开始，初始默认使用 0 号库。
select ：命令切换数据库。SELECT 7 ——> 选择 6号库。
dbsize ：查看当前数据库的key的数量。 keys *：查看所有的 key。(支持占位符 key k?)
flushdb：清空当前库。
flushall：通杀全部库。
统一密码管理，16个库都是同样密码，要么都OK要么一个也连接不上。
Redis 索引都是从 0 开始。
默认端口是6379 (9宫格键盘 merz)。
七、Redis的五大数据类型
Redis 键(key)
set key
get key
常用：
keys * 查看所有的 key。
exists key： 判断某个key是否存在。
move key db： 把当前 key 移动到指定 DB (当前库就没有了)。
expire key 秒钟： 为给定的key设置过期时间。
ttl key 查看还有多少秒过期，-1表示永不过期，-2表示已过期。(过期移除内存系统)
type key： 查看你的key是什么类型。
1、String
string（字符串）
string 是 redis 最基本的类型，可以理解成与Memcached一样的类型，key：value。
string 类型是二进制安全的。意思是redis的string可以包含任何数据。比如序列化的对象…
string 一个redis中字符串 value 最多可以是512M。
单值 单value
String 常用命令
set/get/del/append/strlen 设置、获取、删除、追加、获取长度。
incr/decr/incrby/decrby： 一定要是数字才能进行加减。
getrange： 获取指定区间范围内的值，截取字符串。从0到-1表示全部。
setrange： 设置指定区间范围内的值，插入字符串。格式是setrange key值 具体值。对指定位置 覆盖操作。
setex(set with expire)键秒值： 对该key设置存活时间。setex k1 10 v1
setnx(set if not exist)： 如果key值存在返回0，如果不存在进行set设置值。setnx k1 v1
mset： 合并set。mset k1 v1 k2 v2 k3 v3
mget： 合并get。gset k1 k2 k3
msetnx： mset k1 v1 k2 v2 如果 k1 已经存在了。全部set失败！
getset(先get再set)： 获取输出当前value值，然后set。
2、list（列表）
Redis 列表是简单的字符串列表，按照插入顺序排序。
你可以添加一个元素列表的头部（左边）或者尾部（右边）。
底层实际是个链表
单值多value
list 常用命令：
lpush/rpush/lpop/rpop/lrange
 
lindex： 按照索引下标获得元素(从上到下)。LINDEX KEY_NAME 1
llen： 获取长度。 LLEN KEY_NAME
lrem： 删2个1。LREM KEY_NAME 2 1
ltrim： 开始index 结束index，截取指定范围的值后再赋值给key。LTRIM KEY_NAME 3 5
👆ltrim： 
rpoplpush：源列表目的列表rpoplpush KEY_NAME01 KEY_NAME02
lset：对下标的具体某个值设置值。lset KEY_NAME 1 x把下标为1的值变为x。
linsert：在list某个已有值的前后再添加具体值。LINSERT KEY_NAME before 1 Y在值1的前面添加Y。
性能总结
list 是一个字符串链表，left、right 都可以插入添加。
如果键不存在，创建新的链表。
如果键已存在，新增内容。
如果值全移除，对应的键也就消失了。
链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很低。
3、set（集合）
Redis 的Set是string类型的无序集合,且不允许重复的成员。它是通过HashTable实现实现的。
单值多value
set 常用命令
sadd：将一个或多个成员元素加入到集合中，已经存在于集合的成员元素将被忽略。sadd KEY_NAME v1
del：删除集合。del KEY_NAME
smembers：命令返回集合中的所有的成员。不存在的集合 set 被视为空集合。smembers KEY_NAME
srandmember:srandmember KEY_NAME value从集合中随机出几个数。
sismember：判断成员元素是否是集合的成员。sismember KEY_NAME1
scard：返回集合中元素的数量。scard KEY_NAME
srem：删除集合中元素。srem KEY_NAME value
spop：随机集合中一个value出栈。spop KEY_NAME
smove：将指定成员 member 元素从 source 集合移动到目标集合。smove set01 set02 value
数学集合类
差集：sdiff 返回在KEY_NAME01里面，不在set02里面的 value。
sdiff KEY_NAME01 KEY_NAME02
交集：sinter 返回在KEY_NAME01里面，也在set02里面的 value。
sinter KEY_NAME01 KEY_NAME02
并集：sunion 返回去重后的集合。
4、hash（哈希）
Redis hash 是一个键值对集合。
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象 类似Java里面的 Map< String,Object>
K/V模式不变，但V是一个键值对。
hash 常用命令：
hset/hget：hset KEY_NAME name zs hget KEY_NAME name
hmset/hmget：hmset KEY_NAME id 11 name zs hmget KEY_NAME id name
hgetall/hdel：hgetall KEY_NAME hdel KEY_NAME name
hexists:用于查看哈希表的指定字段是否存在hexists KEY_NAME id
hkeys/hvals：用于获取哈希表中的所有域（key）,返回哈希表所有域(value)的值。hkeys KEY_NAME hvals KEY_NAME
hincrby/hincrbyfloat： 用于为哈希表中的字段值加上指定增量值。hincrby user id
hlen：获取哈希表中字段的数量。hlen KEY_NAME
hsetnx：用于为哈希表中不存在的的字段赋值。hsetnx KEY_NAME key value v1
5、zset(sorted set：有序集合)
Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
不同的是每个元素都会关联一个整数或者双精度浮点数的分数。(比如游戏分数)
redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。
在set基础上，加一个score值。之前set是k1 v1 v2 v3，
现在 zset是k1 score1 v1 score2 v2
zset 常用命令：命令 KEY_NAME value [WITHSCORES] [LIMIT offset count]
zadd：用于将一个或多个成员元素及其分数值加入到有序集中。
zadd KEY_NAME 60 v1 70 v2
zrange：ZRANGE KEY_NAME 0 -1|ZRANGE KEY_NAME 0 -1 withscores
zrangebyscore：返回有序集合中指定分数区间的成员列表。 
zrangebyscore KEY_NAME 开始score 结束score withscores
zrangebyscore zset 10 (50 不包含50。
zrangebyscore zset 10 (50 limit 2 2 limit 返回限制,limit 开始下标走多少步。
zrem： 用于移除有序集中的一个或多个成员，不存在的成员将被忽略。zrem KEY_NAME value
zcard：用于计算集合中元素的数量。ZCARD KEY_NAME
zcount：用于计算有序集合中指定分数区间的成员数量。ZCOUNT KEY_NAME min max
zrank：作用是获得下标值。zrank KEY_NAME value
zscore：对应值,获得分数。zscore KEY_NAME value
zrevrank：作用是逆序获得下标值。 zrevrank KEY_NAME value
zrevrange：返回有序集中，指定区间内的成员。其中成员的位置按分数值递减(从大到小)来排列。ZREVRANGE key 0 -1 [WITHSCORES]
zrevrangebyscore：返回有序集中指定分数区间内的所有的成员。有序集成员按分数值递减(从大到小)的次序排列。
八、redis.conf 配置文件
units单位
配置大小单位,开头定义了一些基本的度量单位，只支持bytes，不支持bit。对大小写不敏感
1k => 1000 bytes
1kb => 1024 bytes
1m => 1000000 bytes
1mb => 1024 * 1024 bytes
1g => 1000000000 bytes
1gb => 1024 * 1024 * 1024 bytes
1、INCLUDES 包含
通过includes包含，redis.conf可以作为总闸，包含其他 other.conf。
2、GENERAL 通用
daemonize yes：后台运行
pidfile：当Redis以守护进程方式运行时，Redis默认会把pid写入 /var/run/redis.pid文件。
port：6379 端口号。
tcp-backlog：设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列。
timeout：超时断开连接。(0 to disable)
bind：端口的绑定。
tcp-keepalive：单位为秒，设置为60s (0即为不检测)
loglevel：默认(verbose)。日志级别,debug、verbose、notice、warning。
logfile：指定日志文件名，为空则标准输出。
syslog-enabled：是否把系统日志输出到syslog中。默认关闭，开启后。
syslog-ident：日志鉴别，日志以 redis 开头。
syslog-facility：指定syslog设备，值可以是USER或 LOCAL0-LOCAL7。默认为 LOCAL 0
databases 16：默认为 16 个库。
3、SECURITY 安全
访问密码的查看、设置和取消
127.0.0.1：6379 > config get requirepass (获取密码)
127.0.0.1：6379 > config set requirepass “123456” (设置密码) ping 不通了。
127.0.0.1：6379 > auth 123456 （输入密码）
4、LIMITS 限制
maxclients 10000：redis同时可以与多少个客户端进行连接，默认情况为10000个客户端。
maxmemory： 设置redis可以使用的内存量。一旦到达内存使用上限，redis将会试图移除内部数据，移除规则可以通过maxmemory-policy来指定。
maxmemory-policy：
（1）volatile-lru：从已设置过期时间的数据集中挑选最近最少使用的数据淘汰。
（2）volatile-ttl：从已设置过期时间的数据集中挑选将要过期的数据淘汰。
（3）volatile-random：从已设置过期时间的数据集中任意选择数据淘汰。
（4）allkeys-lru：从数据集中挑选最近最少使用的数据淘汰。
（5）allkeys-random：从数据集中随机选择数据淘汰。
（6）no-enviction（驱逐）：永不过期，不进行移除。针对写操作，只是返回错误信息。禁止驱逐数据。
（7）volatile-lfu： 使用具有过期集的键之间的近似LFU(数据过去被访问多次，那么将来被访问的频率也更高)将访问频率最少的键值对淘汰。
（8）allkeys-lfu：从数据集中近似LFU(将访问频率最少的键值对淘汰。)选择数据淘汰。
maxmemory-samples： 设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小。
redis默认会检查这么多个key并选择其中LRU的那个。
5、SNAPSHOTTING 快照
Save the DB on disk； (默认) dump.rdb 文件
Save：save <seconds> <changes>
Redis 默认配置文件中提供了三个条件 触发 RDB 保存：
save 900 1 ：——> 900s（15min）至少有 1 次 key 的改动。
save 300 10 ：——> 300s（5min）至少有 10 次 key 的改动。
save 60 10000 ：——> 60s（1min）至少有10000次key的改动。
直接输入 save 或者是 bgsave 命令，即刻备份！
Save：save时只管保存，其它不管，全部阻塞。
BGSAVE：会在后台异步进行快照操作,快照同时还可以响应客户端请求。
可以通过 lastsave 命令获取最后一次成功执行快照的时间。
禁用 RDB：save “” (不设置任何save指令，或者给save传入一个空字符串参数)。
stop-writes-on-bgsave-error：yes
如果配置成no，表示你不在乎数据不一致或者有其他的手段发现和控制。
rdbcompression：
对于存储到磁盘中的快照，可以设置是否进行压缩存储。
如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。
rdbchecksum
在存储快照后，还可以让redis使用CRC64算法来进行数据校验。
但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。
dbfilename dump.rdb
指定本地数据库文件名，默认值为dump.rdb。
dir：config get dir (获取目录)
6、APPEND ONLY MODE 追加
By default Redis asynchronously dumps the dataset on disk； (默认) appendonly.aof 文件
appendonly no： 启动改为 yes
appendfilename： 默认名字 appendonly.aof。
appendfsync everysec：
always：同步持久化 每次发生数据变更会被立即记录到磁盘 性能较差但数据完整性比较好。
everysec： 出厂默认推荐，异步操作，每秒记录 如果一秒内宕机，有数据丢失。
no-appendfsync-on-rewrite no： 重写时是否可以运用 Appendfsync，用默认no即可，保证数据安全性。
auto-aof-rewrite-percentage 100： （默认）设置重写的基准值
auto-aof-rewrite-min-size 64mb： （默认）设置重写的基准值
九、持久化 RDB 和 AOF
RDB（Redis DataBase）
👆 参考 Redis.conf SNAPSHOTTING 快照
RDB 保存的是 (默认) dump.rdb 文件：配置位置在 redis.conf 👆 参考 SNAPSHOTTING 快照
在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是Snapshot快照，它恢复时是将快照文件直接读到内存里。
 
Redis 会单独创建（fork）一个子进程进行持久化，会先将数据写入到一个临时文件中，待持久化过程结束了再用这个临时文件替换上次持久化好的文件
fork 的作用是复制一个与当前进程一样的进程。
新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程
恢复：
将备份文件 (dump.rdb) 移动到 redis 安装目录并启动服务即可。
优点：
整个过程中，主进程是不进行任何IO操作的，确保了极高的性能 适合进行大规模数据的恢复。
如果对于数据恢复的 完整性 不是非常敏感，那RDB方式要比AOF方式更加的高效。
缺点：
RDB的缺点是最后一次持久化后的数据可能丢失。
在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改。
fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑。
修复： 先备份
如果 RDB 文件有错误 在安装目录下，用 redis-check-rdb 对 RDB 文件进行修复，把错误的命令全部删除。
redis-check-rdb --fix dump.rdb
停止：
动态停止所有RDB保存规则的方法：redis-cli config set save “”
AOF（Append Only File）
👆 参考 Redis.conf
APPEND ONLY MODE 追加
AOF 保存的是 appendonly.aof 文件：配置位置在 redis.conf 👆 参考 APPEND ONLY MODE 追加
以日志的形式来记录每个写操作：
将 Redis 执行过的所有写指令默认异步记录下来(读操作不记录)，只许追加文件但不可以改写文件。
redis启动之初会读取该文件重新构建数据，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。
 
AOF and RDB persistence can be enabled at the same time without problems.
dump.rdb 文件 和 appendonly.aof 文件 可以共存。优先找 AOF 文件
恢复：
将有数据的aof文件复制一份保存到对应目录(config get dir)，重启 redis 然后重新加载。
rewrite：
因为重写机制，所以要经常 ——> free 看内存，df -h 看磁盘空间。
AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制。
当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集。可以使用命令bgrewriteaof
原理
AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再 rewrite)，遍历新进程的内存中数据，每条记录有一条的Set语句。重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似。
触发机制
Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发。
优点：
每次修改修改同步：appendfsync always 同步持久化 每次发生数据变更会被立即记录到磁盘 性能较差但数据完整性比较好。
不同步：appendfsync no 从不同步。
每秒同步：appendfsync everysec 默认异步操作，每秒记录，如果一秒内宕机有数据丢失。
缺点：
相同数据集的数据而言 aof 文件要远大于 rdb 文件，恢复速度慢于rdb。
aof 运行效率要慢于 rdb,每秒同步策略效率较好，不同步效率和rdb相同。
如果 AOF 文件有错误 在安装目录下，用 redis-check-aof 对 AOF 文件进行修复，把错误的命令全部删除。
redis-check-aof --fix appendonly.aof
选择 (Which one) RDB/AOF
同时开启两种持久化方式
在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据。
AOF 文件保存的数据集要比 RDB 文件保存的数据集要 完整 RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。
那要不要只使用AOF呢？
因为RDB更适合用于备份数据库(AOF在不断变化不好备份)，快速重启，而且不会有AOF可能潜在的bug，留着作为一个万一的手段。
因为 RDB 文件只用作后备用途，建议只在Slave上持久化RDB文件，只要15分钟备份一次就够了，只保留 save 900 1 这条规则。
如果开启 Enalbe AOF
好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了，代价一是带来了持续的IO。二是AOF rewrite 的最后将 rewrite 过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。只要硬盘许可，应该尽量减少AOF rewrite的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上。默认超过原大小100%大小时重写可以改到适当的数值。
如果不开启 Enable AOF
仅靠Master-Slave Replication 实现高可用性也可以。能省掉一大笔IO也减少了rewrite时带来的系统波动。代价是如果 Master/Slave 同时down掉，会丢失十几分钟的数据，启动脚本也要比较两个Master/Slave中的RDB文件，载入较新的那个。
十、Redis 的事务
一个队列中可以一次性,顺序性执行多个命令本质是一组命令的集合。
一个事务中的所有命令都会序列化，按顺序地串行化执行而不会被其它命令插入，不许加塞。
没有隔离级别的概念。不保证原子性，没有回滚。
命令
MULTI： 标记一个事务块的开始。OK
EXEC： 执行所有事务块内的命令。
DISCARD： 取消事务，放弃执行事务块内的所有命令。
UNWATCH： 取消 WATCH 命令对所有 key 的监视。一旦执行了exec，之前加的监控锁都会被取消掉了。
WATCH： 监视一个或多个 key；如果在事务执行之前这个 key 被其他命令所改动，事务将被打断。
Case1 正常执行： MULTI 一系列操作 EXEC
Case2 放弃事务： MULTI 一系列操作 DISCARD
Case3 全体连坐： MULTI 一系列操作中有错误操作（直接报错的命令） EXEC（全部失败）
Case4 冤头债主： MULTI 一系列操作中有错误操作（不直接报错的命令，比如 incr 字符串） EXEC （错误命令不执行）
Case4 watch监控： 先监控 在开启事务。watch key 、MULTI 一系列操作 EXEC 如果其他线程没有干扰则成功，反之失败。
悲观锁/乐观锁/CAS(Check And Set)
悲观锁： 行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。
乐观锁： 读写不会上锁。但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。
CAS：
Watch指令，类似乐观锁，事务提交时，如果Key的值已被别的客户端改变，整个事务队列都不会被执行。
通过WATCH命令在事务执行之前监控了多个Keys，倘若在WATCH之后有任何Key的值发生了变化，EXEC命令执行的事务都将被放弃，同时返回Nullmulti-bulk应答以通知调用者事务执行失败。
十一、Redis 的复制(Master/Slave)
主从复制 读写分离 容灾恢复。配从不配主
主机数据更新后根据配置和策略，自动同步到备机的 master/slaver 机制，Master以写为主，Slave以读为主。
info replication 查看 role 角色。
常用三种配置方法
1、一主二仆
info replication： 返回主从复制的信息。主机才可以写，从机只能读。
 
从库配置：slaveof 主库IP 主库端口 slaveof 127.0.0.1 6379
如果 master shutdown 后，从库 role 还是 slave。 master 恢复上线，关系也恢复。
每次 slave shutdown 与 master 断开之后，都需要重新连接，除非你配置进redis.conf文件。
从库每次 slave shutdown 后 role 变为 master。
info replication 查看 role 角色。
 
2、薪火相传
上一个 Slave 可以是下一个 Slave 的 Master，Slave 同样可以接收其他 slaves 的连接和同步请求。减轻主 Master 压力。
中途变更转向：会清除之前的数据，重新建立拷贝最新的。
slaveof 新主库IP 新主库端口 slaveof 127.0.0.1 6379
 
3、反客为主
当 主Master shutdown 以后， 使当前数据库停止与其他数据库的同步。
SLAVEOF no one 命令 使 Slave 转成 Master 数据库。
 
________________________________________
哨兵模式(反客为主自动版本)
能够后台自动监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。
原有体系 主仆关系
1、新建一个文件名为 sentinel.conf 空 配置文件。
2、配置 sentinel.conf文件。写入 sentinel monitor 被监控数据库名字 127.0.0.1 6379 1
上面最后一个数字1，表示主机挂掉后 salve 投票看让谁接替成为主机，得票数多成为主机。
3、启用 redis-sentinel /myredis/sentinel.conf 监控。
4、如果 主Master shutdown 以后，salve 投票多的选为 新Master，而剩余 salve 转向 新Master。
5、即便 主Master恢复，原来的体系已经建立，由于监控则变成新Master 的 salve
6、一组sentinel能同时监控多个Master。
 
Redis复制缺点
由于所有的写操作都是先在Master上操作，然后同步更新Slave上，
所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。
十一、Redis的Java客户端 Jedis
测试jar 包
commons-pool-1.6.jar
jedis-2.1.0.jar
import java.util.Set;
import redis.clients.jedis.Jedis;
public class RedisDemo {
  public static void main(String[] args) {
    Jedis jedis = new Jedis("192.168.98.129",6379);
    System.out.println(jedis.ping());
    jedis.set("k1", "v1");
    jedis.set("k2", "v2");
    jedis.set("k3", "v3");
    jedis.set("k4", "v4");
    String string = jedis.get("k1");
    System.out.println(string);
    Set<String> keys = jedis.keys("*");
    for (String string2 : keys) {
      System.out.println(string2);}
    Long dbSize = jedis.dbSize();
    System.out.println(dbSize);}}
事务
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;
public class RedisDemoTx {
  public static void main(String[] args) {
    Jedis jedis = new Jedis("192.168.98.129",6379);
    System.out.println(jedis.ping());
    Transaction transaction = jedis.multi();
    transaction.set("k1", "v1");
    transaction.set("k2", "v2");
    transaction.set("k3", "v3");
    transaction.set("k4", "v4");
    //transaction.exec();
    transaction.discard();}}
watch … 监控
主从复制 主写从读
jedis_S.slaveof("127.0.0.1",6379);
jedis_M.set("K1","V1");
//稍慢执行
jedis_S.("K1");
Redis Pool
单例模式
数据库连接池的设计一般采用单例模式
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;
public class JedisPoolUtil {
  private static volatile JedisPool jedisPool = null;
  private JedisPoolUtil() {}
  public static JedisPool getJedisPoolInstance() {
    if (null == jedisPool) {
      synchronized (JedisPoolUtil.class) {
        if (null == jedisPool) {
          JedisPoolConfig poolConfig = new JedisPoolConfig();
          poolConfig.setMaxActive(1000);
          poolConfig.setMaxIdle(32);
          poolConfig.setMaxWait(100 * 1000);
          poolConfig.setTestOnBorrow(true);
         jedisPool = new JedisPool(poolConfig, "127.0.0.1", 6379);}}}
         return jedisPool;}
  public static void release(JedisPool jedisPool, Jedis jedis) {
    if (null != jedis) {
      jedisPool.returnResourceObject(jedis);}}}
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
public class TestPool {
  public static void main(String[] args) {
    JedisPool jedisPool = JedisPoolUtil.getJedisPoolInstance();
    JedisPool jedisPool2 = JedisPoolUtil.getJedisPoolInstance();
    System.out.println(jedisPool == jedisPool2);
    Jedis jedis = null;
    try {
      jedis = jedisPool.getResource();
      jedis.set("aa","bb");
    } catch (Exception e) {
      e.printStackTrace();
    }finally{
      JedisPoolUtil.release(jedisPool, jedis);}}}
Redis的发布订阅
进程间的一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。
redis.conf 配置项说明如下：
1、 Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
  daemonize no
2、 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
  pidfile /var/run/redis.pid
4、 绑定的主机地址
  bind 127.0.0.1
5、当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
  timeout 300
7、日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
  logfile stdout
10、指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
  rdbcompression yes
12、指定本地数据库存放目录     dir ./
13、设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步    slaveof 
14、当master服务设置了密码保护时，slav服务连接master的密码    masterauth 
15、设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH命令提供密码，默认关闭  requirepass foobared
16、设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息     maxclients 128
17、指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区     maxmemory 
18、指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
  appendonly no
20、指定更新日志条件，共有3个可选值： 
  no：表示等操作系统进行数据缓存同步到磁盘（快） 
  always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
  everysec：表示每秒同步一次（折衷，默认值）
  appendfsync everysec
21、指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
   vm-enabled no
22、虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享      vm-swap-file /tmp/redis.swap
23、将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0     vm-max-memory 0
24、Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值   vm-page-size 32
25、设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。      vm-pages 134217728
26、设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4      vm-max-threads 4
27、设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启      glueoutputbuf yes
28、指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法    hash-max-zipmap-entries 64
  hash-max-zipmap-value 512
29、指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）    activerehashing yes
30、指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
  include /path/to/local.conf
Msql8.0.16：Eric_029
③ 查看mysql 数据库的 user 表中当前 root 用户的相关信息
执行命令为：select host,user,authentication_string,plugin 
from user;
执行完命令后显示一个表格， root 用户的 host默认显示的 
localhost，说明只支持本地访问，不允许远程访问。
④ 更改 host 的默认配置
执行命令为：update user set host='%' where user='root';
⑤ 刷新
执行命令为：flush privileges;
https://www.52pojie.cn/forum.php?mod=viewthread&tid=729216
1、use mysql;
2、alter user 'root'@'localhost' identified with mysql_native_password by '********';
alter user 'root'@'%' identified with mysql_native_password by '********';
3、flush privileges;
Redis 的数据结构和相关常用命令
数据持久化
内存管理与数据淘汰机制
Pipelining
事务与 ing
Redis 性能调优
主从复制与集群分片
Redis Java 客户端的选择
Redis 是一个开源的，基于内存的结构化数据存储媒介，可以作为数据库、缓存服务或消息服务使用。
Redis 支持多种数据结构，包括字符串、哈希表、链表、集合、有序集合、位图、Hyperloglogs 等。
Redis 具备 LRU 淘汰、事务实现、以及不同级别的硬盘持久化等能力，并且支持副本集和通过 Redis Sentinel 实现的高可用方案，同时还支持通过 Redis Cluster 实现的数据自动分片能力。
Redis 的主要功能都基于单线程模型实现，也就是说 Redis 使用一个线程来服务所有的客户端请求，同时 Redis 采用了非阻塞式 IO，并精细地优化各种命令的算法时间复杂度。
这些信息意味着：
Redis 是线程安全的（因为只有一个线程），其所有操作都是原子的，不会因并发产生数据异常。
Redis 的速度非常快（因为使用非阻塞式 IO，且大部分命令的算法时间复杂度都是 O(1))。
使用高耗时的 Redis 命令是很危险的，会占用唯一的一个线程的大量处理时间，导致所有的请求都被拖慢（例如时间复杂度为 O(N)的 Keys 命令，严格禁止在生产环境中使用）。
Redis 的数据结构和相关常用命令
本节中将介绍 Redis 支持的主要数据结构，以及相关的常用 Redis 命令。本节只对 Redis 命令进行扼要的介绍，且只列出了较常用的命令。
如果想要了解完整的 Redis 命令集，或了解某个命令的详细使用方法，请参考官方文档：
https: //redis.io/commands
Key
Redis 采用 Key-Value 型的基本数据结构，任何二进制序列都可以作为 Redis 的 Key 使用（例如普通的字符串或一张 JPEG 图片）。
关于 Key 的一些注意事项：
不要使用过长的 Key。例如使用一个 1024 字节的 Key 就不是一个好主意，不仅会消耗更多的内存，还会导致查找的效率降低。
Key 短到缺失了可读性也是不好的，例如”u1000flw”比起”user:1000:followers”来说，节省了寥寥的存储空间，却引发了可读性和可维护性上的麻烦。
最好使用统一的规范来设计 Key，比如”object-type:id:attr”，
以这一规范设计出的 Key 可能是”user:1000″
或”comment:1234:reply-to”。
Redis 允许的最大 Key 长度是 512MB（对 Value 的长度限制也是 
512MB）。
String
String 是 Redis 的基础数据类型，Redis 没有 Int、Float、Boolean 等数据类型的概念，所有的基本类型在 Redis 中都以 String 体现。
与 String 相关的常用命令：
SET：为一个 Key 设置 Value，可以配合 EX/PX 参数指定 Key 的有效期，通过 NX/XX 参数针对 Key 是否存在的情况进行区别操作，时间复杂度 O(1)。
GET：获取某个 Key 对应的 Value，时间复杂度 O(1)。
GETSET：为一个 Key 设置 Value，并返回该 Key 的原 Value，时间复杂度 O(1)。
MSET：为多个 Key 设置 Value，时间复杂度 O(N)。
MSETNX：同 MSET，如果指定的 Key 中有任意一个已存在，则不进行任何操作，时间复杂度O(N)。
MGET：获取多个 Key 对应的 Value，时间复杂度O(N)。
上文提到过，Redis 的基本数据类型只有 String，但 Redis 可以把 String 作为整型或浮点型数字来使用，主要体现在 INCR、DECR 类的命令上：
INCR：将 Key 对应的 Value 值自增 1，并返回自增后的值。只对可以转换为整型的 String 数据起作用。时间复杂度 O(1)。
INCRBY：将 Key 对应的 Value 值自增指定的整型数值，并返回自增后的值。只对可以转换为整型的 String 数据起作用。时间复杂度 O(1)。
DECR/DECRBY：同 INCR/INCRBY，自增改为自减。
INCR/DECR 系列命令要求操作的 Value 类型为 String，并可以转换为 64 位带符号的整型数字，否则会返回错误。
也就是说，进行 INCR/DECR 系列命令的 Value，必须在 [-2^63 ~ 2^63 – 1] 范围内。
前文提到过，Redis 采用单线程模型，天然是线程安全的，这使得 INCR/DECR 命令可以非常便利的实现高并发场景下的精确控制。
例 1：库存控制
在高并发场景下实现库存余量的精准校验，确保不出现超卖的情况。设置库存总量：
SETinv:remain "100"
库存扣减+余量校验：
DECRinv:remain
当 DECR 命令返回值大于等于 0 时，说明库存余量校验通过，如果返回小于 0 的值，则说明库存已耗尽。
假设同时有 300 个并发请求进行库存扣减，Redis 能够确保这 300 个请求分别得到 99 到 -200 的返回值，每个请求得到的返回值都是唯一的，绝对不会出现两个请求得到一样的返回值的情况。
例 2：自增序列生成
实现类似于 RDBMS 的 Sequence 功能，生成一系列唯一的序列号。设置序列起始值：
SETsequence"10000"
获取一个序列值：
INCRsequence
直接将返回值作为序列使用即可。获取一批（如100个）序列值：
INCRBYsequence 100
假设返回值为 N，那么 [N – 99 ~ N] 的数值都是可用的序列值。
当多个客户端同时向 Redis 申请自增序列时，Redis 能够确保每个客户端得到的序列值或序列范围都是全局唯一的，绝对不会出现不同客户端得到了重复的序列值的情况。
List
Redis 的 List 是链表型的数据结构，可以使用 LPUSH/RPUSH/LPOP/RPOP 等命令在 List 的两端执行插入元素和弹出元素的操作。
虽然 List 也支持在特定 Index 上插入和读取元素的功能，但其时间复杂度较高（O(N)），应小心使用。
与 List 相关的常用命令：
LPUSH：向指定 List 的左侧（即头部）插入 1 个或多个元素，返回插入后的 List 长度。时间复杂度O(N)，N 为插入元素的数量。
RPUSH：同 LPUSH,向指定List 的右侧（即尾部）插入1或多个元素。
LPOP：从指定 List 的左侧（即头部）移除一个元素并返回，时间复杂度 O(1)。
RPOP：同LPOP,从指定 List 的右侧（即尾部）移除1个元素并返回。
LPUSHX/RPUSHX：与 LPUSH/RPUSH 类似，区别在于 LPUSHX/RPUSHX 操作的 Key 如果不存在，则不会进行任何操作。
LLEN：返回指定 List 的长度，时间复杂度 O(1)。
LRANGE：返回指定 List 中指定范围的元素（双端包含，即 LRANGE key 0 10 会返回 11 个元素），时间复杂度 O(N)。 应尽可能控制一次获取的元素数量，一次获取过大范围的 List 元素会导致延迟，同时对长度不可预知的 List，避免使用 LRANGE key 0 -1 这样的完整遍历操作。
应谨慎使用的 List 相关命令：
LINDEX：返回指定 List 指定 Index 上的元素，如果 Index 越界，返回 nil。Index 数值是回环的，即 -1 代表 List 最后一个位置，-2 代表 List 倒数第二个位置。时间复杂度 O(N)。
LSET：将指定 List 指定 Index 上的元素设置为 Value，如果 Index 越界则返回错误，时间复杂度 O(N)，如果操作的是头/尾部的元素，则时间复杂度为 O(1)。
LINSERT：向指定 List 中指定元素之前/之后插入一个新元素，并返回操作后的 List 长度。 如果指定的元素不存在，返回 -1。如果指定 Key 不存在，不会进行任何操作，时间复杂度 O(N)。
由于 Redis 的 List 是链表结构的，上述的三个命令的算法效率较低，需要对 List 进行遍历，命令的耗时无法预估，在 List 长度大的情况下耗时会明显增加，应谨慎使用。
换句话说，Redis 的 List 实际是设计来用于实现队列，而不是用于实现类似 ArrayList 这样的列表的。
如果你不是想要实现一个双端出入的队列，那么请尽量不要使用 Redis 的 List 数据结构。
为了更好支持队列的特性，Redis 还提供了一系列阻塞式的操作命令，如 BLPOP/BRPOP 等，能够实现类似于 BlockingQueue 的能力，即在 List 为空时，阻塞该连接，直到 List 中有对象可以出队时再返回。
针对阻塞类的命令，此处不做详细探讨，请参考官方文档中”Blocking operations on lists”一节：
https: //redis.io/topics/data-types-intro
Hash
Hash 即哈希表，Redis 的 Hash 和传统的哈希表一样，是一种 field-value 型的数据结构，可以理解成将 HashMap 搬入 Redis。
Hash 非常适合用于表现对象类型的数据，用 Hash 中的 field 对应对象的 field 即可。
Hash 的优点包括：
可以实现二元查找，如”查找 ID 为 1000 的用户的年龄”。
比起将整个对象序列化后作为 String 存储的方法，Hash 能够有效地减少网络传输的消耗。
当使用 Hash 维护一个集合时，提供了比 List 效率高得多的随机访问命令。
与 Hash 相关的常用命令：
HSET：将 Key 对应的 Hash 中的 field 设置为 Value。如果该 Hash 不存在，会自动创建一个。时间复杂度 O(1)。
HGET：返回指定 Hash 中 field 字段的值，时间复杂度 O(1)。
HMSET/HMGET：同 HSET 和 HGET，可以批量操作同一个 Key 下的多个 field，时间复杂度：O(N)，N 为一次操作的 field 数量。
HSETNX：同 HSET，但如 field 已经存在，HSETNX 不会进行任何操作，时间复杂度 O(1)。
HEXISTS：判断指定 Hash 中 field 是否存在，存在返回 1，不存在返回 0，时间复杂度 O(1)。
HDEL：删除指定 Hash 中的 field（1 个或多个），时间复杂度：O(N)，N 为操作的 field 数量。
HINCRBY：同 INCRBY 命令，对指定 Hash 中的一个 field 进行 INCRBY，时间复杂度 O(1)。
应谨慎使用的 Hash 相关命令：
HGETALL：返回指定 Hash 中所有的 field-value 对。返回结果为数组，数组中 field 和 Value 交替出现。时间复杂度 O(N)。
HKEYS/HVALS：返回指定 Hash 中所有的 field/value，时间复杂度 O(N)。
上述三个命令都会对 Hash 进行完整遍历，Hash 中的 field 数量与命令的耗时线性相关，对于尺寸不可预知的 Hash，应严格避免使用上面三个命令。
而改为使用 HSCAN 命令进行游标式的遍历，具体请见：
https: //redis.io/commands/scan
Set
Redis Set 是无序的，不可重复的 String 集合。与 Set 相关的常用命令：
SADD：向指定 Set 中添加 1 个或多个 Member，如果指定 Set 不存在，会自动创建一个。时间复杂度 O(N),N为添加的 Member个数。
SREM：从指定 Set 中移除 1 个或多个 Member，时间复杂度 O(N)，N 为移除的 Member 个数。
SRANDMEMBER：从指定 Set 中随机返回 1 个或多个 Member，时间复杂度 O(N)，N 为返回的 Member 个数。
SPOP：从指定 Set 中随机移除并返回 Count 个 Member，时间复杂度O(N)，N 为移除的 Member 个数。
SCARD：返回指定 Set 中的 Member 个数，时间复杂度 O(1)。
SISMEMBER：判断指定的 Value 是否存在于指定 Set 中，时间复杂度 O(1)。
SMOVE：将指定 Member 从一个 Set 移至另一个 Set。
慎用的 Set 相关命令：
SMEMBERS：返回指定 Hash 中所有的 Member，时间复杂度 O(N)。
SUNION/SUNIONSTORE：计算多个 Set 的并集并返回/存储至另一个 Set 中，时间复杂度 O(N)，N为参与计算的所有集合的总Member 数。
SINTER/SINTERSTORE：计算多个 Set 的交集并返回/存储至另一个 Set 中，时间复杂度 O(N)，N为参与计算的所有集合的总Member 数。
SDIFF/SDIFFSTORE：计算 1 个 Set 与 1 或多个 Set 的差集并返回/存储至另一个 Set 中，时间复杂度 O(N)，N 为参与计算的所有集合的总 Member 数。
上述几个命令涉及的计算量大，应谨慎使用，特别是在参与计算的 Set 尺寸不可知的情况下，应严格避免使用。
可以考虑通过 SSCAN 命令遍历获取相关 Set 的全部 Member，具体请见：
https: //redis.io/commands/scan
如果需要做并集/交集/差集计算，可以在客户端进行，或在不服务实时查询请求的 Slave 上进行。
Sorted Set
Redis Sorted Set 是有序的、不可重复的 String 集合。Sorted Set 中的每个元素都需要指派一个分数(score)，Sorted Set 会根据 Score 对元素进行升序排序。
如果多个 Member 拥有相同的 Score，则以字典序进行升序排序。Sorted Set 非常适合用于实现排名。
Sorted Set 的主要命令：
ZADD：向指定 Sorted Set 中添加 1 个或多个 Member，时间复杂度 O(Mlog(N))，M 为添加的 Member 数量，N 为 Sorted Set 中的 Member 数量。
ZREM：从指定 Sorted Set 中删除 1 个或多个 Member，时间复杂度 O(Mlog(N))，M 为删除的 Member 数量，N 为 Sorted Set 中的 Member 数量。
ZCOUNT：返回指定 Sorted Set 中指定 Score 范围内的 Member 数量，时间复杂度：O(log(N))。
ZCARD：返回指定 Sorted Set 中的 Member 数量，时间复杂度 O(1)。
ZSCORE：返回指定 Sorted Set 中指定 Member 的 Score，时间复杂度 O(1)。
ZRANK/ZREVRANK：返回指定 Member 在 Sorted Set 中的排名，ZRANK 返回按升序排序的排名，ZREVRANK 则返回按降序排序的排名。时间复杂度 O(log(N))。
ZINCRBY：同 INCRBY，对指定 Sorted Set 中的指定 Member 的 Score 进行自增，时间复杂度 O(log(N))。
慎用的 Sorted Set 相关命令：
ZRANGE/ZREVRANGE：返回指定 Sorted Set 中指定排名范围内的所有 Member，ZRANGE 为按 Score 升序排序，ZREVRANGE 为按 Score 降序排序，时间复杂度 O(log(N)+M)，M 为本次返回的 Member 数。
ZRANGEBYSCORE/ZREVRANGEBYSCORE：返回指定 Sorted Set 中指定 Score 范围内的所有 Member，返回结果以升序/降序排序。 Min 和 Max 可以指定为 -inf 和 +inf，代表返回所有的 Member。时间复杂度 O(log(N)+M)。
ZREMRANGEBYRANK/ZREMRANGEBYSCORE：移除 Sorted Set 中指定排名范围/指定 Score 范围内的所有 Member。时间复杂度 O(log(N)+M)。
上述几个命令，应尽量避免传递 [0 -1] 或 [-inf +inf] 这样的参数，来对 Sorted Set 做一次性的完整遍历，特别是在 Sorted Set 的尺寸不可预知的情况下。
可以通过 ZSCAN 命令来进行游标式的遍历，具体请见：
https: //redis.io/commands/scan
或通过 LIMIT 参数来限制返回 Member 的数量（适用于 ZRANGEBYSCORE 和 ZREVRANGEBYSCORE 命令），以实现游标式的遍历。
Bitmap 和 HyperLogLog
Redis 的这两种数据结构相较之前的并不常用，在本文中只做简要介绍，如想要详细了解这两种数据结构与其相关的命令，请参考官方文档中的相关章节：
https: //redis.io/topics/data-types-intro
Bitmap 在 Redis 中不是一种实际的数据类型，而是一种将 String 作为 Bitmap 使用的方法。
可以理解为将 String 转换为 bit 数组。使用 Bitmap 来存储 true/false 类型的简单数据极为节省空间。
HyperLogLogs 是一种主要用于数量统计的数据结构，它和 Set 类似，维护一个不可重复的 String 集合，但是 HyperLogLogs 并不维护具体的 Member 内容，只维护 Member 的个数。
也就是说，HyperLogLogs 只能用于计算一个集合中不重复的元素数量，所以它比 Set 要节省很多内存空间。
其他常用命令：
EXISTS：判断指定的 Key 是否存在，返回 1 代表存在，0 代表不存在，时间复杂度 O(1)。
DEL：删除指定的 Key 及其对应的 Value，时间复杂度 O(N)，N 为删除的 Key 数量。
EXPIRE/PEXPIRE：为一个 Key 设置有效期，单位为秒或毫秒，时间复杂度 O(1)。
TTL/PTTL：返回一个 Key 剩余的有效时间，单位为秒或毫秒，时间复杂度 O(1)。
RENAME/RENAMENX：将 Key 重命名为 Newkey。使用 RENAME 时，如果 Newkey 已经存在，其值会被覆盖。 使用 RENAMENX 时，如果 Newkey 已经存在，则不会进行任何操作，时间复杂度 O(1)。
TYPE：返回指定 Key 的类型，String，List，Set，Zset，Hash。时间复杂度 O(1)。
CONFIG GET：获得 Redis 某配置项的当前值，可以使用 * 通配符，时间复杂度 O(1)。
CONFIG SET：为 Redis 某个配置项设置新值，时间复杂度 O(1)。
CONFIG REWRITE：让 Redis 重新加载 redis.conf 中的配置。
数据持久化
Redis 提供了将数据定期自动持久化至硬盘的能力，包括 RDB 和 AOF 两种方案，两种方案分别有其长处和短板，可以配合起来同时运行，确保数据的稳定性。
必须使用数据持久化吗？Redis 的数据持久化机制是可以关闭的。如果你只把 Redis 作为缓存服务使用，Redis 中存储的所有数据都不是该数据的主体而仅仅是同步过来的备份，那么可以关闭 Redis 的数据持久化机制。
但通常来说，仍然建议至少开启 RDB 方式的数据持久化，因为：
RDB 方式的持久化几乎不损耗 Redis 本身的性能，在进行 RDB 持久化时，Redis 主进程唯一需要做的事情就是 Fork 出一个子进程，所有持久化工作都由子进程完成。
Redis 无论因为什么原因 Crash 掉之后，重启时能够自动恢复到上一次 RDB 快照中记录的数据。 这省去了手工从其他数据源（如 DB）同步数据的过程，而且要比其他任何的数据恢复方式都要快。
现在硬盘那么大，真的不缺那一点地方。
RDB
采用 RDB 持久方式，Redis 会定期保存数据快照至一个 RBD 文件中，并在启动时自动加载 RDB 文件，恢复之前保存的数据。
可以在配置文件中配置 Redis 进行快照保存的时机：
save[seconds][changes]
意为在 [seconds] 秒内如果发生了 [changes] 次数据修改，则进行一次 RDB 快照保存，例如：
save60100
会让 Redis 每 60 秒检查一次数据变更情况，如果发生了 100 次或以上的数据变更，则进行 RDB 快照保存。可以配置多条 Save 指令，让 Redis 执行多级的快照保存策略。
Redis 默认开启 RDB 快照，默认的 RDB 策略如下：
save9001
save 30010
save 6010000
也可以通过 BGSAVE 命令手工触发 RDB 快照保存。
RDB 的优点：
对性能影响最小。如前文所述，Redis 在保存 RDB 快照时会 Fork 出子进程进行，几乎不影响 Redis 处理客户端请求的效率。
每次快照会生成一个完整的数据快照文件，所以可以辅以其他手段保存多个时间点的快照（例如把每天 0 点的快照备份至其他存储媒介中），作为非常可靠的灾难恢复手段。
使用 RDB 文件进行数据恢复比使用 AOF 要快很多。
RDB 的缺点：
快照是定期生成的，所以在 Redis Crash 时或多或少会丢失一部分数据。
如果数据集非常大且 CPU 不够强（比如单核 CPU），Redis 在 Fork 子进程时可能会消耗相对较长的时间（长至 1 秒），影响这期间的客户端请求。
AOF
采用 AOF 持久方式时，Redis 会把每一个写请求都记录在一个日志文件里。在 Redis 重启时，会把 AOF 文件中记录的所有写操作顺序执行一遍，确保数据恢复到最新。
AOF 默认是关闭的，如要开启，进行如下配置：
appendonlyyes
AOF提供了三种Fsync配置，always/everysec/no，通过配置项 [appendfsync] 指定：
appendfsync no：不进行 Fsync，将 Flush 文件的时机交给 OS 决定，速度最快。
appendfsync always：每写入一条日志就进行一次 Fsync 操作，数据安全性最高，但速度最慢。
appendfsync everysec：折中的做法交由后台线程每秒 Fsync 一次。
随着 AOF 不断地记录写操作日志，必定会出现一些无用的日志，例如某个时间点执行了命令 SET key1 “abc”，在之后某个时间点又执行了 SET key1 “bcd”，那么第一条命令很显然是没有用的。
大量的无用日志会让 AOF 文件过大，也会让数据恢复的时间过长。所以 Redis 提供了 AOF Rewrite 功能，可以重写 AOF 文件，只保留能够把数据恢复到最新状态的最小写操作集。
AOF Rewrite 可以通过 BGREWRITEAOF 命令触发，也可以配置 Redis 定期自动进行：
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
上面两行配置的含义是，Redis 在每次 AOF Rewrite 时，会记录完成 Rewrite 后的 AOF 日志大小，当 AOF 日志大小在该基础上增长了 100% 后，自动进行 AOF Rewrite。
同时如果增长的大小没有达到 64MB，则不会进行 Rewrite。
AOF 的优点：
最安全，在启用 appendfsync always 时，任何已写入的数据都不会丢失使用在启用appendfsync everysec也至多只会丢失1秒的数据。
AOF 文件在发生断电等问题时也不会损坏，即使出现了某条日志只写入了一半的情况，也可以使用 redis-check-aof 工具轻松修复。
AOF 文件易读，可修改，在进行了某些错误的数据清除操作后，只要 AOF 文件没有 Rewrite，就可以把 AOF 文件备份出来，把错误的命令删除，然后恢复数据。
AOF 的缺点：
AOF 文件通常比 RDB 文件更大。
性能消耗比 RDB 高。
数据恢复速度比 RDB 慢。
内存管理与数据淘汰机制
最大内存设置
默认情况下，在 32 位 OS 中，Redis 最大使用 3GB 的内存，在 64 位 OS 中则没有限制。
在使用 Redis 时，应该对数据占用的最大空间有一个基本准确的预估，并为 Redis 设定最大使用的内存。
否则在 64 位 OS 中 Redis 会无限制地占用内存（当物理内存被占满后会使用 Swap 空间），容易引发各种各样的问题。
通过如下配置控制 Redis 使用的最大内存：
maxmemory100mb
在内存占用达到了maxmemory后，再向Redis写入数据时，Redis 会：
根据配置的数据淘汰策略尝试淘汰数据，释放空间。
如果没有数据可以淘汰，或者没有配置数据淘汰策略，那么 Redis 会对所有写请求返回错误，但读请求仍然可以正常执行。
在为 Redis 设置 maxmemory 时，需要注意：如果采用了 Redis 的主从同步，主节点向从节点同步数据时，会占用掉一部分内存空间；如果maxmemory过于接近主机的可用内存，导致数据同步时内存不足。
所以设置的 maxmemory 不要过于接近主机可用的内存，留出一部分预留用作主从同步。
数据淘汰机制
Redis 提供了 5 种数据淘汰策略：
volatile-lru：使用 LRU 算法进行数据淘汰（淘汰上次使用时间最早的，且使用次数最少的 Key），只淘汰设定了有效期的 Key。
allkeys-lru：使用 LRU 算法进行数据淘汰，所有的 Key 都可以被淘汰。
volatile-random：随机淘汰数据，只淘汰设定了有效期的 Key。
allkeys-random：随机淘汰数据，所有的 Key 都可以被淘汰。
volatile-ttl：淘汰剩余有效期最短的 Key。
最好为 Redis 指定一种有效的数据淘汰策略以配合 maxmemory 设置，避免在内存使用满后发生写入失败的情况。
一般来说，推荐使用的策略是 volatile-lru，并辨识 Redis 中保存的数据的重要性。
对于那些重要的，绝对不能丢弃的数据（如配置类数据等），应不设置有效期，这样 Redis 就永远不会淘汰这些数据。
对于那些相对不是那么重要的，并且能够热加载的数据（比如缓存最近登录的用户信息，当在 Redis 中找不到时，程序会去 DB 中读取），可以设置上有效期，这样在内存不够时 Redis 就会淘汰这部分数据。
配置方法：
maxmemory-policy volatile-lru #默认是noeviction，即不进行数据淘汰
Pipelining
Redis 提供许多批量操作的命令，如 MSET/MGET/HMSET/HMGET 等等，这些命令存在的意义是减少维护网络连接和传输数据所消耗的资源和时间。
例如连续使用 5 次 SET 命令设置 5 个不同的 Key，比起使用一次 MSET 命令设置 5 个不同的 Key，效果是一样的，但前者会消耗更多的 RTT(Round Trip Time)时长，永远应优先使用后者。
然而，如果客户端要连续执行的多次操作无法通过 Redis 命令组合在一起，例如：
SETa "abc"
INCR b
HSET c name"hi"
此时便可以使用 Redis 提供的 Pipelining 功能来实现在一次交互中执行多条命令。
使用 Pipelining 时，只需要从客户端一次向 Redis 发送多条命令（以 rn）分隔，Redis 就会依次执行这些命令，并且把每个命令的返回按顺序组装在一起一次返回，比如：
$ (printf "PINGrnPINGrnPINGrn"; sleep 1) | nc localhost 6379
+PONG
+PONG
+PONG
大部分的 Redis 客户端都对 Pipelining 提供支持，所以开发者通常并不需要自己手工拼装命令列表。
Pipelining 的局限性
Pipelining 只能用于执行连续且无相关性的命令，当某个命令的生成需要依赖于前一个命令的返回时，就无法使用 Pipelining 了。通过 ing 功能，可以规避这一局限性。
事务与 ing
Pipelining 能够让 Redis 在一次交互中处理多条命令，然而在一些场景下，我们可能需要在此基础上确保这一组命令是连续执行的。
比如获取当前累计的 PV 数并将其清 0：
> GET vCount  12384
> SET vCount 0  OK
如果在 GET 和 SET 命令之间插进来一个 INCR vCount，就会使客户端拿到的 vCount 不准确。
Redis 的事务可以确保复数命令执行时的原子性。也就是说 Redis 能够保证：一个事务中的一组命令是绝对连续执行的，在这些命令执行完成之前，绝对不会有来自于其他连接的其他命令插进去执行。
通过 MULTI 和 EXEC 命令来把这两个命令加入一个事务中：
> MULTI
OK
> GET vCount
QUEUED
> SET vCount 0
QUEUED
> EXEC
1) 12384
2) OK
Redis 在接收到 MULTI 命令后便会开启一个事务，这之后的所有读写命令都会保存在队列中但并不执行，直到接收到 EXEC 命令后，Redis 会把队列中的所有命令连续顺序执行，并以数组形式返回每个命令的返回结果。
可以使用 DISCARD 命令放弃当前的事务，将保存的命令队列清空。需要注意的是，Redis 事务不支持回滚。
如果一个事务中的命令出现了语法错误，大部分客户端驱动会返回错误，2.6.5 版本以上的 Redis 也会在执行 EXEC 时检查队列中的命令是否存在语法错误，如果存在，则会自动放弃事务并返回错误。
但如果一个事务中的命令有非语法类的错误（比如对 String 执行 HSET 操作），无论客户端驱动还是 Redis 都无法在真正执行这条命令之前发现，所以事务中的所有命令仍然会被依次执行。
在这种情况下，会出现一个事务中部分命令成功部分命令失败的情况，然而与 RDBMS 不同，Redis 不提供事务回滚的功能，所以只能通过其他方法进行数据的回滚。
通过事务实现 CAS
Redis 提供了 WATCH 命令与事务搭配使用，实现CAS乐观锁的机制。
假设要实现将某个商品的状态改为已售：
if( exec(HGET stock: 1001state) == "in stock")
exec(HSET stock: 1001state"sold");
这一伪代码执行时，无法确保并发安全性，有可能多个客户端都获取到了”in stock”的状态，导致一个库存被售卖多次。
使用 WATCH 命令和事务可以解决这一问题：
exec(WATCH stock: 1001);
if( exec(HGET stock: 1001state) == "in stock") {
exec(MULTI);
exec(HSET stock: 1001state"sold");
exec(EXEC);}
WATCH 的机制是：在事务 EXEC 命令执行时，Redis 会检查被 WATCH 的 Key，只有被 WATCH 的 Key 从 WATCH 起始时至今没有发生过变更，EXEC 才会被执行。
如果 WATCH 的 Key 在 WATCH 命令到 EXEC 命令之间发生过变化，则 EXEC 命令会返回失败。
ing通过 EVAL 与 EVALSHA 命令，可以让 Redis 执行 LUA 脚本。这就类似于 RDBMS 的存储过程一样，可以把客户端与 Redis 之间密集的读/写交互放在服务端进行，避免过多的数据交互，提升性能。
ing 功能是作为事务功能的替代者诞生的，事务提供的所有能力 ing 都可以做到。Redis 官方推荐使用 LUA 来代替事务，前者的效率和便利性都超过了事务。
关于 ing 的具体使用，本文不做详细介绍，请参考官方文档：
https://redis.io/commands/ eval
Redis 性能调优
尽管 Redis 是一个非常快速的内存数据存储媒介，也并不代表 Redis 不会产生性能问题。
前文中提到过，Redis 采用单线程模型，所有的命令都是由一个线程串行执行的，所以当某个命令执行耗时较长时，会拖慢其后的所有命令，这使得 Redis 对每个任务的执行效率更加敏感。
针对 Redis 的性能优化，主要从下面几个层面入手：
最初的也是最重要的，确保没有让 Redis 执行耗时长的命令。
使用 Pipelining 将连续执行的命令组合执行。
操作系统的 Transparent huge pages 功能必须关闭。
echo never > /sys/kernel/mm/transparent_hugepage/enabled
如果在虚拟机中运行 Redis，可能天然就有虚拟机环境带来的固有延迟。可以通过 ./redis-cli –intrinsic-latency 100 命令查看固有延迟。 同时如果对 Redis 的性能有较高要求的话，应尽可能在物理机上直接部署 Redis。
检查数据持久化策略。
考虑引入读写分离机制。
长耗时命令
Redis 绝大多数读写命令的时间复杂度都在 O(1) 到 O(N) 之间，在文本和官方文档中均对每个命令的时间复杂度有说明。
通常来说，O(1) 的命令是安全的，O(N) 命令在使用时需要注意，如果 N 的数量级不可预知，则应避免使用。
例如对一个 field 数未知的 Hash 数据执行 HGETALL/HKEYS/HVALS 命令，通常来说这些命令执行的很快，但如果这个 Hash 中的 field 数量极多，耗时就会成倍增长。
又如使用 SUNION 对两个 Set 执行 Union 操作，或使用 SORT 对 List/Set 执行排序操作等时，都应该严加注意。
避免在使用这些 O(N) 命令时发生问题主要有几个办法：
不要把 List 当做列表使用，仅当做队列来使用。
通过机制严格控制 Hash、Set、Sorted Set 的大小。
可能的话，将排序、并集、交集等操作放在客户端执行。
绝对禁止使用 Keys 命令。
避免一次性遍历集合类型的所有成员，而应使用 SCAN 类的命令进行分批的，游标式的遍历。
Redis 提供了 SCAN 命令，可以对 Redis 中存储的所有 Key 进行游标式的遍历，避免使用 Keys 命令带来的性能问题。
同时还有 SSCAN/HSCAN/ZSCAN 等命令，分别用于对 Set/Hash/Sorted Set 中的元素进行游标式遍历。
SCAN 类命令的使用请参考官方文档：
https: //redis.io/commands/scan
Redis 提供了 Slow Log 功能，可以自动记录耗时较长的命令。相关的配置参数有两个：
slowlog- log-slower-than xxxms #执行时间慢于xxx毫秒的命令计入Slow Log
slowlog-max- lenxxx #Slow Log的长度，即最大纪录多少条Slow Log
使用 SLOWLOG GET [number] 命令，可以输出最近进入 Slow Log 的 Mumber 条命令。使用 SLOWLOG RESET 命令，可以重置 Slow Log。
 
Redis五大数据类型应用场景：
解下Redis内部内存管理中是如何描
 述这些不同数据类型的：
首先Redis内部使用一个redisObject对象来表示所有的key和value，redisObject最主要的信息如上图所示：type代表一个value对象具体是何种数据类型，encoding是不同数据类型在redis内部的存储方式，比如：type=string代表value存储的是一个普通字符串，那么对应的encoding可以是raw或者是int，如果是int则代表实际redis内部是按数值型类存储和表示这个字符串的，当然前提是这个字符串本身可以用数值表示，比如:"123" "456"这样的字符串。
  这里需要特殊说明一下vm字段，只有打开了Redis的虚拟内存功能，此字段才会真正的分配内存，该功能默认是关闭状态的。通过上图我们可以发现Redis使用redisObject来表示所有的key/value数据是比较浪费内存的，当然这些内存管理成本的付出主要也是为了给Redis不同数据类型提供一个统一的管理接口，实际作者也提供了多种方法帮助我们尽量节省内存使用，我们随后会具体讨论。
二各种数据类型应用和实现方式
下面我们先来逐一的分析下这五种数据类型的使用和内部实现方式：
2.1  String
String 数据结构是简单的key-value类型，value其实不仅是String，也可以是数字。
常用命令：get、set、incr、decr、mget等。
应用场景：String是最常用的一种数据类型，普通的key/ value 存储都可以归为此类，即可以完全实现目前 Memcached 的功能，并且效率更高。还可以享受Redis的定时持久化，操作日志及 Replication等功能。除了提供与 Memcached 一样的get、set、incr、decr 等操作外，Redis还提供了下面一些操作： 
获取字符串长度
往字符串append内容
设置和获取字符串的某一段内容
设置及获取字符串的某一位（bit）
批量设置一系列字符串的内容
使用场景：常规key-value缓存应用。常规计数: 微博数, 粉丝数。
实现方式：String在redis内部存储默认就是一个字符串，被redisObject所引用，当遇到incr,decr等操作时会转成数值型进行计算，此时redisObject的encoding字段为int。
2.2 Hash
常用命令：hget,hset,hgetall 等。
应用场景：我们简单举个实例来描述下Hash的应用场景，比如我们要存储一个用户信息对象数据，包含以下信息：
用户ID为查找的key，存储的value用户对象包含姓名，年龄，生日等信息，如果用普通的key/value结构来存储，主要有以下2种存储方式：
 
第一种方式将用户ID作为查找key,把其他信息封装成一个对象以序列化的方式存储，这种方式的缺点是，增加了序列化/反序列化的开销，并且在需要修改其中一项信息时，需要把整个对象取回，并且修改操作需要对并发进行保护，引入CAS等复杂问题。
 
第二种方法是这个用户信息对象有多少成员就存成多少个key-value对儿，用用户ID+对应属性的名称作为唯一标识来取得对应属性的值，虽然省去了序列化开销和并发问题，但是用户ID为重复存储，如果存在大量这样的数据，内存浪费还是非常可观的。
那么Redis提供的Hash很好的解决了这个问题，Redis的Hash实际是内部存储的Value为一个HashMap，并提供了直接存取这个Map成员的接口，如下图：
 
也就是说，Key仍然是用户ID, value是一个Map，这个Map的key是成员的属性名，value是属性值，这样对数据的修改和存取都可以直接通过其内部Map的Key(Redis里称内部Map的key为field), 也就是通过 key(用户ID) + field(属性标签) 就可以操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题。很好的解决了问题。
 这里同时需要注意，Redis提供了接口(hgetall)可以直接取到全部的属性数据，但是如果内部Map的成员很多，那么涉及到遍历整个内部Map的操作，由于Redis单线程模型的缘故，这个遍历操作可能会比较耗时，而另其它客户端的请求完全不响应，这点需要格外注意。
使用场景：存储部分变更数据，如用户信息等。
实现方式：上面已经说到Redis Hash对应Value内部实际就是一个HashMap，实际这里会有2种不同实现，这个Hash的成员比较少时Redis为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的HashMap结构，对应的value redisObject的encoding为zipmap，当成员数量增大时会自动转成真正的HashMap，此时encoding为ht。
2.3 List
常用命令：lpush,rpush,lpop,rpop,lrange等。
应用场景：Redis list的应用场景非常多，也是Redis最重要的数据结构之一，比如twitter的关注列表，粉丝列表等都可以用Redis的list结构来实现。
List 就是链表，相信略有数据结构知识的人都应该能理解其结构。使用List结构，我们可以轻松地实现最新消息排行等功能。List的另一个应用就是消息队列，
可以利用List的PUSH操作，将任务存在List中，然后工作线程再用POP操作将任务取出进行执行。Redis还提供了操作List中某一段的api，你可以直接查询，删除List中某一段的元素。
实现方式：Redis list的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销，Redis内部的很多实现，包括发送缓冲队列等也都是用的这个数据结构。
Redis的list是每个子元素都是String类型的双向链表，可以通过push和pop操作从列表的头部或者尾部添加或者删除元素，这样List即可以作为栈，也可以作为队列。 
使用场景：
消息队列系统
使用list可以构建队列系统，使用sorted set甚至可以构建有优先级的队列系统。
比如：将Redis用作日志收集器
实际上还是一个队列，多个端点将日志信息写入Redis，然后一个worker统一将所有日志写到磁盘。
取最新N个数据的操作
记录前N个最新登陆的用户Id列表超出的范围可以从数据库中获得。
//把当前登录人添加到链表里
ret = r.lpush("login:last_login_times", uid)
//保持链表只有N位
ret = redis.ltrim("login:last_login_times", 0, N-1)
//获得前N个最新登陆的用户Id列表
last_login_list = r.lrange("login:last_login_times", 0, N-1)
比如sina微博：
 在Redis中我们的最新微博ID使用了常驻缓存，这是一直更新的。但是我们做了限制不能超过5000个ID，因此我们的获取ID函数会一直询问Redis。只有在start/count参数超出了这个范围的时候，才需要去访问数据库。
  我们的系统不会像传统方式那样“刷新”缓存，Redis实例中的信息永远是一致的。SQL数据库（或是硬盘上的其他类型数据库）只是在用户需要获取“很远”的数据时才会被触发，而主页或第一个评论页是不会麻烦到硬盘上的数据库了。
二各种数据类型应用和实现方式
2.4  Set
常用命令：sadd,spop,smembers,sunion 等。
应用场景：Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。
Set 就是一个集合，集合的概念就是一堆不重复值的组合。利用Redis提供的Set数据结构，可以存储一些集合性的数据。
案例：在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。Redis还为集合提供了求交集、并集、差集等操作，可以非常方便的实现如共同关注、共同喜好、二度好友等功能，对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存集到一个新的集合中。
Set是集合，是String类型的无序集合，set是通过hashtable实现的，概念和数学中个的集合基本类似，可以交集，并集，差集等等，set中的元素是没有顺序的。
实现方式： set 的内部实现是一个 value永远为null的HashMap，实际就是通过计算hash的方式来快速排重的，这也是set能提供判断一个成员是否在集合内的原因。
使用场景：
交集，并集，差集：(Set)
//book表存储book名称
set book:1:name    ”The Ruby Programming Language”
set book:2:name     ”Ruby on rail”
set book:3:name     ”Programming Erlang”
//tag表使用集合来存储数据，因为集合擅长求交集、并集
sadd tag:ruby 1
sadd tag:ruby 2
sadd tag:web 2
sadd tag:erlang 3
//即属于ruby又属于web的书？
inter_list = redis.sinter("tag.web", "tag:ruby") 
//即属于ruby，但不属于web的书？
inter_list = redis.sdiff("tag.ruby", "tag:web") 
//属于ruby和属于web的书的合集？
inter_list = redis.sunion("tag.ruby", "tag:web")
获取某段时间所有数据去重值
这个使用Redis的set数据结构最合适了，只需要不断地将数据往set中扔就行了，set意为集合，所以会自动排重。
2.5  Sorted Set
常用命令：zadd,zrange,zrem,zcard等
使用场景：Redis sorted set的使用场景与set类似，区别是set不是自动有序的，而sorted set可以通过用户额外提供一个优先级(score)的参数来为成员排序，并且是插入有序的，即自动排序。当你需要一个有序的并且不重复的集合列表，那么可以选择sorted set数据结构，比如twitter 的public timeline可以以发表时间作为score来存储，这样获取时就是自动按时间排好序的。
  和Set相比，Sorted Set增加了一个权重参数score，使得集合中的元素能够按score进行有序排列，比如一个存储全班同学成绩的Sorted Set，其集合value可以是同学的学号，而score就可以是其考试得分，这样在数据插入集合的时候，就已经进行了天然的排序。另外还可以用Sorted Set来做带权重的队列，比如普通消息的score为1，重要消息的score为2，然后工作线程可以选择按score的倒序来获取工作任务。让重要的任务优先执行。
实现方式： Redis sorted set的内部使用HashMap和跳跃表(SkipList)来保证数据的存储和有序，HashMap里放的是成员到score的映射，而跳跃表里存放的是所有的成员，排序依据是HashMap里存的score,使用跳跃表的结构可以获得比较高的查找效率，并且在实现上比较简单。
三Redis实际应用场景
3.1  显示最新的项目列表
下面这个语句常用来显示最新项目，随着数据多了，查询毫无疑问会越来越慢。
SELECT * FROM foo WHERE ... ORDER BY time DESC LIMIT 10   
 在Web应用中，“列出最新的回复”之类的查询非常普遍，这通常会带来可扩展性问题。这令人沮丧，因为项目本来就是按这个顺序被创建的，但要输出这个顺序却不得不进行排序操作。类似的问题就可以用Redis来解决。比如说，我们的一个Web应用想要列出用户贴出的最新20条评论。在最新的评论边上我们有一个“显示全部”的链接，点击后就可以获得更多的评论。我们假设数据库中的每条评论都有一个唯一的递增的ID字段。我们可以使用分页来制作主页和评论页，使用Redis的模板，每次新评论发表时，我们会将它的ID添加到一个Redis列表：
LPUSH latest.comments <ID>   
我们将列表裁剪为指定长度，因此Redis只需要保存最新的5000条评论：
LTRIM latest.comments 0 5000 
每次我们需要获取最新评论的项目范围时，我们调用一个函数来完成（使用伪代码）：
FUNCTION get_latest_comments(start, num_items):  
   id_list = 
redis.lrange("latest.comments",start,start+num_items -1)
   IF id_list.length < num_items  
id_list = SQL_DB("SELECT ... ORDER BY time LIMIT ...")
  END  
   RETURN id_list  
END 
这里我们做的很简单。在Redis中我们的最新ID使用了常驻缓存，这是一直更新的。但是我们做了限制不能超过5000个ID，因此我们的获取ID函数会一直询问Redis。只有在start/count参数超出了这个范围的时候，才需要去访问数据库。
 我们的系统不会像传统方式那样“刷新”缓存，Redis实例中的信息永远是一致的。SQL数据库（或是硬盘上的其他类型数据库）只是在用户需要获取“很远”的数据时才会被触发，而主页或第一个评论页是不会麻烦到硬盘上的数据库了。
3.2  排行榜应用，取TOP N操作
 这个需求与上面需求的不同之处在于，取最新N个数据的操作以时间为权重，这个是以某个条件为权重，比如按顶的次数排序，这时候就需要我们的sorted set出马了，将你要排序的值设置成sorted set的score，将具体的数据设置成相应的value，每次只需要执行一条ZADD命令即可。
热门，排行榜应用：
//将登录次数和用户统一存储在一个
sorted set里zadd login:login_times 5 1zadd 
login:login_times 1 2zadd login:login_times 2 3
//当用户登录时，对该用户的登录次数自增
1ret = r.zincrby("login:login_times", 1, uid)//那么如何获得登录次数最多的用户呢，逆序排列取得排名前N的用户
ret = r.zrevrange("login:login_times", 0, N-1)
另一个很普遍的需求是各种数据库的数据并非存储在内存中，因此在按得分排序以及实时更新这些几乎每秒钟都需要更新的功能上数据库的性能不够理想。典型的比如那些在线游戏的排行榜，比如一个Facebook的游戏，根据得分你通常想要：
- 列出前100名高分选手
- 列出某用户当前的全球排名
这些操作对于Redis来说小菜一碟，即使你有几百万个用户，每分钟都会有几百万个新的得分。模式是这样的，每次获得新得分时，我们用这样的代码：
ZADD leaderboard  <score>  <username>
你可能用userID来取代username，这取决于你是怎么设计的。得到前100名高分用户很简单：
ZREVRANGE leaderboard 0 99
 用户的全球排名也相似，只需要：
ZRANK leaderboard <username>
3.3  删除与过滤
我们可以使用LREM来删除评论。如果删除操作非常少，另一个选择是直接跳过评论条目的入口，报告说该评论已经不存在。 有些时候你想要给不同的列表附加上不同的过滤器。如果过滤器的数量受到限制，你可以简单的为每个不同的过滤器使用不同的Redis列表。毕竟每个列表只有5000条项目，但Redis却能够使用非常少的内存来处理几百万条项目。
3.4  按照用户投票和时间排序
排行榜的一种常见变体模式就像Reddit或Hacker News用的那样，新闻按照类似下面的公式根据得分来排序：score = points / time^alpha 因此用户的投票会相应的把新闻挖出来，但时间会按照一定的指数将新闻埋下去。下面是我们的模式，当然算法由你决定。模式是这样的，开始时先观察那些可能是最新的项目，例如首页上的1000条新闻都是候选者，因此我们先忽视掉其他的，这实现起来很简单。每次新的新闻贴上来后，我们将ID添加到列表中，使用LPUSH + LTRIM，确保只取出最新的1000条项目。有一项后台任务获取这个列表，并且持续的计算这1000条新闻中每条新闻的最终得分。计算结果由ZADD命令按照新的顺序填充生成列表，老新闻则被清除。这里的关键思路是排序工作是由后台任务来完成的。
3.5  处理过期项目
另一种常用的项目排序是按照时间排序。我们使用unix时间作为得分即可。 模式如下：
- 每次有新项目添加到我们的非Redis数据库时，我们把它加入到排序集合中。这时我们用的是时间属性，current_time和time_to_live。
- 另一项后台任务使用ZRANGE…SCORES查询排序集合，取出最新的10个项目。如果发现unix时间已经过期，则在数据库中删除条目。
3.6  计数
 Redis是一个很好的计数器，这要感谢INCRBY和其他相似命令。我相信你曾许多次想要给数据库加上新的计数器，用来获取统计或显示新信息，但是最后却由于写入敏感而不得不放弃它们。好了，现在使用Redis就不需要再担心了。有了原子递增（atomic increment），你可以放心的加上各种计数，用GETSET重置，或者是让它们过期。例如这样操作：
INCR user:<id> EXPIRE
你可以计算出最近用户在页面间停顿不超过60秒的页面浏览量，当计数达到比如20时，就可以显示出某些条幅提示，或是其它你想显示的东西。
3.7  特定时间内的特定项目
另一项对于其他数据库很难，但Redis做起来却轻而易举的事就是统计在某段特点时间里有多少特定用户访问了某个特定资源。比如我想要知道某些特定的注册用户或IP地址，他们到底有多少访问了某篇文章。每次我获得一次新的页面浏览时我只需要这样做
SADD page:day1:<page_id> <user_id> 
当然你可能想用unix时间替换day1，比如time()-(time()%3600*24)等等。 想知道特定用户的数量吗？只需要使用
SCARD page:day1:<page_id>
3.8 查找某个值所在的区间(区间无重合) 
Sorted Set：
例如有下面两个范围，10－20和30－40
A_start 10, A_end 20
B_start 30, B_end 40
我们将这两个范围的起始位置存在Redis的Sorted Sets数据结构中，基本范围起始值作为score，范围名加start和end为其value值：
redis 127.0.0.1:6379> zadd ranges 10 A_start
(integer) 1redis 127.0.0.1:6379> zadd ranges 20 A_end
(integer) 1redis 127.0.0.1:6379> zadd ranges 30 B_start
(integer) 1redis 127.0.0.1:6379> zadd ranges 40 B_end
(integer) 1
这样数据在插入Sorted Sets后，相当于是将这些起始位置按顺序排列好了。现在我需要查找15这个值在哪一个范围中，只需要进行如下的zrangbyscore查找：
redis 127.0.0.1:6379> zrangebyscore ranges (15 +inf LIMIT 0 1 1) "A_end"
这个命令的意思是在Sorted Sets中查找大于15的第一个值。（+inf在Redis中表示正无穷大，15前面的括号表示>15而非>=15）查找的结果是A_end，由于所有值是按顺序排列的，所以可以判定15是在A_start到A_end区间上，也就是说15是在A这个范围里。至此大功告成。
3.9  交集，并集，差集：(Set)
//book表存储book名称
set book:1:name    ”The Ruby Programming Language”
set book:2:name     ”Ruby on rail”
set book:3:name     ”Programming Erlang”
//tag表使用集合来存储数据，因为集合擅长求交集、并集sadd tag:ruby 1sadd tag:ruby 2sadd tag:web 2sadd tag:erlang 3
//即属于ruby又属于web的书？
inter_list = redis.sinter("tag.web", "tag:ruby") 
//即属于ruby，但不属于web的书？
inter_list = redis.sdiff("tag.ruby", "tag:web") 
//属于ruby和属于web的书的合集？
inter_list = redis.sunion("tag.ruby", "tag:web")
网络引发的延迟
尽可能使用长连接或连接池，避免频繁创建销毁连接，客户端进行的批量数据操作，应使用 Pipeline 特性在一次交互中完成。具体请参照本文的 Pipelining 章节。
数据持久化引发的延迟
Redis 的数据持久化工作本身就会带来延迟，需要根据数据的安全级别和性能要求制定合理的持久化策略：
AOF + fsync always 的设置虽然能够绝对确保数据安全，但每个操作都会触发一次 Fsync，会对 Redis 的性能有比较明显的影响。
AOF + fsync every second 是比较好的折中方案，每秒 Fsync 一次。
AOF + fsync never 会提供 AOF 持久化方案下的最优性能。
使用 RDB 持久化通常会提供比使用 AOF 更高的性能，但需要注意 RDB 的策略配置。
每一次 RDB 快照和 AOF Rewrite 都需要 Redis 主进程进行 Fork 操作。Fork 操作本身可能会产生较高的耗时，与 CPU 和 Redis 占用的内存大小有关。 根据具体的情况合理配置 RDB 快照和 AOF Rewrite 时机，避免过于频繁的 Fork 带来的延迟。
Redis 在 Fork 子进程时需要将内存分页表拷贝至子进程，以占用了 24GB 内存的 Redis 实例为例，共需要拷贝 24GB / 4KB * 8 = 48MB 的数据。
在使用单 Xeon 2.27Ghz 的物理机上，这一 Fork 操作耗时 216ms。可以通过 INFO 命令返回的 latest_fork_usec 字段查看上一次 Fork 操作的耗时（微秒）。
Swap 引发的延迟
当 Linux 将 Redis 所用的内存分页移至 Swap 空间时，将会阻塞 Redis 进程，导致 Redis 出现不正常的延迟。
Swap 通常在物理内存不足或一些进程在进行大量 I/O 操作时发生，应尽可能避免上述两种情况的出现。
/proc/<pid>/smaps 文件中会保存进程的 Swap 记录，通过查看这个文件，能够判断 Redis 的延迟是否由 Swap 产生。
如果这个文件中记录了较大的 Swap size，则说明延迟很有可能是 Swap 造成的。
数据淘汰引发的延迟
当同一秒内有大量 Key 过期时，也会引发 Redis 的延迟。在使用时应尽量将 Key 的失效时间错开。
引入读写分离机制
Redis 的主从复制能力可以实现一主多从的多节点架构，在这一架构下，主节点接收所有写请求，并将数据同步给多个从节点。
在这一基础上，我们可以让从节点提供对实时性要求不高的读请求服务，以减小主节点的压力。
尤其是针对一些使用了长耗时命令的统计类任务，完全可以指定在一个或多个从节点上执行，避免这些长耗时命令影响其他请求的响应。
主从复制与集群分片
主从复制
Redis 支持一主多从的主从复制架构。一个 Master 实例负责处理所有的写请求，Master 将写操作同步至所有 Slave。
借助 Redis 的主从复制，可以实现读写分离和高可用：
实时性要求不是特别高的读请求，可以在 Slave 上完成，提升效率。特别是一些周期性执行的统计任务，这些任务可能需要执行一些长耗时的 Redis 命令，可以专门规划出 1 个或几个 Slave 用于服务这些统计任务。
借助 Redis Sentinel 可以实现高可用，当 Master Crash 后，Redis Sentinel 能够自动将一个 Slave 晋升为 Master，继续提供服务。
启用主从复制非常简单，只需要配置多个 Redis 实例，在作为 Slave 的 Redis 实例中配置：
slaveof192 .168.1.16379 #指定 Master的 IP和端口
当 Slave 启动后，会从 Master 进行一次冷启动数据同步，由 Master 触发 BGSAVE 生成 RDB 文件推送给 Slave 进行导入。
导入完成后 Master 再将增量数据通过 Redis Protocol 同步给 Slave。之后主从之间的数据便一直以 Redis Protocol 进行同步。
使用 Sentinel 做自动 Failover：Redis 的主从复制功能本身只是做数据同步，并不提供监控和自动 Failover 能力，要通过主从复制功能来实现 Redis 的高可用，还需要引入一个组件：Redis Sentinel。
Redis Sentinel 是 Redis 官方开发的监控组件，可以监控 Redis 实例的状态，通过 Master 节点自动发现 Slave 节点，并在监测到 Master 节点失效时选举出一个新的 Master，并向所有 Redis 实例推送新的主从配置。
Redis Sentinel 需要至少部署 3 个实例才能形成选举关系。关键配置：
sentinelmonitor mymaster 127.0.0.163792#Master实例的IP、端口，以及选举需要的赞成票数
sentinel down-after-milliseconds mymaster 60000#多长时间没有响应视为Master失效
sentinel failover-timeout mymaster 180000#两次failover尝试间的间隔时长
sentinel parallel-syncs mymaster 1#如果有多个Slave，可以通过此配置指定同时从新Master进行数据同步的Slave数，避免所有Slave同时进行数据同步导致查询服务也不可用
另外需要注意的是，Redis Sentinel 实现的自动 Failover 不是在同一个 IP 和端口上完成的。
也就是说自动 Failover 产生的新 Master 提供服务的 IP 和端口与之前的 Master 是不一样的。
所以要实现 HA，还要求客户端必须支持 Sentinel，能够与 Sentinel 交互获得新 Master 的信息才行。
集群分片
为何要做集群分片？原因如下：
Redis 中存储的数据量大，一台主机的物理内存已经无法容纳。
Redis 的写请求并发量大，一个 Redis 实例以无法承载。
当上述两个问题出现时，就必须要对 Redis 进行分片了。Redis 的分片方案有很多种，例如很多 Redis 的客户端都自行实现了分片功能，也有向 Twemproxy 这样的以代理方式实现的 Redis 分片方案。
然而首选的方案还应该是 Redis 官方在 3.0 版本中推出的 Redis Cluster 分片方案。
本文不会对 Redis Cluster 的具体安装和部署细节进行介绍，重点介绍 Redis Cluster 带来的好处与弊端。
Redis Cluster 的能力：
能够自动将数据分散在多个节点上。
当访问的 Key 不在当前分片上时，能够自动将请求转发至正确的分片。
当集群中部分节点失效时仍能提供服务。
其中第三点是基于主从复制来实现的，Redis Cluster 的每个数据分片都采用了主从复制的结构，原理和前文所述的主从复制完全一致。
唯一的区别是省去了 Redis Sentinel 这一额外的组件，由 Redis Cluster 负责进行一个分片内部的节点监控和自动 Failover。
Redis Cluster 分片原理：Redis Cluster 中共有 16384 个 hash slot，Redis 会计算每个 Key 的 CRC16，将结果与 16384 取模，来决定该 Key 存储在哪一个 hash slot 中。
同时需要指定 Redis Cluster 中每个数据分片负责的 Slot 数。Slot 的分配在任何时间点都可以进行重新分配。
客户端在对 Key 进行读写操作时，可以连接 Cluster 中的任意一个分片，如果操作的 Key 不在此分片负责的 Slot 范围内，Redis Cluster 会自动将请求重定向到正确的分片上。
Hash Tags：在基础的分片原则上，Redis 还支持 hash tags 功能，以 hash tags 要求的格式明明的 Key，将会确保进入同一个 Slot 中。
例如：{uiv}user:1000 和 {uiv}user:1001 拥有同样的 hash tag {uiv}，会保存在同一个 Slot 中。
使用 Redis Cluster 时，Pipelining、事务和 LUA 功能涉及的 Key 必须在同一个数据分片上，否则将会返回错误。
如要在 Redis Cluster 中使用上述功能，就必须通过 hash tags 来确保一个 Pipeline 或一个事务中操作的所有 Key 都位于同一个 Slot 中。
有一些客户端（如 Redisson）实现了集群化的 Pipelining 操作，可以自动将一个 Pipeline 里的命令按 Key 所在的分片进行分组，分别发到不同的分片上执行。
但是 Redis 不支持跨分片的事务，事务和 LUA 还是必须遵循所有 Key 在一个分片上的规则要求。
主从复制 VS 集群分片
在设计软件架构时，要如何在主从复制和集群分片两种部署方案中取舍呢？从各个方面看，Redis Cluster 都是优于主从复制的方案：
Redis Cluster 能够解决单节点上数据量过大的问题。
Redis Cluster 能够解决单节点访问压力过大的问题。
Redis Cluster 包含了主从复制的能力。
那是不是代表 Redis Cluster 永远是优于主从复制的选择呢？并不是。软件架构永远不是越复杂越好，复杂的架构在带来显著好处的同时，一定也会带来相应的弊端。
采用 Redis Cluster 的弊端包括：
维护难度增加。在使用 Redis Cluster 时，需要维护的 Redis 实例数倍增，需要监控的主机数量也相应增加，数据备份/持久化的复杂度也会增加。 同时在进行分片的增减操作时，还需要进行 Reshard 操作，远比主从模式下增加一个 Slave 的复杂度要高。
客户端资源消耗增加。当客户端使用连接池时，需要为每一个数据分片维护一个连接池，客户端同时需要保持的连接数成倍增多，加大了客户端本身和操作系统资源的消耗。
性能优化难度增加。你可能需要在多个分片上查看 Slow Log 和 Swap 日志才能定位性能问题。
事务和 LUA 的使用成本增加。在 Redis Cluster 中使用事务和 LUA 特性有严格的限制条件，事务和 中操作的 Key 必须位于同一个分片上。 这就使得在开发时必须对相应场景下涉及的 Key 进行额外的规划和规范要求。如果应用的场景中大量涉及事务和 的使用，如何在保证这两个功能的正常运作前提下把数据平均分到多个数据分片中就会成为难点。
所以说，在主从复制和集群分片两个方案中做出选择时，应该从应用软件的功能特性、数据和访问量级、未来发展规划等方面综合考虑，只在确实有必要引入数据分片时再使用 Redis Cluster。
下面是一些建议：
需要在 Redis 中存储的数据有多大？未来 2 年内可能发展为多大？这些数据是否都需要长期保存？是否可以使用 LRU 算法进行非热点数据的淘汰？综合考虑前面几个因素，评估出 Redis 需要使用的物理内存。
用于部署 Redis 的主机物理内存有多大？有多少可以分配给 Redis 使用？对比 (1) 中的内存需求评估，是否足够用？
Redis 面临的并发写压力会有多大？在不使用 Pipelining 时，Redis 的写性能可以超过 10 万次/秒（更多的 Benchmark 可以参考 https://redis.io/topics/benchmarks ）。
在使用 Redis 时，是否会使用到 Pipelining 和事务功能？使用的场景多不多？
综合上面几点考虑，如果单台主机的可用物理内存完全足以支撑对 Redis 的容量需求，且 Redis 面临的并发写压力距离 Benchmark 值还尚有距离，建议采用主从复制的架构，可以省去很多不必要的麻烦。
同时，如果应用中大量使用 Pipelining 和事务，也建议尽可能选择主从复制架构，可以减少设计和开发时的复杂度。
Redis Java 客户端的选择
Redis 的 Java 客户端很多，官方推荐的有三种：
Jedis
Redisson
Lettuce
在这里对 Jedis 和 Redisson 进行对比介绍。
Jedis：
轻量，简洁，便于集成和改造。
支持连接池。
支持 Pipelining、事务、LUA ing、Redis Sentinel、Redis Cluster。
不支持读写分离，需要自己实现。
文档差（真的很差，几乎没有……）。
Redisson：
基于 Netty 实现，采用非阻塞 IO，性能高。
支持异步请求。
支持连接池。
支持 Pipelining、LUA ing、Redis Sentinel、Redis Cluster。
不支持事务，官方建议以 LUA ing 代替事务。
支持在 Redis Cluster 架构下使用 Pipelining。
支持读写分离，支持读负载均衡，在主从复制和 Redis Cluster 架构下都可以使用。
内建 Tomcat Session Manager，为 Tomcat 6/7/8 提供了会话共享功能。
可以与 Spring Session 集成，实现基于 Redis 的会话共享。
文档较丰富，有中文文档。
对于 Jedis 和 Redisson 的选择，同样应遵循前述的原理，尽管 Jedis 比起 Redisson 有各种各样的不足，但也应该在需要使用 Redisson 的高级特性时再选用 Redisson，避免造成不必要的程序复杂度提升。


