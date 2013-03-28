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
* The `loc` field holds the location as a latitude longitude pair.





## States with Populations Over 10 Million

To return all states with a population greater than 10 million, use the following aggregation operation:

```ruby
puts coll.aggregate([
  {"$group" => {_id: "$state", total_pop: {"$sum" => "$pop"}}},
  {"$match" => {total_pop: {"$gte" => 10_000_000}}}
])
```
