Ops Class
---------
These are my notes started from Mongo DC workshop [http://www.10gen.com/events/mongodb-washington-dc-workshops-2013]

#### For me during class
```
./mongod --dbpath /code/exploratory/mongo-ops/data/db -rest
use mongo-ops
```

Mongo Processes
---------------
The mongo ecosystem is comprised of:

`mongod`: database engine
`mongo`: mongo shell client
`mongos`: mongo router after you have setup sharding

Startup
-------
In general, start using `mongod`.

Options:
#### specify data directory
```
mongod --dbpath /data/db
```

#### specify listener port
```
mongod --port 12345
```

#### run as a daemon
By default, mongod runs as foreground process.  To run as background process / daemon / fork:
```
mongod --fork --logpath /tmp/file.log
```

#### directory per db
The `directoryperdb` option allows for a different directory per logical database handled by the mongod process.  Although this is likely to be more of a dev concern, it could be used in production to allow mapping different databases to different physical disks.

More info: [http://docs.mongodb.org/manual/tutorial/manage-mongodb-processes/#start-mongod]
More config options: [http://docs.mongodb.org/manual/administration/configuration/]


Shutdown
--------
Shutdown using SIGTERM:
```
killall mongod
```

Shutdown using SIGKILL:
```
killall -9 mongod
```

From mongo shell:
```
db.shutdownServer()
```

More info on shutdown: [http://docs.mongodb.org/manual/administration/configuration/#starting-stopping-and-running-the-database]


Production setup
----------------
Can be run using either linux or windows, although instructor seemed to prefer linux.

Recommend to use download [http://www.10gen.com/products/mongodb] rather than linux/mac package installer.

Release Numbers
---------------
Even releases are prod releases (2.2)
Odd releases are dev preview release (2.3)


Get Stats on db
---------------
Mongodb offers mechanisms to collect stats from the database engine.  These can be done via shell.

database stats
==============
```
> db.stats()
{
 "db" : "mongo-ops",
 "collections" : 4,
 "objects" : 29475,
 "avgObjSize" : 95.95209499575911,
 "dataSize" : 2828188,
 "storageSize" : 11194368,
 "numExtents" : 9,
 "indexes" : 2,
 "indexSize" : 1291808,
 "fileSize" : 201326592,
 "nsSizeMB" : 16,
 "ok" : 1
}
```

Info on db.stats: [http://docs.mongodb.org/manual/reference/method/db.stats/#db.stats]

specific collection stats
=========================
```
> db.zips.stats();
{
	"ns" : "mongo-ops.zips",
	"count" : 29467,
	"size" : 2827824,
	"avgObjSize" : 95.9657922421692,
	"storageSize" : 11182080,
	"numExtents" : 6,
	"nindexes" : 1,
	"lastExtentSize" : 8388608,
	"paddingFactor" : 1,
	"systemFlags" : 1,
	"userFlags" : 0,
	"totalIndexSize" : 1283632,
	"indexSizes" : {
		"_id_" : 1283632
	},
	"ok" : 1
}
```

Info on db.collection.stats: [http://docs.mongodb.org/manual/reference/collection-statistics/]

One note is that padding factor can be used to recognize that we have to move stuff a lot

to see in MB:
```
> db.zips.stats(1024*1024);
```

Server Status
=============
```
db.serverStatus()
```

pid: current process
uptime: various fields to see how long db has been up
locks
mem: expect it to be bits:64; resident: amount of memory allocate (in MB)
connections: active client connections to mongod
indexCounter: can see how often we are using indexes vs not
network: can see info on network in/out usages
opcounters: number of discrete actions (i/u/etc)
metrics: has more stuff

Info on server status: [http://docs.mongodb.org/manual/reference/server-status/]

per DB view of some of this stuff
=================================
```
db.adminCommand('top')`
```

mongostat
=========
`mongostat`
will output to command line the server stats

mongotop
========
`mongotop`
shows which collections are being hit

info on mongostat: [http://docs.mongodb.org/manual/reference/mongostat/]

ulimit
======
`ulimit -a`

http://docs.mongodb.org/manual/administration/ulimit/

I wasn't paying attention for a moment, but this show number of max open files (1024).  He said to raise this

info on ulimit: [http://docs.mongodb.org/manual/administration/ulimit/]

Web Interface
=============
Mongodb comes with an admin web interface.

It runs on port+1000

So for example, if mongod is running on 27017, then the web interface would be on `http://localhost:28017`

Many of the features require that mongod is started with rest support using `--rest`.  This also enables ability to set settings via http / curl or utilities.

With rest enabled, need to be concerned with preventing unauthorized access to admin settings.  Can enable auth.  Would want to block these port from the world.

Can optionally enable jsonp but don't do in production as this would allow arbitrary execution on db.

More info on HTTP / Web Interface: [http://docs.mongodb.org/ecosystem/tools/http-interfaces/]

OS tools 
========
Most of these are linux specific tools.  I list these just as recommendation from instructor on what is good for tracking mongod performance.

#### iostat
view disk activity
on Linux: `iostat -xm 2` 
on Mac: `iostat` 

#### top
view cpu, etc

#### lsblk, blockdev
view disk performance

Note: can turn to reduce read-ahead blocks on disk if doing lots of random-access.
Making lower can reduce disk io.

Mongo Monitoring Service
========================
http://www.10gen.com/products/mongodb-monitoring-service

Cloud-host service.  Also offered as a on-premise app.

Advice on Disk
--------------
switch to fast disks.  SSD way over spinning-disks.
file system recommendation: XFS or ext4, do not use NFS

Backup
------
### On-line backup using mongo
`mongodump` / `mongorestore`
(what is the oplog flag)

### File system backup
shutdown server or use db.fsyncLock()
copy data/db files
restart or use db.fsyncUnlock()

Can do this from a secondary rather than the primary to lessen production impact

Note that while locked, access is blocked.

Run javascript from terminal through mongo
------------------------------------------
```
mongo /path/to/code.js
```

this allow you to execute shell commands

Replication
-----------
For my own experiment, create data location for each instance that I will start
```
mkdir /code/exploratory/mongo-ops/data/db/a
mkdir /code/exploratory/mongo-ops/data/db/b
mkdir /code/exploratory/mongo-ops/data/db/c
```

Start multiple instances, each with own data and port (this would likely be multiple machines)
```
./mongod --replSet myrs --rest --smallfiles --dbpath /code/exploratory/mongo-ops/data/db/a --port 10001
./mongod --replSet myrs --rest --smallfiles --dbpath /code/exploratory/mongo-ops/data/db/b --port 10002
./mongod --replSet myrs --rest --smallfiles --dbpath /code/exploratory/mongo-ops/data/db/c --port 10003
```

#### Setup
rs.initiate() # this start an interactive session of adding to the replica set
rs.add(..)
rs.add(..)
rs.conf()
-> can init with this output rather than this interactive

rs.status()

#### Adding a new node
can be done via rs.add() with blank or starting from backup

#### Enable read from slave
rs.slaveOk();

#### Edit config
```
c = rs.config()
edit c
rs.reconfig(c)
```

#### Config options
- priority
- hidden: don't even use for queries
- slaveDelay: don't send data right away to give time in case of accident, acts as rolling backup

#### Wait for propagation
to demo this, set slaveDelay to 10s
db.z.insert(..); db.getLastError(..);
client will wait while it propagates

#### conflicts, rollback
if diverge (primary goes down and has not propagate)
when primary comes back up possible that it will need to auto-rollback
this can be mitigated by changing write-concern to majority+

## Read from secondary
Instructor: tried to talk us out of reading from secondaries as it will likely put you in a spot where you are beyond capacity.  Usually if you are using read from secondary you have probably messed up your capacity planning.
Good use is distributed data centers

Sharding
--------
#### Analogy
Sharding => raid 0
Replication => raid 1

#### Notes
setup replica's on each shard
each shard represents a replica SET

for each collection, set key
start with single chunk (+/- inf)
when certain size, split chunk (- to x, x to inf)
chunks get split among shard

mongos on each appserver

can use --chunkSize to make it easier to view behavior

#### view sharding status
> sh.status

#### More notes
http://docs.mongodb.org/manual/reference/command/shardCollection/
sh.ShareCollection('other', {key:1});









