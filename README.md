BufDB 
==========

[![GoDoc](https://godoc.org/github.com/djherbis/bufdb?status.svg)](https://godoc.org/github.com/djherbis/bufdb)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE.txt)
[![Build Status](https://travis-ci.org/djherbis/bufdb.svg?branch=master)](https://travis-ci.org/djherbis/bufdb) 
[![Coverage Status](https://coveralls.io/repos/djherbis/bufdb/badge.svg?branch=master)](https://coveralls.io/r/djherbis/bufdb?branch=master)

Usage
------------

This package provides a persistence manager for github.com/djherbis/buffer Buffers.

```go
import (
  "bytes"
  "io/ioutil"
  "os"
  "testing"

  "github.com/boltdb/bolt"
  "github.com/djherbis/buffer"
)

func TestStore(t *testing.T) {
  db, err := bolt.Open("my.db", 0600, nil)
  if err != nil {
    t.Error(err.Error())
  }
  defer os.Remove("my.db")
  defer db.Close()

  store := NewBufferStore(db, []byte("buckets"))

  key := []byte("hello")
  input := []byte("hello world")

  b := buffer.NewPartition(buffer.NewFilePool(10, "."))
  b.Write(input)

  store.Put(key, b)
  buf, err := store.Pull(key)
  if err != nil {
    t.Error(err.Error())
  }

  data, err := ioutil.ReadAll(buf)

  if !bytes.Equal(data, input) {
    t.Errorf("expected %s, got %s", input, data)
  }

  store.Wipe()
}
```

Installation
------------
```sh
go get github.com/djherbis/bufdb
```
