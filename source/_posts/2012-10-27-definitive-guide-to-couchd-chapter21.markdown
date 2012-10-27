---
layout: post
title: Notes of Definitive guide to couchdb - CHAPTER 21 View Cookbook for SQL Jockeys
date: 2012-10-27 21:41
comments: true
categories: couchdb nosql view map reduce
---

##Defining a View

Defining a view is done by creating a special document in a CouchDB database. The only real specialness is the `_id` of the document, which starts with `_design/` —for example, `_design/application`. Other than that, it is just a regular CouchDB document.

##Querying a View

/database/_design/application/_view/viewname


##Map Function
A map function may call the built-in `emit(key, value)` function 0 to N  times per document, creating a row in the map result per invocation.

(a map function can return any counts of emit(key, value) call)

##Look up by key

To look something up quickly, regardless of the storage mechanism, an index is needed.  An index is a data structure optimized for quick search and retrieval. CouchDB’s map result is stored in such an index, which happens to be a B+ tree.

(couchdb will generate a file with name xxxx.view to store the result of map/reduce function, it will copy the desired data from documents to the view file, not just reference it, so a view file may be very large)

Query: `/ladies/_design/ladies/_view/age?key=5`

Result:
``` javascript
{
    "total_rows":3,
        "offset":1,
        "rows":[
        {"id":"fc2636bf50556346f1ce46b4bc01fe30","key":5,"value":"Lena"}
    ]
}
```

`total_rows`: how many documents are there in this view result  
`offset`: position/index of this document/the first document in this view result


##Aggregation Function

* Reduce functions are similar to aggregate functions in SQL. They compute a value over multiple documents.
* Reduce functions operate on the output of the map function (also called the map result  or intermediate result ).
* This reduce function takes two arguments: a list of keys  and a list of values .
* You’ll see one difference between the map and the reduce function. The map function uses emit() to create its result, whereas the reduce function returns a value.

The result for a reduce view may looks like this:
``` javascript
{
    "rows":[
    {"key":null,"value":15}
    ]
}
```


##Get Unique Values

The map result will look like this:
``` javascript
{"total_rows":9,"offset":0,"rows":[
    {"id":"3525ab874bc4965fa3cda7c549e92d30","key":"bike","value":null},
        {"id":"3525ab874bc4965fa3cda7c549e92d30","key":"couchdb","value":null},
        {"id":"53f82b1f0ff49a08ac79a9dff41d7860","key":"couchdb","value":null},
        {"id":"da5ea89448a4506925823f4d985aabbd","key":"couchdb","value":null},
        {"id":"3525ab874bc4965fa3cda7c549e92d30","key":"drums","value":null},
        {"id":"53f82b1f0ff49a08ac79a9dff41d7860","key":"hypertext","value":null},
        {"id":"da5ea89448a4506925823f4d985aabbd","key":"music","value":null},
        {"id":"da5ea89448a4506925823f4d985aabbd","key":"mustache","value":null},
        {"id":"53f82b1f0ff49a08ac79a9dff41d7860","key":"philosophy","value":null}
    ]
}
```

* Couchdb doesn’t have a keyword like `distinct`, but we can use `group` query parameter to group the result of reduce function, as we mentioned in last section, a reduce function only returns one value, but when we add `group=true` query parameter, multi values will be returned(each value is the reduce result of according group), grouped by the key in keys list, in this way, we can get the distinct keys(in this case, distinct tags)
* Coucdb reduce result by default discard keys, so in the reduce result, key always be null, but if we add group=true in query, key will be used to grouping, so key will be reserved.
* `group=true` parameter can only be used on a view which has reduce function.
* if a view has a reduce function, by default a query will return the result of reduce. we can explicit pass `reduce=false` to get map result instead of reduce result.
