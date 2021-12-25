
# file-starve

[![Total alerts](https://img.shields.io/lgtm/alerts/g/hakavlad/file-starve.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/hakavlad/file-starve/alerts/)

Explore the impact of reducing [page cache](https://www.kernel.org/doc/html/latest/admin-guide/mm/concepts.html#page-cache) size: from moderate [thrashing](https://en.wikipedia.org/wiki/Thrashing_(computer_science)) to a complete UI freeze. This script tries to keep a specified size of file LRU lists (20 MiB by default) within a specified time (60s by default).

## Background

Let's talk about the elephant in the room - the Linux kernel's inability to gracefully handle low memory pressure:
- https://lore.kernel.org/lkml/d9802b6a-949b-b327-c4a6-3dbca485ec20@gmx.com/

>...in normal operation you will have nearly all of your executables nad libraries sitting in good ol' physical RAM. But when RAM runs low, but not low enough for the out-of-memory killer to be run, these pages are evicted from RAM. So you end up with a situation where pages are evicted -- at first, no problem, because they are evicted least-recently-used first and it kicks out pages you aren't using anyway. But then, it kicks out the ones you are using, just to have to page them right back in moments later. Thrash city.

-- https://serverfault.com/a/319818

## Options

```
$ file-starve -h
usage: file-starve [-h] [-t TARGET] [-T TIMEOUT] [-s SCALE] [-c CHUNK] [-i INTERVAL] [-d DEV]

optional arguments:
  -h, --help            show this help message and exit
  -t TARGET, --target TARGET
                        target size of file LRU lists in MiB
  -T TIMEOUT, --timeout TIMEOUT
                        timeout in seconds after reaching the target
  -s SCALE, --scale SCALE
                        MemFree scale factor
  -c CHUNK, --chunk CHUNK
                        chunk size in KiB
  -i INTERVAL, --interval INTERVAL
                        output interval in seconds
  -d DEV, --dev DEV     device name to check I/O stats
```

## Usage

Just run the script. Output example:
```
$ file-starve
starting file-starve
W: device name to check I/O stats is not set
process memory locked with MCL_CURRENT | MCL_ONFAULT
  target file LRU lists size: 20.0M
  keep starved (timeout):     60.0s
  MemFree scale factor:       2.0
  chunk size:                 64K
trying to reach the target...
target reached in 6.6s
  file: 19.8M, free: 65.4M, passed: 0.0s
  file: 18.3M, free: 129.7M, passed: 10.0s
  file: 19.1M, free: 129.3M, passed: 20.0s
  file: 19.6M, free: 129.2M, passed: 30.0s
  file: 19.8M, free: 129.2M, passed: 40.0s
  file: 16.9M, free: 116.9M, passed: 50.0s
  file: 20.3M, free: 111.2M, passed: 60.0s
kept starved during 60.0s
file lists and free memory sizes after reaching the target:
  file: min=13.4M, max=21.3M, average=18.2M
  free: min=64.6M, max=131.8M, average=126.7M
vmstat I/O metrics for the last 60.0s:
  pgpgin:  559.9M, 9.3M/s
  pgpgout: 0.0M, 0.0M/s
  pswpin:  0.0M, 0.0M/s
  pswpout: 0.0M, 0.0M/s
PSI metrics for the last 60.0s:
  some io:     55.3s, 92.2%
  full io:     49.5s, 82.5%
  some memory: 27.7s, 46.1%
  full memory: 26.4s, 44.0%
```

Optionally, you can specify a device name for tracking I/O stats:
```
$ file-starve -t 40 -T 30 -i 15 -c 32 -d sdb -s 1.5
starting file-starve
device to check I/O stats: sdb
process memory locked with MCL_CURRENT | MCL_ONFAULT
  target file LRU lists size: 40.0M
  keep starved (timeout):     30.0s
  MemFree scale factor:       1.5
  chunk size:                 32K
trying to reach the target...
target reached in 8.8s
  file: 39.9M, free: 54.8M, passed: 0.0s
  file: 22.4M, free: 77.1M, passed: 15.0s
  file: 28.1M, free: 74.8M, passed: 30.0s
  file: 28.1M, free: 74.8M, passed: 30.0s
kept starved during 30.0s
file lists and free memory sizes after reaching the target:
  file: min=11.7M, max=40.7M, average=31.5M
  free: min=49.7M, max=86.2M, average=76.3M
vmstat I/O metrics for the last 30.0s:
  pgpgin:  264.6M, 8.8M/s
  pgpgout: 0.0M, 0.0M/s
  pswpin:  0.0M, 0.0M/s
  pswpout: 0.0M, 0.0M/s
PSI metrics for the last 30.0s:
  some io:     25.3s, 84.2%
  full io:     24.3s, 81.1%
  some memory: 13.9s, 46.3%
  full memory: 13.6s, 45.2%
I/O statistics for sdb device in the last 30.0s:
  util: 29.8s, 99.2%
  read: 263.1M, 8.8M/s
  read: 6080 requests processed, 202.7 rp/s
  read: await 946.7s, 3155.6%
  write: 0.0M, 0.0M/s
  write: 11 requests processed, 0.4 rp/s
  write: await 0.7s, 2.2%
```

## Warnings

- The UI may not respond while the script is running.
- In some cases, the UI may not respond even after the specified timeout has expired.

## Requirements

- Python >= 3.3

## Installation

Install
```bash
$ git clone https://github.com/hakavlad/file-starve.git && cd file-starve
$ sudo make install
```

Uninstall
```bash
$ sudo make uninstall
```

## See also

- Lock executables and shared libraries in memory to improve system responsiveness under low-memory conditions
    - https://github.com/hakavlad/prelockd
- [PATCH] mm: Protect the working set under memory pressure to prevent thrashing, avoid high latency and prevent livelock in near-OOM conditions
    - https://github.com/hakavlad/le9-patch

