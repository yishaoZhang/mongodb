## projections ##
> by default queries in MongoDB return all fields in matching documents.
> the projection document limits the fields to return for all matching documents
> {field1:<value>, field2:<value>}
> <value> may be 0(or false) to exclude the field, or 1 (or true) to include it.
> the _id field is included automatically unless specifically excluded
```
collection
  .find({})
  .project({'name':1,'cuisine':1})
  .toArray(function(err, docs){})

// using the projection, the returned documents are as following:
// {name:1,cuisine:1,_id:1}

// to return all fields except the address field, use the following:
{address: 0}
```