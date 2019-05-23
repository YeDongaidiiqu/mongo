## 配置主机
1.配置文件
>
IP: 127.0.0.1
port: 27013
>
IP: 127.0.0.1
port: 27014
>
IP: 127.0.0.1
port: 27015


配置文件如下
```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo_27013.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb_27013
net:
  port: 27013
  bindIpAll: true
processManagement:
   fork: true
replication:
  replSetName: BigBoss
sharding:
  clusterRole: configsvr
  
  
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo_27014.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb_27014
net:
  port: 27014
  bindIpAll: true
processManagement:
   fork: true
replication:
  replSetName: BigBoss  
sharding:
  clusterRole: configsvr
  
 
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo_27015.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb_27015
net:
  port: 27015
  bindIpAll: true
processManagement:
   fork: true
replication:
  replSetName: BigBoss
sharding:
  clusterRole: configsvr   
  
三个配置节点本身是个复制集集群，集群数量必须是奇数   
```
2. 启动
```
mongod -f /etc/mongo/configsvc1.conf
mongod -f /etc/mongo/configsvc2.conf
mongod -f /etc/mongo/configsvc3.conf
```
3. 初始化集群
```
mongo --port 27013  //连接mongo
rs.initiate(
{
  _id: "BigBoss",
  version: 1,
  protocolVersion: 1,
  writeConcernMajorityJournalDefault: true,
  configsvr: true,
  members: [
    {
      _id: 0,
      host: "192.168.1.4:27013",
      arbiterOnly: false,
      buildIndexes: true,
      hidden: false,
      priority: 66,
      tags: {
        BigBoss: "YES"
      },
      slaveDelay: 0,
      votes: 1
    },
    {
      _id: 1,
      host: "192.168.1.4:27014",
      arbiterOnly: false,
      buildIndexes: true,
      hidden: false,
      priority: 55,
      tags: {
        BigBoss: "NO"
      },
      slaveDelay: 0,
      votes: 1
    },
    {
      _id: 2,
      host: "192.168.1.4:27015",
      arbiterOnly: false,
      buildIndexes: true,
      hidden: false,
      priority: 33,
      tags: {
        BigBoss: "NO"
      },
      slaveDelay: 0,
      votes: 1
    }
  ],
  settings: {
    chainingAllowed : true,
  }
}
)

//查看集群状态
rs.status()
```



## 分片主机
share1:
IP: 127.0.0.1
port: 27018
>
IP: 127.0.0.1
port: 27019
```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo_27018.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb_27018
net:
  port: 27018
  bindIpAll: true
processManagement:
   fork: true
replication:
  replSetName: shard1
sharding:
  clusterRole: shardsvr
  
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo_27019.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb_27019
net:
  port: 27019
  bindIpAll: true
processManagement:
   fork: true
replication:
  replSetName: shard1
sharding:
  clusterRole: shardsvr
  
两台主机组成复制集   


初始化复制集
rs.initiate(
{
  _id: "shard1",
  version: 1,
  protocolVersion: 1,
  writeConcernMajorityJournalDefault: true,
  members: [
    {
      _id: 0,
      host: "192.168.1.1:27018",
      arbiterOnly: false,
      buildIndexes: true,
      hidden: false,
      priority: 66,
      tags: {
        BigBoss: "YES"
      },
      slaveDelay: 0,
      votes: 1
    },
    {
      _id: 1,
      host: "192.168.1.1:27019",
      arbiterOnly: false,
      buildIndexes: true,
      hidden: false,
      priority: 55,
      tags: {
        BigBoss: "NO"
      },
      slaveDelay: 0,
      votes: 1
    }
  ],
  settings: {
    chainingAllowed : true,
  }
}
)

#查看副本集状态
rs.status() 
```
share2:
IP: 127.0.0.1
port: 27019
```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo_27020.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb_27020
net:
  port: 27020
  bindIpAll: true
processManagement:
   fork: true
sharding:
  clusterRole: shardsvr
  
就是单个节点  
```
2. 启动mongo
mongod --config /usr/local/mongo_27018.conf
mongod --config /usr/local/mongo_27019.conf



## 路由主机
IP: 127.0.0.1
port: 27014
配置文件如下
```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo_27016.log
  logAppend: true
net:
  bindIp: 127.0.0.1
  port: 27016
processManagement:
   fork: true
sharding:
   configDB: BigBoss/192.168.1.240:27013,192.168.1.240:27014,192.168.1.240:27015
   
启动连接就可以正常使用   
```

