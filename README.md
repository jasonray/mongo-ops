h1. Ops Class

For me during class
```
./mongod --dbpath /code/exploratory/mongo-ops/data/db
use mongo-ops
```

h1. Shutdown
Safe Shutdown:
`killall mongod`

Harsh Shutdown:
`killall -9 mongod`

From mongo shell:
`db.shutdownServer()`

h1. Production setup

use linux
use download rather than package installer
file system recommendation: XFS or ext4, do not use NFS

h1. Releases

even releases are prod releases (2.2)
odd releases are dev preview release (2.3)

h1. Command line

Data defaults to:
/data/db
or custom --dbpath


option: --directoryperdb
(allows you to mount db's to different storage)


h1. Get Stats on db
h2. On DB
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

h2. On collection
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
