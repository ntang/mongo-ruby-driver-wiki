## Admin Example
```ruby
require 'mongo'
require 'pp'

include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
client  = MongoClient.new(host, port)
db   = client.db('ruby-mongo-examples')
coll = db.create_collection('test')

# Erase all records from collection, if any
coll.remove

admin = client['admin']

# Profiling level set/get
puts "Profiling level: #{admin.profiling_level}"

# Start profiling everything
admin.profiling_level = :all

# Read records, creating a profiling event
coll.find().to_a

# Stop profiling
admin.profiling_level = :off

# Print all profiling info
pp admin.profiling_info

# Validate returns a hash if all is well and
# raises an exception if there is a problem.
info = db.validate_collection(coll.name)
puts "valid = #{info['ok']}"
puts info['result']

# Destroy the collection
coll.drop
```

## Capped Collection Example
```ruby
require 'mongo'

include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
db = MongoClient.new(host, port).db('ruby-mongo-examples')
db.drop_collection('test')

# A capped collection has a max size and, optionally, a max number of records.
# Old records get pushed out by new ones once the size or max num records is reached.
coll = db.create_collection('test', :capped => true, :size => 1024, :max => 12)

100.times { |i| coll.insert('a' => i+1) }

# We will only see the last 12 records
coll.find().each { |row| p row }

coll.drop
```

## Working with Cursors
```ruby
require 'mongo'
require 'pp'

include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
db = MongoClient.new(host, port).db('ruby-mongo-examples')
coll = db.collection('test')

# Erase all records from collection, if any
coll.remove

# Insert 3 records
3.times { |i| coll.insert({'a' => i+1}) }

# Cursors don't run their queries until you actually attempt to retrieve data
# from them.

# Find returns a Cursor, which is Enumerable. You can iterate:
coll.find().each { |row| pp row }

# You can turn it into an array:
array = coll.find().to_a

# You can iterate after turning it into an array (the cursor will iterate over
# the copy of the array that it saves internally.)
cursor = coll.find()
array = cursor.to_a
cursor.each { |row| pp row }

# You can get the next object
first_object = coll.find().next_document

# next_document returns nil if there are no more objects that match
cursor = coll.find()
obj = cursor.next_document
while obj
  pp obj
  obj = cursor.next_document
end

# Destroy the collection
coll.drop
```

## GridFS
```ruby
def assert
  raise "Failed!" unless yield
end

require 'mongo'
include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
db = MongoClient.new(host, port).db('ruby-mongo-examples')

data = "hello, world!"

grid = Grid.new(db)

# Write a new file. data can be a string or an io object responding to #read.
id = grid.put(data, :filename => 'hello.txt')

# Read it and print out the contents
file = grid.get(id)
puts file.read

# Delete the file
grid.delete(id)

begin
grid.get(id)
rescue => e
  assert {e.class == Mongo::GridError}
end

# Metadata
id = grid.put(data, :filename => 'hello.txt', :content_type => 'text/plain', :metadata => {'name' => 'hello'})
file = grid.get(id)

p file.content_type
p file.metadata.inspect
p file.chunk_size
p file.file_length
p file.filename
p file.data
```

## Gathering Information
```ruby
require 'mongo'

include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
db = MongoClient.new(host, port).db('ruby-mongo-examples')
coll = db.collection('test')

# Erase all records from collection, if any
coll.remove

# Insert 3 records
3.times { |i| coll.insert({'a' => i+1}) }

# Collection names in database
p db.collection_names

# More information about each collection
p db.collections_info

# Index information
coll.create_index('a')
p db.index_information('test')

# Destroy the collection
coll.drop
```

