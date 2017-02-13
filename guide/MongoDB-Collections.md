# MongoDB Collections
Once your connection and database are defined, you will be working with a collection to create, update, delete, and query documents within that collection.

A collection can be defined either by "cascading" through defining the connection client, the database then the collection:

``` swift
let client = try! MongoClient(uri: "mongodb://localhost")
let db = client.getDatabase(name: "test")
let collection = db.getCollection(name: "testcollection")
```
Or by assigning directly after opening the connection:

``` swift
let client = try! MongoClient(uri: "mongodb://localhost")
let collection = MongoCollection(
	client: client, 
	databaseName: "test", 
	collectionName: "testcollection"
	)
```
### Closing Connections
Remember to set up your connections to close:

``` swift
defer {
    collection.close()
    db.close() 			// if created by "cascade"
    client.close()
}
```

### Collection Name

To return the collection name as a string:

``` swift
collection.name()
```

### Rename Collection

To rename the collection using newDbName and newCollectionName, with an option to drop any existing collection immediately instead of after the move:

``` swift
collection.rename(
	newDbName: <String>, 
	newCollectionName: <String>,
	dropExisting: <Bool>
	)
```

#### Parameters
* **newDbName:** String name for db after move
* **newCollectionName:** String name for collection after move
* **dropExisting:** Bool option to drop any existing collection immediately instead of after move

The returned value is the status of the renaming action.

### Drop Collection

`.drop` removes a collection from the database. The method also removes any indexes associated with the dropped collection.

``` swift
collection.drop()
```

The returned value is the status of the drop action.

### Inserting a Document into a Collection

Insert document into the current collection returning a result status:

``` swift
collection.insert(_ 
	document: <BSON>,
	flag: <MongoInsertFlag>
	)
```
#### Parameters
* **document:** BSON document to be inserted
* **flag:** Optional MongoInsertFlag defaults to .None

The returned value is the status of the insert action.

#### MongoInsertFlag Options

The MongoInsertFlag enum has the following options:

* **None:** No additional action is to be taken. This is the default option
* **ContinueOnError:** Instruct MongoDB to ignore errors
* **NoValidate:** Ignore validation process

### Updating a Document
To update a document in the collection, assemble the BSON object to replace the existing document, and the selector. 

``` swift
collection.update( 
	update: <BSON>,
	selector: <BSON>,
	flag: <MongoUpdateFlag>
	)
```
#### Parameters
* **update:** BSON document to replace the existing document
* **selector:** BSON document with selection criteria
* **flag:** Optional MongoUpdateFlag defaults to .None

The returned value is the status of the update action.

#### MongoUpdateFlag options

The MongoUpdateFlag enum has the following options:

* **None:** No additional action is to be taken. This is the default option
* **Upsert:** Insert, or if selector matches a record, update
* **MultiUpdate:** Update more than one document id matched by the selector
* **NoValidate:** Ignore validation process

### Save

Updates an existing document or inserts a new document, depending on its document parameter. 

``` swift
collection.save(document: <BSON>)
```

#### Parameters
* **document:** BSON document to save

The returned value is the status of the save action.

* If the document does not contain an `_id` field, a new document will be created
* If an `_id` is specified, `save` will perform an "upsert": If a matching `_id` is found in the collection an update will occur, otherwise an insert will be performed

``` swift
let bson = BSON()
defer {
	bson.close()
}
	
bson.append(key: "stringKey", string: "String Value")
bson.append(key: "intKey", int: 42)
	
let result2 = collection.save(document: bson)

```

### Find

Selects documents in a collection and returns a cursor to the selected documents.

``` swift
collection.find( 
	query: <BSON>,
	fields: <BSON>, 
	flags: <MongoQueryFlag>, 
	skip: <Int>, 
	limit: <Int>, 
	batchSize: <Int>
	)
```
#### Parameters
* **query:** *(Optional)* Specifies selection filter using query operators. To return all documents in a collection, omit this parameter or pass an empty document ({})
* **fields:** *(Optional)* Specifies the fields to return in the documents that match the query filter. To return all fields in the matching documents, omit this parameter
* **flags:** *(Optional)*  Set queryFlags for the current search
* **skip:** *(Optional)*  Skip the supplied number of records
* **limit:** *(Optional)*  Return no more than the supplied number of records
* **batchSize:** *(Optional)*  Change number of automatically iterated documents

#### Return Value
A cursor to the documents that match the query criteria. When the find() method "returns documents" the method is actually returning a cursor to the documents.

### Find and Modify

Modifies and returns a single document. By default, the returned document does not include the modifications made on the update. To return the document with the modifications made on the update, use the **new** option.

