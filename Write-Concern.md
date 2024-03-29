# Write Concern in Ruby

## Setting the write concern

Write concern is set using the `:w` option. As of driver version 1.8, writes are acknowledged by default with :w => 1.  There are several possible other options:

```ruby
@mongo_client.save({:doc => 'foo'}, {:w => 0})  # writes are not acknowledged
@mongo_client.save({:doc => 'foo'}, {:w => 1})  # writes are acknowledged (MongoClient DEFAULT)
@mongo_client.save({:doc => 'foo'}, {:w => 2})  # replica set acknowledged

# replica set acknowledged, with 200 ms timeout
@mongo_client.save({:doc => 'foo'}, {:w => 2, :wtimeout => 200}) 

# replicaset acknowledged, with 200 ms timeout and journal write acknowledged
@mongo_client.save({:doc => 'foo'}, {:w => 2, :wtimeout => 200, :j => true})
```

The option, :w => 0, indicates that writes are not acknowledged.
The option, :w => 1, is the default setting for a mongo client and specifies that writes are acknowledged.
The option, :w => 2, acknowledges a write to a replica set.  Any integer can be provided for w that indicates the number of nodes to which the write must be applied before it is deemed successful.
The options :j, :fsync and :wtimeout may be used with :w, when :w is greater than 0.  In other words, the j, :fsync and :wtimeout options may be used in conjunction with acknowledged writes.


## Write concern inheritance

The Ruby driver allows you to set write concern on each of four levels: the mongo client, database, collection, and write operation.
Objects inherit the default write concern from their direct parents. If you set a write concern of `{:w => 0}` when creating
a new mongo client, then all databases and collections created from that connection will inherit the same setting. See this code example:

```ruby
@mongo_client = MongoClient.new('localhost', 27017, :w => 0)  # include Mongo module above
@db  = @mongo_client['test']
@collection = @db['foo']
@collection.save({:name => 'foo'})

@collection.save({:name => 'bar'}, :w => 1)
```

Here, the first call to Collection#save will use the inherited write concern, `{:w => 0}` and not acknowledge the write. But notice that the second call
to Collection#save overrides this setting and confirms the write.