This is an implementation of the [LevelDB key/value database](https://github.com/google/leveldb) in the [Go programming language](https://go.dev).

In this fork, applied sharing mode when opening files in read/write mode on Windows.

[![Build Status](https://app.travis-ci.com/syndtr/goleveldb.svg?branch=master)](https://app.travis-ci.com/syndtr/goleveldb)

Installation
-----------

	go get github.com/syndtr/goleveldb/leveldb

Requirements
-----------

* Need at least `go1.14` or newer.

Usage
-----------

Create or open a database:
```go
// The returned DB instance is safe for concurrent use. Which mean that all
// DB's methods may be called concurrently from multiple goroutine.
db, err := leveldb.OpenFile("path/to/db", nil)
...
defer db.Close()
...
```
Read or modify the database content:
```go
// Remember that the contents of the returned slice should not be modified.
data, err := db.Get([]byte("key"), nil)
...
err = db.Put([]byte("key"), []byte("value"), nil)
...
err = db.Delete([]byte("key"), nil)
...
```

Iterate over database content:
```go
iter := db.NewIterator(nil, nil)
for iter.Next() {
	// Remember that the contents of the returned slice should not be modified, and
	// only valid until the next call to Next.
	key := iter.Key()
	value := iter.Value()
	...
}
iter.Release()
err = iter.Error()
...
```
Seek-then-Iterate:
```go
iter := db.NewIterator(nil, nil)
for ok := iter.Seek(key); ok; ok = iter.Next() {
	// Use key/value.
	...
}
iter.Release()
err = iter.Error()
...
```
Iterate over subset of database content:
```go
iter := db.NewIterator(&util.Range{Start: []byte("foo"), Limit: []byte("xoo")}, nil)
for iter.Next() {
	// Use key/value.
	...
}
iter.Release()
err = iter.Error()
...
```
Iterate over subset of database content with a particular prefix:
```go
iter := db.NewIterator(util.BytesPrefix([]byte("foo-")), nil)
for iter.Next() {
	// Use key/value.
	...
}
iter.Release()
err = iter.Error()
...
```
Batch writes:
```go
batch := new(leveldb.Batch)
batch.Put([]byte("foo"), []byte("value"))
batch.Put([]byte("bar"), []byte("another value"))
batch.Delete([]byte("baz"))
err = db.Write(batch, nil)
...
```
Use bloom filter:
```go
o := &opt.Options{
	Filter: filter.NewBloomFilter(10),
}
db, err := leveldb.OpenFile("path/to/db", o)
...
defer db.Close()
...
```
Documentation
-----------

You can read package documentation [here](https://pkg.go.dev/github.com/syndtr/goleveldb).
