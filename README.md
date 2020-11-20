# tunasync

![Build Status](https://github.com/tuna/tunasync/workflows/tunasync/badge.svg)
[![Coverage Status](https://coveralls.io/repos/github/tuna/tunasync/badge.svg?branch=master)](https://coveralls.io/github/tuna/tunasync?branch=master)
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)
![GPLv3](https://img.shields.io/badge/license-GPLv3-blue.svg)

## Get Started

- [中文文档](https://github.com/tuna/tunasync/blob/master/docs/zh_CN/get_started.md)

## Download

Pre-built binary for Linux x86_64 and ARM64 is available at [Github releases](https://github.com/tuna/tunasync/releases/latest).

## Design

```text
# Architecture

- Manager: Central instance for status and job management
- Worker: Runs mirror jobs

+------------+ +---+                  +---+
| Client API | |   |    Job Status    |   |    +----------+     +----------+ 
+------------+ |   +----------------->|   |--->|  mirror  +---->|  mirror  | 
+------------+ |   |                  | w |    |  config  |     | provider | 
| Worker API | | H |                  | o |    +----------+     +----+-----+ 
+------------+ | T |   Job Control    | r |                          |       
+------------+ | T +----------------->| k |       +------------+     |       
| Job/Status | | P |   Start/Stop/... | e |       | mirror job |<----+       
| Management | | S |                  | r |       +------^-----+             
+------------+ |   |   Update Status  |   |    +---------+---------+         
+------------+ |   <------------------+   |    |     Scheduler     |
|   BoltDB   | |   |                  |   |    +-------------------+
+------------+ +---+                  +---+


# Job Run Process


PreSyncing                           Syncing                               Success
+-----------+     +----------+    +-----------+    +-------------+     +--------------+
|  pre-job  +--+->| pre-exec +--->|  job run  +--->|  post-exec  +-+-->| post-success |
+-----------+  ^  +----------+    +-----------+    +-------------+ |   +--------------+
               |                                                   |
               |                +-----------------+                | Failed
               +----------------+    post-fail    |<---------------+
                                +-----------------+
```

## Filecoin

### Storage for Filecoin

Calculate the CID of the file, establish the index, retrieve the filecoin network, whether there are n copies. If so, discard the storage. Otherwise, save the file to the filecoin until the number of copies is n.

### Syncing for Filecoin

In order to save disk space, the server will cache the latest n-year production files, and automatically clean up the expired files. When users need to retrieve data, they will synchronize the data from the filecoin network and distribute the data to the client.

## Building

Go version: 1.13

```shell
> make all
```

Binaries in the `build-linux-amd64/`.
