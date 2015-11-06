Replication:
+ Hight available(failover)
+ data safety(durability)
    * extra cupies
    * "DR"(disaster recovery)
+ 'asynchronis' 不同步
+ single primary

# Replica Sets
+ automatic failover
    * 10secs for failover
    * driver 'replica set aware'
+ automatic node recovery 
+ ReadPrefference 从哪儿读

# Starting Replica Sets
+ ports dbpath
+ single machine
+ Replica Sets name
    * `mongod --port 27001 --replSet abc --dbpath /Users/webinar/dba/1 --logpath /Users/webinar/dba/log.1 --logappend --oploSize 50 --smallfiles --fork`
        `Why do we give replica sets names?`
        `Having a single name to refer to a set reduces confusion and human error.`
    * ps ax | grep mongo | grep abc
    * need initial

## initiating the set 
+ specify config
+ initial data
        `rs.initiate(--config--)`
Note:
+ don't use raw ip addresses
+ don't use names from /etc/hosts
+ use DNS
    * pick an appropriate TTL(eg. a few minutes)
```
mongo --port 27001
> cfg={
>       _id : "abc",
>       members : [
>        {_id:0, host:"10gen.local:27001"},
>        {_id:1, host:"10gen.local:27002"},
>        {_id:2, host:"10gen.local:27003"}
>       ]
>     }
```

```
> `cat /etc/hosts`
> `rs.help`
> rs.initiate(cfg)   //initial from cfg
> ls -la
> ls -la journal     
```

使用rs.initiate(cfg)会出现 abc:PRIMARY>
`rs.status()`可以查看信息
`rs.help`可以看可以用的function
`config`存在`db.system.replset`里面, 如果要改config要用`rs.reconfig()`
`db.isMaster()`可以看

## Reading and writing
`mongod 不让从secondary里面读，除非设置输入rs.slaveOk()`

## Failover
`用kill (id名字)` // do not use `-9`

## Read Preference -- "slaveOk"
`secondary 用rs.slaveOk()`
`有的时候读用secondary`因为：DNS就是读secondary的一个很好的例子
    + geography
    + seperate a workload(analytics server)
    + separate load
    + high availability

## Read Preference
+ primary(Default)
+ primary preferred
+ secondary
+ secondary preferred 
+ nearest


















