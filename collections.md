## capped collections ##
+ capped collections have maximum size or document counts
+ mongodb remove older documents if a collection reaches the maximum size limit before it reached the maximum document count.
```
db.createCollection("mycollection", {capped:true,size:100000,max:5000}, function(err, resutl){})
```

## document validation ##
+ depending on the validationLevel and validationAction, mongodb either returns a warning, or refuses to insert or update the document if it fails to meet the specified criteria.
+ the following example creates a contact collection with a validator that specifies that insert or update document should match at least one of three following conditions
  + the phone field is string
  + the email field matches the regular expression
  + the status field either unkown or incomplete
```
db.createCollection('contacts', {
  'validator': {
    '$or': [
      {'phone': {'$type': 'string'}},
      {'email': {'$regex': /@mongodb\.com$}},
      {'status': {'$in': ["Unknown, "Incomplete"]}},
    ]
  }
}, function(err, results))
```