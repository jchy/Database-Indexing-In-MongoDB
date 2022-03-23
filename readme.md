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

### part 2
- 1. do a compound index
```js
    // <!-- Compund indexing over beds, bedrooms amd accommodates fileds in airbnb database -->
    db.airbnb.createIndex({"beds":1, "bedrooms":1,"accommodates":-1});
```
- 2. explain pros and cons of compound index
```js
    1. Applications may encounter reduced performance during index builds, including limited read/write access to the collection. 
    2. Building indexes during time periods where the target collection is under heavy write load can result in reduced write performance and longer index builds.
    3. Insufficient Available System Memory (RAM)
    You can override the memory limit by setting the maxIndexBuildMemoryUsageMegabytes server parameter. Setting a higher memory limit may result in faster completion of index builds. However, setting this limit too high relative to the unused RAM on your system can result in memory exhaustion and server shutdown.
```
- 3. explain prefix requirements for compound index 
```js
    Index prefixes are the beginning subsets of indexed fields. For example, consider the following compound index:

    { "beds": 1, "bedrooms": 1, "accommodates": 1 }

    The index has the following index prefixes:

    { beds: 1 }
    { beds: 1, bedrooms: 1 }
    For a compound index, MongoDB can use the index to support queries on the index prefixes. As such, MongoDB can use the index for queries on the following fields:

    the  field,
    the beds field and the bedrooms field,
    the beds field and the bedrooms field and the accommodates field.

    MongoDB cannot use the index to support queries that include the following fields since without the beds field, none of the listed fields correspond to a prefix index:

    If you have a collection that has both a compound index and an index on its prefix (e.g. { a: 1, b: 1 } and { a: 1 }), if neither index has a sparse or unique constraint, then you can remove the index on the prefix (e.g. { a: 1 }). MongoDB will use the compound index in all of the situations that it would have used the prefix index.
```
- 4. when does compound index not work?
```js
    In MongoDB, the compound index can contain a single hashed index field, if a field contains more than one hashed index field then MongoDB will give an error.
```
- 5. apply compound index on 3 fields that are commonly searched in airbnb, and query those fields and sort them to see explain
```js
    db.airbnb.find().sort({beds:1, bedrooms:-1, accommodates:1}).explain("executionStats");
```

