# Replica Sets in Ruby

Here follow a few considerations for those using the MongoDB Ruby driver with [replica sets](http://www.mongodb.org/display/DOCS/Replica+Sets).

### Setup

First, make sure that you've configured and initialized a replica set.

Use `MongoReplicaSetClient.new` to connect to a replica set. This method, which accepts a variable number of arguments,
takes a list of seed nodes followed by any connection options. You'll want to specify at least two seed nodes. This gives
the driver more chances to connect in the event that any one seed node is offline. Once the driver connects, it will
cache the replica set topology as reported by the given seed node and use that information if a failover is later required.
```ruby
@mongo_client = MongoReplicaSetClient.new(
  ['n1.mydb.net:27017', 'n2.mydb.net:27017', 'n3.mydb.net:27017']
)
```
Note that in driver version >= 1.8, writes are confirmed by default.  This can be changed with the :w option.  For versions prior to 1.8, we recommended specifying :safe => true for write confirmation.

### Read slaves

If you want to read from a secondary node, you can pass :read => :secondary to MongoReplicaSetClient#new.
```ruby
@mongo_client = MongoReplicaSetClient.new(
  ['n1.mydb.net:27017', 'n2.mydb.net:27017', 'n3.mydb.net:27017'],
  :read => :secondary
)
```
A random secondary will be chosen to be read from. In a typical multi-process Ruby application, you'll have a good distribution of reads across secondary nodes.

### Connection Failures

Imagine that either the master node or one of the read nodes goes offline. How will the driver respond?

If any read operation fails, the driver will raise a *ConnectionFailure* exception. It then becomes the application's responsibility to decide how to handle this.

If the application decides to retry, it's not guaranteed that another member of the replica set will have been promoted to master right away, so it's still possible that the driver will raise another *ConnectionFailure*. However, once a member has been promoted to master, typically within a few seconds, subsequent operations will succeed. *Note that this does not prevent
exception in the event of a primary failover.*

The driver will essentially cycle through all known seed addresses until a node identifies itself as master.

### Refresh mode

You can specify a refresh mode and refresh interval for a replica set connection. This will help to ensure that
changes to a replica set's configuration are quickly reflected on the driver side. In particular, if you change
the state of any secondary node, the automated refresh will ensure that this state is recorded on the client side.

There are two scenarios in which refresh is helpful and does not raise exceptions:

1. You add a new secondary node to an existing replica set
2. You remove an unused secondary from an existing replica set

If you are using MongoDB earlier than 2.0, any changes to replica set state will raise exceptions therefore refresh mode will not be useful.

If you add a secondary that responds to pings much faster than the existing nodes, then the new secondary will
be used for reads if :read_preference is :secondary or :secondary_preferred

Refresh mode is disabled by default.

However, if you expect to make live changes to your secondaries, and you want this to be reflected without
having to manually restart your app server, then you should enable it. You can enable this mode
synchronously, which will refresh the replica set data in a synchronous fashion (which may
occasionally slow down your queries):
```ruby
@mongo_client = MongoReplicaSetClient.new(['n1.mydb.net:27017'], :refresh_mode => :sync)
```
If you want to change the default refresh interval of 90 seconds, you can do so like this:
```ruby
@mongo_client = MongoReplicaSetClient.new(
  ['n1.mydb.net:27017'],
  :refresh_mode => :sync,
  :refresh_interval => 60
)
```
Do not set this value to anything lower than 30, or you may start to experience performance issues.

You can also disable refresh mode altogether:
```ruby
@mongo_client = MongoReplicaSetClient.new(
  ['n1.mydb.net:27017'],
  :refresh_mode => false
)
```
And you can call `refresh` manually on any replica set connection:
```ruby
@mongo_client.refresh
```
### Recovery

Driver users may wish to wrap their database calls with failure recovery code. Here's one possibility, which will attempt to connect
every half second and time out after thirty seconds.
```ruby
# Ensure retry upon failure
def rescue_connection_failure(max_retries=60)
  retries = 0
  begin
    yield
  rescue Mongo::ConnectionFailure => ex
    retries += 1
    raise ex if retries > max_retries
    sleep(0.5)
    retry
  end
end

# Wrapping a call to #count()
rescue_connection_failure do
  @db.collection('users').count()
end
```
Of course, the proper way to handle connection failures will always depend on the individual application. We encourage object-mapper and application developers to publish any promising results.

### Testing

The Ruby driver (>= 1.1.5) includes unit tests for verifying replica set behavior. They reside in *tests/replica_sets*. You can run them as a group with the following rake task:

    rake test:rs

The suite will set up a five-node replica set by itself and ensure that driver behaves correctly even in the face
of individual node failures. Note that the `mongod` executable must be in the search path for this to work.

### Further Reading

* [Replica Sets](http://www.mongodb.org/display/DOCS/Replica+Set+Configuration)
* [Replica Set Configuration](http://www.mongodb.org/display/DOCS/Replica+Set+Configuration)