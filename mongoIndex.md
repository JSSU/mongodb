# Storage Engine: MMAPv1 and WiredTiger
## What is Storage Engine 
The storage engine handles the interface between the database and the hardware, and by hardware, we mean specifically memory and disk, and any representations of the data (or metadata) that is located there, which is to say the data and indexes.

+ The data file format - The data files' format is determined by the storage engine, so this choice is correct. Different storage engines can implement different types of compression, and different ways of storing the BSON for mongoDB.
+ Architecture of a cluster - We picked false here, but you might argue with this choice. The architecture of the cluster is determined by interactions between servers, which communicate over the wire. Since the storage engine doesn't affect the wire protocol, the server architecture could be exactly the same, which is why we went with false. Even so, one might argue that a good choice of storage engine should result in a smaller cluster to handle the same workload. If your gut was to go with true for this reason, that's a defensible choice.
+ The wire protocol for the drivers - False. The wire protocol determines how servers communicate with each other, or with the applications. This is clearly outside of the storage engine.
+ Format of indexes - True. It may not be obvious when you first think about it, but indexes are controlled by the storage engine. For instance, MongoDB uses Btrees. With MongoDB 3.0, WiredTiger will be using B+ trees, with other formats expected to come in later releases.

## MMAPv1
`Maps data files directly into virtual memory.`

### Locks
* Shared resources (multi-readers, single writer)
	+ Data
	+ Metadata
		+ Indexes
		+ Journal
* Journal
	+ write-ahead log
* Data on disk is BSON
	+ Bits are mapped from disk to virtual memory

# Power of Two Sized Allocations in MMAPv1 provides
+ Documents will not have to move as soon as they grow in size. - This is because you have some space to grow before you reach your record size.
+ Record spaces are likely to get re-used. - Without standardized sizes, the likelihood that your new document will want the same size of record space as an old document might be slim, especially if your documents have different sizes (which they will if they're all growing). With standard sizes, all documents are likely to find record spaces that fit them.
+ Documents that grow at a constant rate will move less often as time goes on. - This makes sense, if you think about it. If your documents start out at an average size of 20 bytes, and grow at a rate of 1 byte per day, your first move will happen in 12 days (since the smallest record size is 32 bytes). Your second move will take 32 days, and your third will take 64. Each successive move will take longer and longer.
+ Document movement need updates to indexes. 

## WiredTiger (plug-in)
Compression:Snappy(fast),zilib, none.
Document level locking
Improve performance

#Index
+ keys can be any type
+ _id index is automatic (unique)
+ other than _id, explicityl declared
+ automativally used
+ can index array contacts -"multikeys"
+ can index subdocument and subfields
+ sequence matters like sort()  ($eleMatch)
+ fieldnames are not in the index 
+ TTL : time to live {expireaFTERsECONDS: 3600}...
##Have:
```
db.collection.createIndex()     
db.collection.getIndexes()
db.collection.dropIndexes()
```
##properties:
+ "default"
+ "unique:true"
+ sparse -if an index is unique AND sparse, 2 documents which do not include the field that is indexed can exist in the same collection.

##Geospatial Indexes: 
`[$near][type:"Point"][spherical:true][$geoWithin,$geometry][type:"Polygon"][]`

loc:[x,y]
createIndexes: db.collections.createIndexes({loc : "2dsphere"})

##Text Indexes: [$text][$search : "words1 words2"] (or)
creatIndexes: db.collection.createIndexes({[name] : "text"})  --(multikey indexes, each words)
```
[EX] db.collection.find({$text : {$search : "[Trees cat]"}},{score:{$meta:"textScore", _id:0}})
	 +.sort({score:{$metaL "textScore"}})
```
##Explain
aggregate, find, count, remove, update(), group()
explain()

##Homework---{db:"pcat", collection:"products"}
+ find match in for, for contain "ac3"
db.products.find({for:{$in:["ac3"]}}).count()

+ use for as index
db.products.createIndex({for:1})
db.products.explain("allPlansExecution").find({for:{$in:["ac3"]}})

+ Which of the following are available in WiredTiger but not in MMAPv1? Check all that apply.
Document level locking
Data compression

















