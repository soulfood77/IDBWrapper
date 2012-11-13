About
=====

This is a wrapper for indexedDB. It is meant to

a) ease the use of indexedDB and abstract away the differences between the 
existing impls in Chrome and Firefox (yes, it works in both), and

b) show how IDB works. The code is split up into short methods, so that it's
easy to see what happens in what method.

"Showing how it works" is the main intention of this project. IndexedDB is 
all the buzz, but only a few people actually know how to use it. 

The code in IDBWrapper.js is not optimized for anything, nor minified or anything. 
It is meant to be read and easy to understand. So, please, go ahead and check out
the source!

A tutorial with some more details can be found here:
http://jensarps.de/2011/11/25/working-with-idbwrapper-part-1/

##November Rewrite

I rewrote IDBWrapper to cope with all the issues, and the new version is on
master since Nov, 13th 2012. The API didn't change much, I just removed some
of the methods. Method signatures remain unchanged.

However, if you have a previous version of IDBWrapper in use, there's an
issue: The new version won't be able to access the store created with the old
version, because database names changed. In that case, you need to manually
migrate the data: Include both versions of IDBWrapper (use a different name for
them), do a getAll() on the old store and write the data to the new store.

I am very sorry about any inconveniences, but there was no other way.


Examples
========

There are some examples to run right in your browser over here: http://jensarps.github.com/IDBWrapper/example/

The source for these examples are in the `example` folder of this repository.

Usage
=====

Including the IDBStore.js file will add an IDBStore contructor to the global scope.

Alternatively, you can use an AMD loader such as RequireJS to load the file, 
and you will recieve the constructor in your load callback (the constructor 
will then, of course, have whatever name you call it).

You can then create an IDB store:

```javascript
var myStore = new IDBStore();
```

You may pass two parameters to the constructor: the first is an object with optional parameters,
the second is a function reference to a function that is called when the store is ready to use.

The options object may contain the following properties (default values are shown):

```javascript
{
  storeName: 'Store',
  dbVersion: 1,
  keyPath: 'id',
  autoIncrement: true,
  onStoreReady: function(){}
}
```

'keyPath' is the name of the property to be used as key index. If 'autoIncrement' is set to true, 
the database will automatically add a unique key to the keyPath index when storing objects missing 
that property.

You can also pass a callback function to the options object. If a callback is provided both as second 
parameter and inside of the options object, the function passed as second parameter will be used.

Methods
=======

Here's an overview of available methods in IDBStore:

Data Manipulation
-----------------

Use the following methods to read and write data:

___

1) The put method.


```javascript
put(/*Object*/ dataObj, /*Function?*/onSuccess, /*Function?*/onError)
```

`dataObj` is the Object to store. `onSuccess` will be called when the insertion/update was successful, 
and it will recieve the keyPath value (the id, so to say) of the inserted object as first and only 
argument. `onError` will be called if the insertion/update failed and it will recieve the error event 
object as first and only argument. If the store already contains an object with the given keyPath id,
it will be overwritten by `dataObj`.

___

2) The get method.

```javascript
get(/*keyPath value*/ key, /*Function?*/onSuccess, /*Function?*/onError)
```

`key` is the keyPath property value (the id) of the object to retrieve. `onSuccess` will be called if
the get operation was successful, and it will receive the stored object as first and only argument. If
no object was found with the given keyPath value, this argument will be null. `onError` will be called
if the get operation failed and it will recieve the error event object as first and only argument.

___

3) The getAll method.

```javascript
getAll: function(/*Function?*/onSuccess, /*Function?*/onError)
```

`onSuccess` will be called if the getAll operation was successful, and it will receive an Array of
all objects currently stored in the store as first and only argument. `onError` will be called if 
the getAll operation failed and it will recieve the error event object as first and only argument.

___

4) The remove method.

```javascript
remove: function(/*keyPath value*/ key, /*Function?*/onSuccess, /*Function?*/onError)
```

`key` is the keyPath property value (the id) of the object to remove. `onSuccess` will be called if
the remove operation was successful, and it _should_ receive `false` as first and only argument if the
object to remove was not found, and `true` if it was found and removed.

NOTE: FF 8 will pass the key to the onSuccess handler, no matter if there is an corresponding object
or not. Chrome 15 will pass `null` if removal wass successful, and call the error handler if the object
wasn't found. Chrome 17 will behave as described above.

`onError` will be called if the remove operation failed and it will recieve the error event object as first 
and only argument.

___

5) The clear method.

```javascript
clear: function(/*Function?*/onSuccess, /*Function?*/onError)
```

`onSuccess` will be called if the clear operation was successful. `onError` will be called if the clear 
operation failed and it will recieve the error event object as first and only argument.


Index Operations
----------------

Use the following methods to create, retrieve and delete indices:

___


2) The hasIndex method.

```javascript
hasIndex: function(/*String*/ indexName)
```

Return true if an index with the given name exists in the store, false if not.

___

4) The getIndex method.

```javascript
getIndex: function(/*String*/indexName)
```

Will return the index with the given name. You can then open open cursors on the index or call the index' get
method. Kepp in mind though, that IDB will throww if there is no index with the given name, so make sure to
check if it exists if you are not sure.

___

5) The getIndexList method.

```javascript
getIndexList: function()
```

Returns a `DOMStringList` with all existing indices.


