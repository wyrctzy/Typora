## Cassandra存储配置远程访问

使用非Docker的方式安装Cassandra数据库，作为JanusGraph的存储配置

**安装启动Cassandra**

下载Beta4.0

[Download Cassandra](:https://www.apache.org/dyn/closer.lua/cassandra/4.0-beta4/apache-cassandra-4.0-beta4-bin.tar.gz)

 janusGraph官方：

[安装文档](https://cassandra.apache.org/doc/latest/getting_started/installing.html)

 本次安装版本4.0-beta4,JAVA版本1.8.0_222,JanusGraph0.5.3

 解压： `tar -zxvf apache-cassandra-4.0-beta4-bin.tar.gz`

 `cd apache-cassandra-4.0-beta4`
 启动：`.bin/cassandra`

 查看日志： `tail -f logs/system.log`
 默认监听端口9042 ,只允许本地连接(listen 127.0.0.1)

> 远程访问Cassandra是通过其Cassandra 2.0的旧端口.在Cassandra 2.0.x中,默认的cqlsh监听端口是9160,由rpc_port参数在cassandra.yaml中定义.默认情况下,Cassandra 2.0.x及更早版本通过在`cassandra.yaml`中将start_rpc配置为true来启用Thrift.
>
> 在Cassandra 2.1中,cqlsh实用程序使用本机协议.在使用Datastax python驱动程序的Cassandra 2.1中,默认的cqlsh监听端口是9042.
>
> cassandra节点应绑定到服务器网卡的IP地址 – 它不应该是127.0.0.1或localhost,这是环回接口的IP,绑定到此将阻止直接远程访问.要配置绑定地址,请使用cassandra.yaml中的rpc_address参数.将其设置为0.0.0.0将侦听所有网络接口.

**配置**

`vim conf/cassadra.yaml`

打开远程访问

```yaml
...
rpc_address: 0.0.0.0
```
重新启动Cassandra
```
ps auwx | grep cassandra
kill -9 <pid>
.bin/cassandra
```
提示错误：
` Exception encountered during startup: If rpc_address is set to a wildcard address (0.0.0.0), then you must set broadcast_rpc_address to a value other than 0.0.0.0`，并根据提示设置`broadcast_rpc_address`为`1.2.3.4`

```yaml
...
broadcast_rpc_address: 1.2.3.4
```

再次尝试重新启动，测试连接

```
jdbc:cassandra://172.18.234.111:9042
```

连接成功。

![image-20210323173400297](../NoteImages/Cassandra存储配置远程访问.assets/image-20210323173400297.png)