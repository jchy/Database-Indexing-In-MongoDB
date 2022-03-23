# Indexing in mongoDB database
### Problem.1
- 1. use the airbnb listing collection
- 2. index the price field
- 3. note down the time difference between the queries.
- 4. try queries where you check for a range of values ( gte, lte, in etc )
- 5. do an index on a nested sub document (like location etc) and do queries on that as well
- 6. do queries on two indexes and see when rejected plans happen

### Solutions.1 
- 1. downloaded airbnb.json from airbnb site
- 2. index the price filed using the following command 
```js
    db.collection_name.createIndex({"price":1});
```
- 3. Here time difference between the quer1 and query2 is significant i.e. 11ms
- 4. try queries where you check for a range of values ( gte, lte, in etc )
```js
    // query.1  BEFORE INDEXING
    db.airbnb.find({price : {$lte : 50}}).explain("executionStats");
    // NOTE : Here "airbnb" is a collection inside airbnb database
    // Result of this query without indexing the price filed is:~
    // executionSuccess: true,
    // nReturned: 868,
    // executionTimeMillis: 25,
    // totalKeysExamined: 0,
    // totalDocsExamined: 5555

    // query.2 AFTER INDEXING
    db.airbnb.find({price : {$lte : 50}}).explain("executionStats");
    // NOTE : Here "airbnb" is a collection inside airbnb database
    // Result of this query with indexing the price filed is:~
    // executionSuccess: true,
    // nReturned: 868,
    // executionTimeMillis: 14,
    // totalKeysExamined: 868,
    // totalDocsExamined: 868
```
- 5. do an index on a nested sub document (like country_code etc) and do queries on that as well
```js
    db.airbnb.createIndex({"address.country_code":1});
```
- 6. do queries on two indexes and see when rejected plans happen
```js
    db.airbnb.find({"address.country_code":"BR"}).explain("executionStats");
    // nReturned: 606,
    // executionTimeMillis: 4,
    // totalKeysExamined: 606,
    // totalDocsExamined: 606,
    // rejectedPlans: []
```

