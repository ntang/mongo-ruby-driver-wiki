## Thin
### load.rb
```ruby
require 'logger'

include Mongo

$con = MongoReplicaSetClient.new(
  ['localhost:30000', 'localhost:30001'],
  :read => :secondary,
  :refresh_mode => :sync,
  :refresh_interval => 30
)
$db = $con['foo']

class Load < Sinatra::Base

  configure do
    LOGGER = Logger.new("sinatra.log")
    enable :logging, :dump_errors
    set :raise_errors, true
  end

  get '/' do
    $db['test'].insert({:a => rand(1000)})
    $db['test'].find({:a => {'$gt' => rand(2)}}, :read => :secondary).limit(2).to_a
    "ok"
  end

end
```

### config.ru
```ruby
require "rubygems"
require "sinatra"

require File.join(File.dirname(__FILE__), 'load.rb')

run Load
```