## Queries
```ruby
require 'mongo'
require 'pp'

include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
db = MongoClient.new(host, port).db('ruby-mongo-examples')
coll = db.collection('test')

# Remove all records, if any
coll.remove

# Insert three records
coll.insert('a' => 1)
coll.insert('a' => 2)
coll.insert('b' => 3)
coll.insert('c' => 'foo')
coll.insert('c' => 'bar')

# Count.
puts "There are #{coll.count} records."

# Find all records. find() returns a Cursor.
puts "Find all records:"
pp cursor = coll.find.to_a

# Print them. Note that all records have an _id automatically added by the
# database. See pk.rb for an example of how to use a primary key factory to
# generate your own values for _id.
puts "Print each document individually:"
pp cursor.each { |row| pp row }

# See Collection#find. From now on in this file, we won't be printing the
# records we find.
puts "Find one record:"
pp coll.find('a' => 1).to_a

# Find records sort by 'a', skip 1, limit 2 records.
# Sort can be single name, array, or hash.
puts "Skip 1, limit 2, sort by 'a':"
pp coll.find({}, {:skip => 1, :limit => 2, :sort => 'a'}).to_a

# Find all records with 'a' > 1. There is also $lt, $gte, and $lte.
coll.find({'a' => {'$gt' => 1}})
coll.find({'a' => {'$gt' => 1, '$lte' => 3}})

# Find all records with 'a' in a set of values.
puts "Find all records where a is $in [1, 2]:"
pp coll.find('a' => {'$in' => [1,2]}).to_a

puts "Find by regex:"
pp coll.find({'c' => /f/}).to_a

# Print query explanation
puts "Print an explain:"
pp coll.find({'c' => /f/}).explain

# Use a hint with a query. Need an index. Hints can be stored with the
# collection, in which case they will be used with all queries, or they can be
# specified per query, in which case that hint overrides the hint associated
# with the collection if any.
coll.create_index('c')
coll.hint = 'c'

puts "Print an explain with index:"
pp coll.find('c' => /[f|b]/).explain

puts "Print an explain with natural order hint:"
pp coll.find({'c' => /[f|b]/}, :hint => '$natural').explain
```

## Replica Sets
```ruby
# This code assumes a running replica set with at least one node at localhost:27017.
require 'mongo'

include Mongo

cons = []

10.times do
  cons << MongoReplicaSetClient(['localhost:27017'], :read => :secondary)
end

ports = cons.map do |con|
  con.read_pool.port
end

puts "These ten connections will read from the following ports:"
p ports

cons[rand(10)]['foo']['bar'].remove
100.times do |n|
  cons[rand(10)]['foo']['bar'].insert({:a => n})
end

100.times do |n|
  p cons[rand(10)]['foo']['bar'].find_one({:a => n})
end
```

## Simple
```ruby
require 'rubygems'
require 'mongo'

include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
db = MongoClient.new(host, port).db('ruby-mongo-examples')
coll = db.collection('test')

# Erase all records from collection, if any
coll.remove

# Insert 3 records
3.times { |i| coll.insert({'a' => i+1}) }

puts "There are #{coll.count()} records in the test collection. Here they are:"
coll.find().each { |doc| puts doc.inspect }

# Destroy the collection
coll.drop
```

## Strict Mode
```ruby
require 'mongo'

include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
db = MongoClient.new(host, port).db('ruby-mongo-examples')

db.drop_collection('does-not-exist')
db.create_collection('test')

db.strict = true

begin
  # Can't reference collection that does not exist
  db.collection('does-not-exist')
  puts "error: expected exception"
rescue => ex
  puts "expected exception: #{ex}"
end

begin
  # Can't create collection that already exists
  db.create_collection('test')
  puts "error: expected exception"
rescue => ex
  puts "expected exception: #{ex}"
end

db.strict = false
db.drop_collection('test')
```

## Types
```ruby
require 'mongo'
require 'pp'

include Mongo

host = ENV['MONGO_RUBY_DRIVER_HOST'] || 'localhost'
port = ENV['MONGO_RUBY_DRIVER_PORT'] || MongoClient::DEFAULT_PORT

puts "Connecting to #{host}:#{port}"
db = MongoClient.new(host, port).db('ruby-mongo-examples')
coll = db.collection('test')

# Remove all records, if any
coll.remove

# Insert record with all sorts of values
coll.insert('array' => [1, 2, 3],
            'string' => 'hello',
            'hash' => {'a' => 1, 'b' => 2},
            'date' => Time.now, # milliseconds only; microseconds are not stored
            'oid' => ObjectID.new,
            'binary' => Binary.new([1, 2, 3]),
            'int' => 42,
            'float' => 33.33333,
            'regex' => /foobar/i,
            'boolean' => true,
            'where' => Code.new('this.x == 3'),
            'dbref' => DBRef.new(coll.name, ObjectID.new),
            'null' => nil,
            'symbol' => :zildjian)

pp coll.find().next_document

coll.remove
```