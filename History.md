# MongoDB Ruby Driver History

### 1.8.4
2013-03-26

* Support for MongoDB 2.4 and all changes from 1.8.4.rc0 are in this release.
* Fixed warning during C extension compilation (RUBY-567)

### 1.8.4.rc0
2013-03-19

* Improvements to connection pool pinning behavior when change read preferences (RUBY-475)
* Explicit exception is now raised on attempts to authenticate multiple users to the same database (RUBY-529)
* Support for new MongoDB 2.4 index types (RUBY-564)
* Resolved an issue where Collection#drop caused a fetch of all namespaces, deprecated DB#strict (RUBY-537)

### 1.8.3
2013-03-04

* All changes from 1.8.3.rc0 and 1.8.3.rc1 are in this release.
* Fix so that batch size is applied to initial query (RUBY-558)

### 1.8.3.rc1
2013-02-27

* Fixes for replica set routing of OP_KILL_CURSORS and OP_GET_MORE
* Replica set tests now use 3 members and no arbiters

### 1.8.3.rc0
2013-02-14

* Added cryptographic signature to all gems, [install with `-P` option](http://docs.rubygems.org/read/chapter/21) (RUBY-538)
* Support for object_id.to_s in the BSON C extension (@jeem)
* Corrected logic for how the connection pool refreshes and cleans-up sockets (RUBY-543)
* Fixed read preferences for MongoShardedClient (RUBY-542)
* Fixed URI connection string parsing for MongoShardedClient (RUBY-541)
* Fixed potential thread dead-locking in Pool#checkin (RUBY-556)
* Fixed thread-safety issues in the BSON extension for JRuby (RUBY-554)

### 1.8.2
2013-01-17

* Fix for thread affinity not using the max socket pool size (RUBY-532)
* Fix for meta operator failure on older versions of MongoDB (RUBY-525)

### 1.8.1 (pulled)
2013-01-03

* Support sending $readPreference to mongos
* Fix thread leaks in MongoReplicaSetClient (Chris Heald)
* Java extension cleanup
* Improved index creation interface (Steve Francia)
* Support read preference for various commands (Nelson Elhage)
* Added UNIX socket support (thanks to @vpereira for initial contribution)

### 1.8.0
2012-11-27

* New top level class Mongo::MongoClient acknowledges writes by default
* New top level class Mongo::ReplSetClient acknowledges writes by default
* New top level class Mongo::ShardedClient acknowledges writes by default
* MongoClient, MongoReplicaSetClient, MongoShardedClient and GridFS implement a new write concern interface at Client, DB, Collection, and Operation levels
* Deprecation of Mongo::Connection in favor of Mongo::MongoClient
* Deprecation of Mongo::ReplSetConnection in favor of Mongo::MongoReplicaSetClient
* Deprecation of Mongo::ShardedConnection in favor of Mongo::MongoShardedClient
* Allow specification of comment query opt (Evan Broder)
* Fix for pool authentication and logout (Olivier Bonnaure)
* Fix for cursor not being closed in presence of exceptions (Simon Simeonov)
* Fix for data send failure not closing socket (Nelson Elhage)
* Tutorials and non code documentation moved to GitHub wiki 
* Provided binary mongo_client has been renamed to mongo_console


### 1.7.1
2012-11-16

* Adding a fix for an issue that was breaking replica set connections when utilizing database authentication.

### 1.7.0
2012-08-20

* Added testing and full support for MongoDB 2.1 & 2.2
* Added Aggregation Framework helper method
* Added support for Mongos high availability
* Modified and added new read preferences (details in documentation)
* Added support for data center awareness (tag_sets)
* Fixed bug which attempted to close cursors on wrong replica set member

### 1.6.4
2012-06-06

* Added ability to declare sort ordering via an ordered hash
* Addresses major compatability issue with mongoid created by v1.6.3

### 1.6.3
2012-06-05

* Performance measurements and enhancements (especially for C-extensions)
* Bug fixes for checking strings with non UTF-8 forced or implied encodings
* Added refresh support for multiple threaded instances of ReplSetConnection
* Added ability to handle IRB::Abort Exception (ctrl-c) cleanly
* Added support for large dates on 32-bit platforms (Ruby 1.9+)
* Added #to_ary method for BSON::ObjectId (Farrel Lifson)
* Added support for ENV['MONGODB_URI'] (Seamus Abshere)
* Various gridio bug fixes (John Bintz)
* Various logging support improvements
* Various documentation improvements (tutorials, sorting, links)

### 1.6.2
2012-04-05

* Implements socket timeouts via non-blocking IO instead of Timeout module
which should greatly increase performance in highly threaded applications
* Added ability to authentication via secondary if primary node unavailable
* Replica set refresh interval now enforces a lower bound of 60 seconds
* Added documentation for dropping indexes, collections, databases
* Test output cleanup (...)s unless failure occurs

### 1.6.1
2012-03-07

* Added thread affinity to Mongo::Pool
* Added deploy tasks
* Added Travis CI support (Cyril Mougel)
* Logging warning message is only displayed for level :debug

### 1.6.0
2012-02-22

* Added Gemfile
* ReplSetConnection seed format is now array of 'host:port' strings
* Added read preference :secondary_only
* Added ability to log duration -- enabled by default (Cyril Mougel)
* Added read_only option for DB#add_user (Ariel Salomon)
* Added :collect_on_error option for bulk-insert (Masahiro Nakagawa)
* Added and updated URI options (now case insensitive)
* Bug fix for ReplSet refresh attempting to close a closed socket
* Default op_timeout for ReplSetConnection is now disabled (was 30 seconds)
* Support db output option for map reduce (John Ewart)
* Support for keeping limited versions of files using GridFS (VvanGemert)

### 1.5.2
2011-12-13

* Lots of fixes for replica set connection edge cases.
* Set default op_timeout and connect_timeout to 30 seconds.
* Support GeoHaystack indexing.

### 1.5.1
2011-11-29

Release due to corrupted gemspec. This was a bug having
to do with rubygems. Apparently, gems must still be
built with Ruby 1.8.

### 1.5.0
2011-11-28

This releases fixes bugs introduced in 1.4.0 and 1.4.1 that
were introduced as a result of adding replica set refresh modes.

* Removed :async refresh mode.
* Disabled auto refresh mode by default. If you want the driver
to automatically check the state of the replica set, you must
use :sync mode. Note that replica set refresh is designed only to
account for benign changes to the replica set (adding and removing
nodes that don't affect current connections).
* Fixed bug with commands being sent to secondary nodes. The next
release will allow you to specify where commands can be sent.
* Support :j safe mode option.
* Fix :max_scan and :show_disk_loc Cursor options.

You can see the remaining issues at https://jira.mongodb.org/secure/ReleaseNote.jspa?projectId=10005&version=10992

### 1.5.0.rc0
2011-11-18

Fix bugs associated with replica set refresh.

### 1.4.1
2011-10-17

If you're using 1.4.0, this is a necessary upgrade.

* Simplified replica set refresh.
* Fix bugs associated with replica set refresh.
* Make cursor smart enough to continue functioning
even if a refresh is triggered.

### 1.4.0
2011-9-19

* Attempt to automatically refresh internal replica set state using ReplSetConnection#refresh.
* Two automated refresh modes: :async and :sync. Automated refresh can also be disabled.
* Choose secondary for reads based on ping time.
* Read preference API: specify whether queries should go to primary or secondary on a per-query basis.
* Pass :require_primary => false to ReplSetConnection to connect without requiring a primary node.
* Enable exhaust-mode queries with OP_QUERY_EXHAUST.
* Collection#count takes a query selector.
* Support continue_on_error flag for bulk inserts (use :continue_on_error => true)
* Add Cursor#add_option. Deprecate Cursor#query_opts and replace with Cursor#options.
* Initial SSL support (connect with :ssl => true)
* Update to latest Java driver for JRuby.
* Check max BSON size on a per-connection basis.
* Fixed two platform-specific BSON serialization issues.
* Lots of bug fixes and code cleanup.

### 1.3.1
2011-5-10

* Fix GridIO#gets infinite loop error (Ryan McGeary)
* Fix BSON::OrderedHash#reject! leaving keys with null values (rpt. by Ben Poweski)
* Minor semantic fix for OrderedHash#reject!
* Fix Mongo::DB to allow symbols in method traversing collection names (rpt. by Chris Griego)
* Support new server regex option "s" (dotall). This is folded in with \m in Ruby.
* Fix so that Cursor#close hits the right node when :read_secondary is enabled.
* Support maxScan, showDiskLoc, and returnKey cursor options.
* Make DB#validate_collection compatible with server v1.9.1.
* Fix so that GridIO#gets returns local md5 with md5 matches server md5 (Steve Tantra).
* Fix bug in BSON::OrderedHash that prevents YAML.load (Ian Warshak).
* Fix example from /examples.
* Ensure that we do not modify hash arguments by calling Hash#dup when appropriate.
* Ensure that JRuby deserializer preserves binary subtypes properly.
* Fix for streaming an empty file into GridFS (Daniël van de Burgt).
* Minor doc fixes.

### 1.3.0
2011-4-04

* Add option to set timeouts on socket read calls using the
  Mongo::Connection :op_timeout option.
* Add StringIO methods to GridIO objects
* Support for BSON timestamp type with BSON::Timestamp
* Change the BSON binary subtype from 2 to 0
* Remove private method Connection#reset_conection
  and deprecate public method ReplSetConnection#reset_connection
* ByteBuffer#== and OrderedHash#dup (Hongli Lai)
* Better check for UTF8 validity in Ruby 1.9
* Added previously removed Connection#host and Connection#port
* Added transformers to allow Mongo::Cursor to allow instantiated objects (John Nunemaker)
* Automated reconnection on fork
* Added Cursor#next alias for Cursor#next_document
* Audit tests after enabling warnings (Wojciech Piekutowski)
* Various bug fixes thanks to Datanoise, Hongli Lai, and Mauro Pompilio

### 1.2.4
2011-2-23

* Fix the exception message shown when there's an IOError (Mauro Pompilio)
* Another update to map-reduce docs for v1.8. Note that if you use the new
  output option `{:out => {:inline => true}}`, then you must also specify
  `:raw => true`.

### 1.2.3
2011-2-22

* Update docs for map-reduce command
* Minor doc fix

### 1.2.2
2011-2-15

* Improved replica set failover for edge case.
* Fix for REE on OSX (Hongli Lai)

### 1.2.1
2011-1-18

* Enable authentication with connection pooling.
* Allow custom logging with Connection#instrument (CodeMonkeySteve)
* Minor fixes and doc improvements.

### 1.2.0
2011-1-18

* Some minor improvements. See commit history.

### 1.2.rc0
2011-1-5

Lots of cleanup and minor bug fixes.
* Issues resolved: http://jira.mongodb.org/browse/RUBY/fixforversion/10222
* Updated Java BSON to Java driver 2.4.
* Platform gem for JRuby bson.

### 1.1.5
2010-12-15

* ReplSetConnection class. This must be used for replica set connections from
  now on. You can still use Connection.multi, but that method has been deprecated.
* Automated replica set tests. rake test:rs
* Check that request and response ids match.
* Several bug fixes. See the commit history for details.

### 1.1.4
2010-11-30

* Important connection failure fix.
* ObjectId#to_s optimization (David Cuadrado).

### 1.1.3
2010-11-29

* Distributed reads for replica set secondaries. See /docs/examples/replica_set.rb and
  http://api.mongodb.org/ruby/current/file.REPLICA_SETS.html for details.
* Note: when connecting to a replica set, you must use Connection#multi.
* Cursor#count takes optional skip and limit
* Collection#ensure_index for caching index creation calls
* Collection#update and Collection#remove now return error object when using safe mode
* Important fix for int/long serialization on bug introduced in 1.0.9
* Numerous tweaks and bug fixes.

### 1.1.2
2010-11-4

* Two critical fixes to automated failover and replica sets.
* Bug passing :timeout to Cursor.
* Permit safe mode specification on Connection, Collection, and DB levels.
* Specify replica set name on connect to verify connection to the right set.
* Misc. reorganization of project and docs.

### 1.1.1
2010-10-14

* Several critical JRuby bug fixes
* Fixes for JRuby in 1.9 mode
* Check keys and move id only when necessary for JRuby encoder

## 1.1
2010-10-4

* Official JRuby support via Java extensions for BSON (beta)
* Connection#lock! and Connection#unlock! for easy fsync lock
* Note: BSON::Code is no longer a subclass of String.

### 1.0.9
2010-9-20

* Significant performance improvements (with a lot of help from Hongli Lai)

### 1.0.8
2010-8-27

* Cursor#rewind! and more consistent Cursor Enumerable behavior
* Deprecated ObjectID for ObjectId
* Numerous minor bug fixes.

### 1.0.7
2010-8-4

* A few minor test/doc fixes.
* Better tests for replica sets and replication acknowledgment.
* Deprecated DB#error and DB#last_status

### 1.0.6
2010-7-26

* Replica set support.
* Collection#map_reduce bug fix.

### 1.0.5 
2010-7-13

* Fix for bug introduced in 1.0.4.

### 1.0.4
2010-7-13

* Removed deprecated
  * Cursor admin option
  * DB#query
  * DB#create_index (use Collection#create_index)
  * DB#command only takes hash options now
* j2bson executable (neomantra)
* Fixed bson_ext compilation on Solaris (slyphon)
* System JS helpers (neovintage)
* Use one mutex per thread on pooled connections (cremes)
* Check for CursorNotFound response flag
* MapReduce can return raw command output using :raw
* BSON::OrderedHash equality with other Ruby hashes (Ryan Angilly)
* Fix for broken Socket.send with large payloads (Frédéric De Jaeger)
* Lots of minor improvements. See commits.

### 1.0.3
2010-6-15

* Optimiztion for BSON::OrderedHash
* Some important fixes.

### 1.0.2
2010-6-5

This is a minor release for fixing an incompatibility with MongoDB v1.5.2

* Fix for boolean response on commands for core server v1.5.2
* BSON.read_bson_document and b2json executable (neomantra)
* BSON::ObjectID() shortcut for BSON::ObjectID.from_string (tmm1)
* Various bug fixes.

### 1.0.1
2010-5-7

* set Encoding.default_internal
* DEPRECATE JavaScript string on Collection#find. You now must specify $where explicitly.
* Added Grid#exist? and GridFileSystem#exist?
* Support for replication acknowledgment
* Support for $slice
* Namespaced OrderedHash under BSON (sleverbor)

## 1.0
2010-4-29
Note: if upgrading from versions prior to 0.20, be sure to upgrade
to 0.20 before upgrading to 1.0.

* Inspected ObjectID is represented in MongoDB extended json format.
* Support for tailable cursors.
* Configurable query response batch size (thx. to Aman Gupta)

* bson_ext installs on early release of Ruby 1.8.5 (dfitzgibbon)
* Deprecated DB#create_index. Use Collection#create_index index.
* Removed deprecated Grid#put syntax; no longer requires a filename.

### 0.20.1
2010-4-7

* Added bson gem dependency.

### 0.20
2010-4-7

If upgrading from a previous version of the Ruby driver, please read these notes carefully,
along with the 0.20_UPGRADE doc.

* Support for new commands:
  * Collection#find_and_modify
  * Collection#stats
  * DB#stats
* Query :fields options allows for values of 0 to exclude fields (houdini, railsjedi).
* GridFS
  * Option to delete old versions of GridFileSystem entries.
  * Filename is now optional for Grid#put.
  * Option to write arbitrary attributes to a file: @grid.put(@data, :favorite_phrase => "blimey!")
  * Indexes created on the chunks collection are now unique. If you have an existing chunks collection,
    you may want to remove 
* Removed the following deprecated items:
  * GridStore class
  * RegexpOfHolding class
  * Paired connections must now be initialized with Connection.paired

* BSON-related code extracted into two separate gems: bson and bson_ext (thx to Chuck Remes).
  * mongo_ext no longer exists.
  * BSON::Binary constructor can now take a string, which will be packed into an array.
  * Exception class adjustments:
    * Mongo::InvalidObjectID moved to BSON::InvalidObjectID
    * Mongo::InvalidDocument moved to BSON::InvalidDocument
    * Mongo::InvalidStringEncoding moved to BSON::InvalidStringEncoding
    * Mongo::InvalidName replaced by Mongo::InvalidNSName and BSON::InvalidKeyName
  * BSON types are now namespaced under the BSON module. These types include:
    * Binary
    * ObjectID
    * Code
    * DBRef
    * MinKey and MaxKey
  * Extensions compile on Rubinius (Chuck Remes).

## Prior to 0.20

See git revisions.