[![godoc](http://img.shields.io/badge/godev-reference-blue.svg?style=flat)](https://pkg.go.dev/github.com/solutionroute/sid?tab=doc)[![Build Status](https://travis-ci.org/solutionroute/sid.svg?branch=master)](https://travis-ci.org/solutionroute/sid)[![Go Coverage](https://img.shields.io/badge/coverage-98.3%25-brightgreen.svg?style=flat)](http://gocover.io/github.com/solutionroute/sid)[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# sid

Package sid provides a no-confgiuration needed ID generator producing compact, 
unique-enough (65535 per millisecond), URL and human-friendly IDs that look like `af1zwtepacw38`.

The 8-byte ID binary representation of ID is composed of a:

- 6-byte timestamp value representing _milliseconds_ since the Unix epoch
- 2-byte concurrency-safe counter (test included)

If for some reason your application needs to produce more than 65,535 new IDs
per _millisecond_, or has a distributed execution requirement, this ID generator is
not the one you are looking for. May the force be with you!

The encoded `String()` representations of ID are 13 characters long, using a
lowercase Base32 character set, chronologically
[k-sortable](https://en.wikipedia.org/wiki/Partial_sorting), and look like:
`af1zwtepacw38`.

Also included: `cmd/sid` provides a simple tool to generate or inspect SIDs.

## Example Use

```go
package main

import (
   "fmt"
    "github.com/solutionroute/sid"
)

func main() {
    id := sid.New()
    fmt.Printf(`ID:
    String()       %s   // af3gs6jgqrm4g
    Milliseconds() %d   // 1590881748587
    Count()        %d   // 38307
    Time()         %v   // 2020-05-30 16:35:48.587 -0700 PDT
    Bytes():       %3v  // [  1 114 103 241   6 107 149 163]
`, id.String(), id.Milliseconds(), id.Count(), id.Time(), id.Bytes())

    if id, err := sid.FromString("af1zwtepacw38"); err == nil {
        fmt.Printf("ID: %s Timestamp (ms): %d Count: %d\n", id, id.Milliseconds(), id.Count())
    } // Output: ID: af1zwtepacw38 Timestamp (ms): 1577750400000 Count: 42399
}
```

## Motivation

So why this? I had a need for a human and URL friendly ID generator and looked
at how ID generation was being tackled by others. Having no need for
distributed-capable globally-unique IDs, and wanting a shorter string
representation (13 chars vs 20 or more), "sid" was born.

## Acknowledgments

Much of this package was based on [rs/xid](https://github.com/rs/xid), which
offers a globally-unique ID inspired by 
[MongoDB](https://docs.mongodb.com/manual/reference/method/ObjectId/).

[oklog/ulid](https://github.com/oklog/ulid)'s use of millisecond-resolution
timestamps was a good fit; independently, also came to choose [Crockford's
Base32 character set](https://en.wikipedia.org/wiki/Base32#Crockford's_Base32) for 
readability, and concluded sortability was a win over the purposefully random 
looking [HashIDs](https://github.com/speps/go-hashids).

[Generating good unique IDs in
Go](https://blog.kowalczyk.info/article/JyRZ/generating-good-unique-ids-in-go.html) provided additional inspiration.
