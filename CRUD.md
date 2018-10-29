## CRUD ##
+ C
  + 增加 Create
+ R
  + 读取 Retrieve
+ U
  + 更新 update
+ D
  + 删除 Delete

## Insert Documents ##
> es2017
> collection 不存在，将自动被创建
```
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');
const url = 'mongdb://localhost:27017';
const dbname = 'myproject';
const client = new MongoClient(url);
(async function(){
  try {
    await client.connect();
    console.log('connected correctly to server');
    const db = client.db(dbname);
    let r = await db.collection('inserts').insertOne({a:1});
    assert.equal(1, r.insertedCount);
    let re = await db.collection('inserts').insertMany([{a:2},{a:3}]);
    assert.equal(2, re.insertedCount)
    client.close();
  } catch(err) {
    console.log(err.stack);
  }
})();
```
+ insert 参数
  + w
    + <1 只表示请求数量acknowledgment, 没有结果/result？
    + >=1 || 'majority' 得到最终全部写入的数量？
  + wtimeout
    + 写入时间限制 
  + j
    + default:false
    + 是否同步写日志(journal)
  + serializeFunctions
    + default: false
    + 序列化文档？
  + forceServerObjectId
    + Force server to assign _id values instead of driver.

## updating documents ##
```
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');
const url = 'mongodb://localhost:27017';
const dbName = 'myproject';
const client = new MongoClient(url);
(async function() {
  try {
    await client.connect();
    console.log("connected correctly to server");
    const db = client.db(dbName);
    const col = db.collection('updates');
    const r = await col.insertMany([{a:1}, {a:2}, {a:2}]);
    assert.equal(3, r.insertedCount);

    // update a single document
    const updateSingleRe = await col.updateOne({a:1}, {$set:{b:1}});
    assert.equal(1, updateSingleRe.matchedCount);
    assert.equal(1, updateSingleRe.modifiedCount);

    // update multiple document
    const updateMultipleRe = await col.updateMany({a:2},{$set:{b:1}});
    assert.equal(2, updateMultipleRe.matchedCount);
    assert.equal(2, updateMultipleRe.modifiedCount);

    // upsert a single document
    const updateUpsertRe = await col.updateOne({a:3},{$set:{b:1}},{
      upsert:true // 有查询结果便更新，无则执行插入操作
    })
    assert.equal(0, updateUpsertRe.matchedCount);
    assert.equal(1, updateUpsertRe.upsertedCount);

    // close collection
    client.close();
  } catch (err) {
    console.log(err.stack);
  }
})()
```
+ update 参数
  + w 同insert
  + wtimeout
    + 同 insert
  + j
    + write waits for journal sync
      + default: false
  + multi
    + update one/all documents with operation
      + default: false
  + upsert
    + update operation is an upsert
    + if find update, else insert
      + default: false
  
## removing document ##
```
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');
const url = 'mongodb://localhost:27017';
const dbName = 'myproject';
const client = new MongoClient(url);
(async function() {
  try {
    await client.connect();
    const db = client.db(dbName);
    const col = db.collection('removes');
    // insert multi documents
    let r = await col.insertMany([{a:1},{a:2},{a:2}]);
    assert.equal(3, r.deletedCount);

    // remove a single document
    let delSingleRe =  col.deleteOne({a:1});
    assert.equal(1, delSingleRe.deleteCount);

    // remove multiple documents
    let delMultipleRe = col.deleteMany({a:2});
    assert.equal(2, delMultipleRe.deleteCount);

    // close connection
    client.close();
  } catch (err) {
    console.log(err.stack)
  }
})()
```
+ remove document 参数
  + w
  + wtimeout
  + j
  + single
    + remove the first document found


## findOneAndUpdate, findOneAndDelete, findOneAndReplace ##
> when useing these methods, the operation takes a write lock for the duration of the operation in order to ensure the modification is atomic.
+ findOneAndUpdate
  + params
    + w
    + wtimeout
    + j
    + upsert
    + sort
      + sort for find operation
      + Object, default:null
    + projection  
      + projection for returned result
      + (Object, default: null)
    + returnOriginal
      + if you want to return the modified object rather than the original
    + projection returnOriginal两者配合，可以得到，想要的数据结构

    ## bulkwrite ##
    > the bulkwrite function allows a simple set of bulk operations to run in a non-fluent wayd.
    > the bullwrite function takes an array of operations which can be objects of either insertOne, updateOne, updateMany, deleteOne, deleteMany, or replaceOne. It alse takes a second parameter
    + w
    + j
    + wtimeout
    ```
    const MongoClient = require('mongodb').MongoClient;
    const assert = require('assert');
    const url = 'mongodb://localhost:27017';
    const dbName = 'myproject';
    const client = new MongoClient(url);
    (function(){
      try {
        await client.connect();
        console.log('connected correctly to server');
        const db = client.db(dbName);
        // get collection
        const col = db.collection('bulk_write');
        const r = await col.bulkWrite([
          {insertOne: {document: {a:1}}},
          {updateOne:{filter:{a:2},update:{$set:{a:2}}, upsert:true}},
          {updateMany:{filter:{a:2},update:{$set:{a:2}}, upsert:true}},
          {deleteOne:{filter:{c:1}}},
          {deleteMany:{filter:{c:1}}},
          {replaceOne:{filter:{3}, replacement:{c:4},upsert:true}}
        ], {ordered:true,w:1})
      } catch(err) {
        console.log(err.stack);
      }
    })();
    ```