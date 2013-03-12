./mongod --dbpath /code/exploratory/mongo-ops/data/db
use mongo-ops

Safe Shutdown:
killall mongod
Harsh Shutdown:
killall -9 mongod

looks like you can do from shell:
db.shutdownServer()

use linux at runtime
in ops, use download rather than package installer

even releases are prod releases (2.2)
odd releases are dev preview release (2.3)

/data/db
or custom --dbpath

option: --directoryperdb
(allows you to mount db's to different storage)

file system recommendation: XFS or ext4, do not use NFS


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

