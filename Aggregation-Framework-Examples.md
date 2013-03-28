# Aggregation Framework Examples

This document provides a number of practical examples that display the
capabilities of the aggregation framework. All examples use a publicly
available data set of all zipcodes and populations in the United
States.

These data are available at: [zips.json](http://media.mongodb.org/zips.json).


## Requirements

[MongoDB](http://www.mongodb.org/downloads), version 2.4.1 or later.
[Ruby MongoDB Driver](https://github.com/mongodb/mongo-ruby-driver),
version 1.8.4 or later. Ruby, version 1.9.3 or later.

Let’s check if everything is installed.

Use the following command to load *zips.json* data set into mongod
instance:

```sh
mongoimport --drop -d test -c zipcodes zips.json
```

On the *irb* console run:

```ruby
require "mongo"
include Mongo

db = MongoClient.new("localhost", 27017, w: 1).db("test")
coll = db.collection("zipcodes")
coll.count     #=> should return 29467
coll.find_one
```

## Aggregations using the Zip Code Data Set

Each document in this collection has the following form:

```json
{
  "loc" : [-86.51557, 33.584132],
  "pop" : 6055,
  "state" : "AL",
  "_id" : "35004",
  "city" : "Acmar"
}
```

In these documents:

* The `_id` field holds the zipcode as a string.
* The `city` field holds the city name.
* The `state` field holds the two letter state abbreviation.
* The `pop` field holds the population.
* The `loc` field holds the location as a *[*latitude*, *longitude*]* pair.


## States with Populations Over 10 Million

To return all states with a population greater than 10 million, use the following aggregation operation:

```ruby
puts coll.aggregate([
  {"$group" => {_id: "$state", total_pop: {"$sum" => "$pop"}}},
  {"$match" => {total_pop: {"$gte" => 10_000_000}}}
])
```
The result:

```ruby
{"_id"=>"PA", "total_pop"=>11881643}
{"_id"=>"OH", "total_pop"=>10847115}
{"_id"=>"NY", "total_pop"=>17990455}
{"_id"=>"FL", "total_pop"=>12937284}
{"_id"=>"TX", "total_pop"=>16986510}
{"_id"=>"IL", "total_pop"=>11430472}
{"_id"=>"CA", "total_pop"=>29760021}
```

The above aggregate runs two pipeline operator processes:
`$group` and `$match`.

The `$group` process reads all documents and creates a separate
document for each state. This document contains two fields:
`_id` and `total_pop`. The `total_pop` field uses the `$sum`
operation to sum the values of all `pop` fields in the source documents.

The `$group` pipeline produces the following documents:

```ruby
{"_id"=>"WA", "total_pop"=>4866692}
```

The `$group` process pipes produced documents to the `$match` process
which filters documents so that the only documents that remain are
those where the value of `total_pop` is greater than or equal to 10
million.


## Average City Population by State

To return the first three states with the greatest average population
per city, use the following aggregation:

```ruby
puts coll.aggregate([
  {"$group" => {_id: {state: "$state", city: "$city"}, pop: {"$sum" => "$pop"}}},
  {"$group" => {_id: "$_id.state", avg_city_pop: {"$avg" => "$pop"}}},
  {"$sort" => {avg_city_pop: -1}},
  {"$limit" => 3}
])
```

This aggregate pipeline produces:

```ruby
{"_id"=>"DC", "avg_city_pop"=>303450.0}
{"_id"=>"FL", "avg_city_pop"=>27942.29805615551}
{"_id"=>"CA", "avg_city_pop"=>27735.341099720412}
```

The `$group` process produces the following documents:

```ruby
{"_id"=>{"state"=>"WY", "city"=>"Smoot"}, "pop"=>414}
```


# Quiz

1. What is the result of running this command:

```ruby
coll.aggregate([])
```
