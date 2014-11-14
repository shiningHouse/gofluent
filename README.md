gofluent
========

This program is something acting like fluentd rewritten in Go.

Introduction
========

Fluentd is originally written in CRuby, which has too many dependecies.

I hope fluentd to be simpler and cleaner, as its main feature is simplicity and rubostness.

So here I go, and need your help!

Installation
========

Follow steps below and have fun!

```
go get github.com/hnlq715/gofluent
go build
```

Architecture
========
```
    +---------+     +---------+     +---------+     +---------+
    | server1 |     | server2 |     | server3 |     | serverN |
    |---------|     |---------|     |---------|     |---------|
    |syslog-ng|     |syslog-ng|     |syslog-ng|     |syslog-ng|
    |---------|     |---------|     |---------|     |---------|
    |gofluent |     |gofluent |     |gofluent |     |gofluent |
    +---------+     +---------+     +---------+     +---------+
        |               |               |               |
         -----------------------------------------------
                                |
                                | HTTP POST
                                V
                        +-----------------+
                        |                 |
                        |      Httpmq     |
                        |                 | 
                        +-----------------+
                                |
                                | HTTP GET
                                V 
                        +-----------------+                 +-----------------+
                        |                 |                 |                 |
                        |   Preprocessor  | --------------> |     Storage     |
                        |                 |                 |                 | 
                        +-----------------+                 +-----------------+
```

Implementation
========
##Overview
```
Input -> Router -> Output
```
##Data flow
```
                        -------<-------- 
                        |               |
                        V               | generate pool
       PipelineConfig.inputRecycleChan  | recycling
            |           |               |        ^   
            | is         ------->-------          \ 
            |               ^           ^          \
    InputRunner.inChan      |           |           \
            |               |      pack.Recycle()    \
            |               |           |             \
    consume |               |           |              \
            V               |           |               \
          Input(Router.inChan) ---->  Router ----> (Router.outChan)Output.inChan

```
