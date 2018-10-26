## official turoials##
> https://mongodb.github.io/node-mongodb-native/3.1/tutorials/connect/

### connect to single mongodb instance ###
+ to single mongodb instance
```
const MongoClient = require('mongodb').MongoClient;
const url = 'mongodb://localhost:27017';
const dbName = 'myproject';
// create a new MongoClient
const client = new MongoClient(url);
client.connect(function(err){
  const db = client.db(dbName);
  // db do somthing
  client.close();
})
```

### connect to Replica Set ###
+ the same as single mongodb instance except url
  + const url = "mongodb://localhost:27017,localhost:27018/?replicaset=zhang"
    

### connect to sharded cluster ###
+ sharded cluster // 分片集群
+ the connection string specifies the mongos instances running on localhost:50000 and localhost:50001
+ the same as single mongodb instance except url
  + const url="mongodb://localhost:50000,localhost:50001"; 


### connection options ###
> https://docs.mongodb.com/manual/reference/connection-string/
+ specify tls/ssl and authentication setting
```
const MongoClient = require('mongodb').MongoClient;
const fs = require('fs');
// read the certificate authority
const ca = [fs.readFileSync(__dirname + "/ssl/ca.pem")];
const cert = fs.readFileSync(__dirname + "/ssl/client.pem");
// connection url
// 注意单双引号的使用
const url = 'mongodb://dave:password@localhost:27017?authMechanism=DEFAULT&authSource=db&ssl=true"';
const client = new MongoClient(url, {
  sslValidate: true,
  sslCA: ca,
  sslCert: cert
})
client.connect(function(err){
  const db = client.db(dbName);
  // db do somthing
  client.close();
})
```

