# leikegeek.github.com

dependencies:
        @echo "dependencies"
        apt-get update
        apt-get install -y unzip build-essential tcl pkg-config && apt-get autoremove -y
        useradd -m -s /usr/sbin/nologin redis
        mkdir -p /home/redis/data/7001 /home/redis/data/7002 /home/redis/data/7003 /home/redis/data/7004 /home/redis/data/7005 /home/redis/data/7006 /home/redis/log


compile_redis:
        @echo "compile redis"
        unzip redis.zip -d redis
        tar -zxvf redis-5.0.14.tar.gz
        cd redis && make && make test


install:
        @echo "install redis"
        cp redis/src/redis-server redis/src/redis-cli redis/src/redis-sentinel redis/src/redis-benchmark redis/src/redis-check-rdb redis/src/redis-check-aof /usr/local/bin/
        mkdir -p /etc/redis/conf /var/redis
        @echo "init config"
cp redis.conf /etc/redis/conf/7001.conf
cp redis.conf /etc/redis/conf/7002.conf
cp redis.conf /etc/redis/conf/7003.conf
cp redis.conf /etc/redis/conf/7004.conf
cp redis.conf /etc/redis/conf/7005.conf
cp redis.conf /etc/redis/conf/7006.conf
sed -i "s/6379/7001/" /etc/redis/conf/7001.conf
sed -i "s/6379/7002/" /etc/redis/conf/7002.conf
sed -i "s/6379/7003/" /etc/redis/conf/7003.conf
sed -i "s/6379/7004/" /etc/redis/conf/7004.conf
sed -i "s/6379/7005/" /etc/redis/conf/7005.conf
sed -i "s/6379/7006/" /etc/redis/conf/7006.conf
        @echo "edit init script"
cp redis/utils/redis_init_script /etc/init.d/redis_7001
cp redis/utils/redis_init_script /etc/init.d/redis_7002
cp redis/utils/redis_init_script /etc/init.d/redis_7003
cp redis/utils/redis_init_script /etc/init.d/redis_7004
cp redis/utils/redis_init_script /etc/init.d/redis_7005
cp redis/utils/redis_init_script /etc/init.d/redis_7006

sed -i -e "s/6379/7001/" -e "s#CONF=\"/etc/redis/\${REDISPORT}\.conf\"#CONF=\"/etc/redis/conf/\${REDISPORT}\.conf\"#" /etc/init.d/redis_7001
sed -i -e "s/6379/7002/" -e "s#CONF=\"/etc/redis/\${REDISPORT}\.conf\"#CONF=\"/etc/redis/conf/\${REDISPORT}\.conf\"#" /etc/init.d/redis_7002
sed -i -e "s/6379/7003/" -e "s#CONF=\"/etc/redis/\${REDISPORT}\.conf\"#CONF=\"/etc/redis/conf/\${REDISPORT}\.conf\"#" /etc/init.d/redis_7003
sed -i -e "s/6379/7004/" -e "s#CONF=\"/etc/redis/\${REDISPORT}\.conf\"#CONF=\"/etc/redis/conf/\${REDISPORT}\.conf\"#" /etc/init.d/redis_7004
sed -i -e "s/6379/7005/" -e "s#CONF=\"/etc/redis/\${REDISPORT}\.conf\"#CONF=\"/etc/redis/conf/\${REDISPORT}\.conf\"#" /etc/init.d/redis_7005
sed -i -e "s/6379/7006/" -e "s#CONF=\"/etc/redis/\${REDISPORT}\.conf\"#CONF=\"/etc/redis/conf/\${REDISPORT}\.conf\"#" /etc/init.d/redis_7006

update-rc.d redis_7001 defaults
update-rc.d redis_7002 defaults
update-rc.d redis_7003 defaults
update-rc.d redis_7004 defaults
update-rc.d redis_7005 defaults
update-rc.d redis_7006 defaults
        @echo "start..."
/etc/init.d/redis_7001 start
/etc/init.d/redis_7002 start
/etc/init.d/redis_7003 start
/etc/init.d/redis_7004 start
/etc/init.d/redis_7005 start
/etc/init.d/redis_7006 start


create_password:
sed -i -e "/^# masterauth/a masterauth passwd" -e "/^# requirepass/a requirepass passwd" /etc/redis/conf/7001.conf
sed -i -e "/^# masterauth/a masterauth passwd" -e "/^# requirepass/a requirepass passwd" /etc/redis/conf/7002.conf
sed -i -e "/^# masterauth/a masterauth passwd" -e "/^# requirepass/a requirepass passwd" /etc/redis/conf/7003.conf
sed -i -e "/^# masterauth/a masterauth passwd" -e "/^# requirepass/a requirepass passwd" /etc/redis/conf/7004.conf
sed -i -e "/^# masterauth/a masterauth passwd" -e "/^# requirepass/a requirepass passwd" /etc/redis/conf/7005.conf
sed -i -e "/^# masterauth/a masterauth passwd" -e "/^# requirepass/a requirepass passwd" /etc/redis/conf/7006.conf


create_cluster:
    redis-cli --cluster  create \
                127.0.0.1:7003 127.0.0.1:7001 127.0.0.1:7002 \
                127.0.0.1:7004 127.0.0.1:7005 127.0.0.1:7006 \
                --cluster-replicas 1  -a "passwd"
                
                
  -------------------------------------
  
  
  bind 0.0.0.0
protected-mode yes
port 6379
tcp-backlog 511
timeout 0
tcp-keepalive 300
daemonize yes
pidfile /var/run/redis_6379.pid
loglevel notice
logfile /home/redis/log/6379.log
databases 16
always-show-logo no
# set-proc-title yes
# proc-title-template "{title} {listen-addr} {server-mode}"
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
# rdb-del-sync-files no
dir /home/redis/data/6379
# masterauth
replica-serve-stale-data yes
replica-read-only yes
repl-diskless-sync no
repl-diskless-sync-delay 5
# repl-diskless-load disabled
repl-disable-tcp-nodelay no
replica-priority 100
# acllog-max-len 128
# requirepass
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no
# lazyfree-lazy-user-del no
# lazyfree-lazy-user-flush no
# oom-score-adj no
# oom-score-adj-values 0 200 800
# disable-thp yes
appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
aof-use-rdb-preamble yes
lua-time-limit 5000
cluster-enabled yes
cluster-config-file /home/redis/data/6379/nodes-6379.conf
cluster-node-timeout 15000
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0
notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-size -2
list-compress-depth 0
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
stream-node-max-bytes 4096
stream-node-max-entries 100
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
dynamic-hz yes
aof-rewrite-incremental-fsync yes
rdb-save-incremental-fsync yes
# jemalloc-bg-thread yes
  
