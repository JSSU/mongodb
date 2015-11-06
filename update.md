`简写t`
+ 简写替换:如果db=test, collection=profile, 可以简写t=db.profile, 以后t就可以代替db.profile了
+ mongoimport
```bash
> mongoimport -d DBName -c CollectionName FileName
```
+ bool 不用加引号(EX: {"registered":false})

#Insert
db.<collection>.insert(<>)

#Full update
```
db.<collection>.update(<where>,<full or patial update expression>[,<upsert>,<multi>])
```

## mongo shell update use javascript
myobj=t.findOne() `存现有值到一个myobj变量里, 注意一定要用findOne()`
+ t.update({_id : myobj._id}, myobj)
+ t.save(myobj)

#Patial update（BSON Size limit 16MB/Doc）
$set --db.collection.update({WHERE},{$set:{"NEWINSERT":"NEWVALUE}})
mydoc={'$set':{'INSERT':100}}
t.update({_id:myobj._id},mydoc)

$push --db.collection.update({WHERE},{$push:{"ARRAY_NAME":"ARRAY_VALUE}}) 
push to an array if not(Last position), create one
$addToSet --insert if not present

#删除
##$Remove/Delete --db.<collectionName>.remove(<where(multi)>)-- <where(multi)>={...}
db.collection.remove({}) --remove all

#multi update default is false 
db.<collection>.update(<where>,<full or patial update expression>,<multi>) --<multi>default false

#upsert
db.<collection>.update(<where>,<full or patial update expression>,<upsert>)

#Command.  
BSON wire protocal
Query, Insert, Update, Remove, GetMore
getLastError,isMaster,drop, create compact--db.runCommand({})
ensureIndex, dropIndex, currentOp, killOp --help, db.help()
+ db.serverStatus()
+ db.cuurentOp()
```
db.currentOp()
(New Window)mongo localhost/test
(New Window)while(1){db.teest3.insert({ts:new Date()}); sleep(5);}
db.currentOp()就有东西了
```
[Q]If you’re looking for problems with database performance, what is a good place to look, when you run db.currentOp()?
[A]“secs_running”: (look for long times)

+ collection.stats()

+ collection.drop() 把所有东西drop掉-- db.collection.remove({ })
```
{
	Server:[isMaster, serverStatus, logout]
 	db:[dropDatabase, repairDatabase, clone, copydb, dbStats]
 	collection:{dba:[create(implicit), drop, collStats, renameCollection], 
 				user:[count,aggregate, mapReduce, FindAndModify, geo]}
 	index:[ensureIndex, dropIndex]
}
```
#Bulk operations and methods
```
var bulk= db.items.initializeUnorderedBulkOp();
bulk.insert({BULABULABULA})
...
bulk.execute()
```

```
var b= db.items.initializeOrderedBulkOp();
b.find({item:"abc123"}).remove()
b.find({item:"abc123"}).update({$inc:{points:1}})
...
bulk.execute()
```

//---------Quiz Note----------
In the shell, add that this user likes "football". You should not need to pass "tennis" or "golf" to the update query at all.
db.users.update({},{$addToSet:{likes:"football"}},true,true)

+ HW2--
b = db.products_bak; db.products.find().forEach( function(o){ b.insert(o) } )
 // check it worked: 
b.count()
// should print 11
homework.a()

+ > db.products.find({_id:ObjectId("507d95d5719dbef170f15c00")})
db.products.update({_id:ObjectId("507d95d5719dbef170f15c00")},{$inc:{term_years:1}})
db.products.update({_id:ObjectId("507d95d5719dbef170f15c00")},{$inc:{"limits.sms.over_rate":0.01}})

+ How many products have a voice limit? (That is, have a voice field present in the limits subdocument.)
db.products.find({"limits.voice":{$exists:true}}).count()






