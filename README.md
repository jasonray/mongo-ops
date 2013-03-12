Ops Class
---------
These are my notes started from Mongo DC workshop [http://www.10gen.com/events/mongodb-washington-dc-workshops-2013]

#### For me during class
```
./mongod --dbpath /code/exploratory/mongo-ops/data/db -rest
use mongo-ops
```

Shutdown
---------

Safe Shutdown:
`killall mongod`

Harsh Shutdown:
`killall -9 mongod`

From mongo shell:
`db.shutdownServer()`

Production setup
---------

use linux
use download rather than package installer
file system recommendation: XFS or ext4, do not use NFS

Releases
---------

even releases are prod releases (2.2)
odd releases are dev preview release (2.3)

Command line
---------

Data defaults to:
/data/db
or custom --dbpath


option: --directoryperdb
(allows you to mount db's to different storage)


Get Stats on db
---------

On DB
=====
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

On collection
=====
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

Padding Factor: recognizing that we have to move stuff alot

to see in MB:
> db.zips.stats(1024*1024);

Server Status
-------------
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

Per Db View of some of this stuff
=======
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

ulimit
------
`ulimit -a`

http://docs.mongodb.org/manual/administration/ulimit/

I wasn't paying attention for a moment, but this show number of max open files (1024).  He said to raise this

Web Interface
-------------
port+1000

http://localhost:28017

Out of the box admin ui tool

must use `--rest` on server startup to use some of the features.
This enables ability to set stuff via http / curl or utilities

Can enable auth.  Would want to block these port from the world.

Can enable jsonp but don't do in production as this would allow arbitrary execution on db.

OS tools 
--------
Most of these are linux specific tools.  I list these just as recommendation from instructor on what is good for tracking mongod performance.

## iostat
view disk activity
on Linux: `iostat -xm 2` 
on Mac: `iostat` 

## top
view cpu, etc

## lsblk, blockdev
view disk performance