``` swift
collection.findAndModify(
	query: <BSON>, 
	sort: <BSON>, 
	update: <BSON>, 
	fields: <BSON>, 
	remove: <Bool>, 
	upsert: <Bool>, 
	new: <Bool>
	)
```
#### Parameters
* **query:** *Optional*. The selection criteria for the modification. The query field employs the same query selectors as used in the `db.collection.find()` method. Although the query may match multiple documents, `findAndModify()` will only select one document to modify
* **sort:** *Optional*. Determines which document the operation modifies if the query selects multiple documents. `findAndModify()` modifies the first document in the sort order specified by this argument
* **update:** Must specify either the remove or the update field. Performs an update of the selected document. The update field employs the same update operators or field: value specifications to modify the selected document
* **fields:** *Optional*. A subset of fields to return. The fields document specifies an inclusion of a field with 1, as in: `fields: { : 1, : 1, … }`
* **remove:** Must specify either the remove or the update field. Removes the document specified in the query field. Set this to true to remove the selected document. The default is false
* **upsert:** *Optional*. Used in conjunction with the update field. When true, `findAndModify()` creates a new document if no document matches the query, or if documents match the query, `findAndModify()` performs an update. To avoid multiple upserts, ensure that the query fields are uniquely indexed. The default is false
* **new:** *Optional*. When true, returns the modified document rather than the original. The `findAndModify()` method ignores the new option for remove operations. The default is false

### Count

Returns the count of documents that would match a `find()` query. The `count() `method does not perform the `find()` operation but instead counts and returns the number of results that match a query.

``` swift
collection.count(
	query: <BSON>, 
	fields: <BSON>, 
	flags: <MongoQueryFlag>, 
	skip: <Int>, 
	limit: <Int>, 
	batchSize: <Int>
	)
```

#### Parameters
* **query:** The query selection criteria
* **fields:** Optional. Specifies the fields to return in the documents that match the query filter. To return all fields in the matching documents, omit this parameter
* **flags:** Optional. Set queryFlags for the current search
* **skip:** Optional. Skip the supplied number of records
* **limit:** Optional. Returns no more than the supplied number of records
* **batchSize:** Optional. Change number of automatically iterated documents

#### Return Value
The count of documents that would match a `find()` query. The `count()` method does not perform the `find()` operation but instead counts and returns the number of results that match a query.

### Deleting
Remove the document found using selector returning a result status:

``` swift 
collection.remove(
	selector: <BSON>, 
	flag: <MongoRemoveFlag>
	)
```

#### Parameters
* **selector:** BSON document with selection criteria
* **flag:** *Optional* MongoRemoveFlag defaults to .None

### Creating an Index
Creates indexes on collections.

``` swift 
collection.createIndex(
	keys: <BSON>, 
	options: <MongoIndexOptions>
	)
```

#### Parameters
* **keys:** A document that contains the field and value pairs where the field is the index key and the value describes the type of index for that field. For an ascending index on a field, specify a value of 1; for descending index, specify a value of -1
* **options:** *Optional*. A document that contains a set of options that controls the creation of the index. See MongoIndexOptions for details

### Dropping an Index
Drops or removes the specified index from a collection.

``` swift
collection.dropIndex(name: <String>)
```

### Stats
Returns statistics about the collection formatted according to the options document.

``` swift
collection.stats(options: <BSON>)
```

The options document can contain the following fields and values:

* **scale:** *number, Optional*. The scale used in the output to display the sizes of items. By default, output displays sizes in bytes. To display kilobytes rather than bytes, specify a scale value of 1024

* **indexDetails:** *boolean, Optional*. If true, stats() returns index details in addition to the collection stats. Only works for WiredTiger storage engine. Defaults to false

* **indexDetailsKey:** *document, Optional*. If indexDetails is true, you can use indexDetailsKey to filter index details by specifying the index key specification. Only the index that exactly matches indexDetailsKey will be returned. If no match is found, indexDetails will display statistics for all indexes

* **indexDetailsName:** *string, Optional*. If indexDetails is true, you can use indexDetailsName to filter index details by specifying the index name. Only the index name that exactly matches indexDetailsName will be returned. If no match is found, indexDetails will display statistics for all indexes

### Validate

Validates a collection. The method scans a collection's data structures for correctness and returns a single document that describes the relationship between the logical collection and the physical representation of the data.

``` swift
collection.validate(full: <bool>)
```

The full parameter is optional. Specify true to enable a full validation and to return full statistics. MongoDB disables full validation by default because it is a potentially resource-intensive operation.

### GetLastError

`getLastError()` returns a BSON document with description of last transaction status.

``` swift
collection.getLastError()
```
