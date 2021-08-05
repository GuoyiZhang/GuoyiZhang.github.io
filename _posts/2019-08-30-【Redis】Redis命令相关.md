# 【Redis】Redis命令相关


# 1. 输入一下命令测试redis是否安装成功

127.0.0.1:6379> ping PONG 127.0.0.1:6379> set test hello OK 127.0.0.1:6379> get test "hello" 127.0.0.1:6379>

# 2. 清理redis Key

db数据库太大 可删除key redis-cli > command count > dbsize > FLUSHALL

# 3. 安装redis

1.下载 wget http://download.redis.io/releases/redis-5.0.5.tar.gz 2.解压 tar -xzvf redis-5.0.5.tar.gz 3. make 4. make install PREFIX=/www/server/redis-5.0.5 5.建立软连接 /# ln -s /www/server/redis-5.0.5/redis-server /usr/bin/redis-server /# ln -s /www/server/redis-5.0.5/redis-cli /usr/bin/redis-cli

# 4. 启动多个Redis

/# 启动6380端口 /# /www/server/redis/src/redis-server /www/server/redis/redis6380.conf & /# ps auxf | grep redis-server 获取当前所有redis服务 /# redis-cli -p 6380 进入6380命令行

# 5.允许外部访问redis

1、修改redis默认绑定的ip 　　vi /usr/local/redis/etc/redis.conf 　　找到 bind 127.0.0.1 这一行 　　修改为 bind 0.0.0.0，使得所有机器都可以访问 　　当然可以指定ip 2、修改redis配置文件，将保护模式关闭 　　vi /usr/local/redis/etc/redis.conf 　　找到 protected-mode yes 这一行 　　修改为protected-mode no

 


