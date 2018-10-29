## Overview ##
> collations provide a set of rules which comply with the conventions of a particular language when comparing strings
> https://mongodb.github.io/node-mongodb-native/3.1/tutorials/collations/
+ assgin a default collation to a collection
```
const MongoClient = require('mongodb').MongoClient;
const url = 'mongodb://localhost:27017';
const dbName = 'myproject';
const client = new MongoClient(url);

client.connect(function(err, client) {
  const db = client.db(dbname);
  createCollated(db, function(){
    client.close();
  })
})

function createCollated(db, callback) {
  db.createCollection('collection-name', {
    'collation': {
      'locale': 'zh'
    }
  }, function(err, result){
    console.log('collection created!');
    callback();
  })
}
```
+ assign a default collation to an index
  + collection.createIndex({'name': 1}, {unique: 1}, {'collation':{local: "en_US"}}, function(err, result){})
  + use or not use
    + not use
      + collection.find({'city': 'shanghai'}, {'_id': 0})
          .toArray(function(err, docs) {})
    + use
      + collection.find({'city': 'shanghai'}, {'_id': 0}, {
        collation: {locate: 'en_US'}}
      }).toArray(function(err, docs) {})
  + find 第二参数，应为过滤
+ operations that support collation
  + find() sort()
    + collection.find({city:'shanghai'},{_id: 0},{collation:{locate:'en_US'}}).sort({name:1}).toArray(function(err,docs){})
  + findOneAndUpdate()
    + collection.findOneAndUpdate({first_name:{$lt:"Gunter"}}, {$set:{verified:true}}, function(err, docs))
  + findOneAndDelete()
    + collection.findOneAndDelete({a:{$gt:100}},{collation:{local:'en',numericOrdering:true}},function(err, result){})
+ aggragation/聚合
