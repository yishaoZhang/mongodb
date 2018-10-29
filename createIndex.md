## basic ##
+ 用于搜集排序提速
+ 索引是通过b-tree索引来实现的
+ 命令行中使用ensureIndex操作
  + db.things.ensureIndex({j:1})
+ 默认索引
  + 除capped集合(限行限容量集合)，默认会在_id字段创建索引，该索引不被删除，强制唯一，由数据库维护。
+ 文档做为索引
  + db.factories.insert({name:'zhang'},metro:{city:'shanghai',state:'NY'});
  + db.factories.ensuerIndex({metro:1});
    + db.factories.find({metro:{city:"shange",state:'NY'}}) //使用索引查询
    + db.factories.find({metro:{$gte:{city:'shange'}}}) // 能够使用索引查询
  + 创建文档索引的一个替代方法是创建复合索引
    + db.factories.ensureIndex({"metro.city":1,"metro.state":1})
      + db.factories.find().sort("metro.city":1,"metro.state":1);
+ 稀疏索引
  + 缺失该索引字段时间，文档记录可能不返回
    + db.people.ensureIndex({title:1},{sparse:true})
      + sparse:true 在title字段建稀疏索引
    + db.people.insert({name:'zhang'})
    + db.people.insert({name:'zhang', title: 'vp'})
    + db.people.find() // 显示两条数据
    + db.people.find().sort({title:1}) //自有包含有索引字段的记录才被返回
+ 唯一索引
  + 当一个文档以唯一索引的方式保存到集合中，任何缺失的索引字段都会以null代替，因此不允许在
  唯一索引字段，插入两次为空document
    + db.things.ensureIndex({firstname:1,lastname:1},{unique:true})
    + db.things.save({lastname:'smith'}) // 此时firstname:null
    + db.things.save({lastname:'jone'}) // 报错，不允许两个为null的firstname存在
  + 重复值
    + 顾名思义，唯索引其值不允许重复
      + 如果一定要在有重复值的字段上创建，唯一索引，加dropDups字段，系统将保留第一条记录，其余都将被删除
        + db.things.ensureIndex({firstname:1},{unique:true,dropDups:true})
+ 补充说明
  + 文档field是有先顺序的{name:'zhang',age:18} 与 {age:18,name:'zhang'}是不同的
  + 索引大小写敏感
  + 索引性能
    + 索引主要是查询服务的
    + 索引对于删除与写入有额外开销
  + 查询结果比较少（小于4M），sort可以不用索引
  + 索引存储
    + mysql
      + mysql索引存储在硬盘中，需要时会调用部分到内存中
    + mongodb
      + 索引则是直接存储在内存和临时文件中，并且和内存大小限制直接相关。如果超过内存限制则从硬盘加载索引
        + mongodb索引的使用，在大数据面前，会面临内存耗尽的风险

  ## create index ##
  + create an ascending index
    + collection.createIndex({dateofBirth:1},function(err,result){})
  + create a descending index
    + collection.createIndex({lastname:-1},function(err,result){})
  + create a compound index(复合)
    + collection.createIndex({lastname:1,dateofBirth:-1},function(err,result){})
  + create a text index
    + collection.createIndex({comments:'text'},function(err,result){})
      + it's valuse is string or an array of string elements
  + create a hashed index
    + collection.createIndex({timestamp:"hashed"},function(err,result){})
  + create geospatial indexes???
  + IndexOptions
    + create a partial index
      + collection.createIndex({lastname:1,firstname:1},{partialFilterExpression:{points:{$gt:5}}})
    + TTL indexes
    + Unique Indexes
    + Partial Indexes
    + Case Insensitive Indexes
    + Sqarse Indexes