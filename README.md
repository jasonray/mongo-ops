Ops Class
---------

For me during class
---------
```
./mongod --dbpath /code/exploratory/mongo-ops/data/db
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

ulimit
------
`ulimit -a`

http://docs.mongodb.org/manual/administration/ulimit/

I wasn't paying attention for a moment, but this show number of max open files (1024).  He said to raise this





