
# file-starve

[![Total alerts](https://img.shields.io/lgtm/alerts/g/hakavlad/file-starve.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/hakavlad/file-starve/alerts/)

Explore the impact of reducing [page cache](https://www.kernel.org/doc/html/latest/admin-guide/mm/concepts.html#page-cache) size: from moderate [thrashing](https://en.wikipedia.org/wiki/Thrashing_(computer_science)) to a complete UI freeze. This script tries to keep a specified size of LRU file lists (15 MiB by default) within a specified time (60s by default).

## Background

Let's talk about the elephant in the room - the Linux kernel's inability to gracefully handle low memory pressure:
- https://lore.kernel.org/lkml/d9802b6a-949b-b327-c4a6-3dbca485ec20@gmx.com/

>...in normal operation you will have nearly all of your executables nad libraries sitting in good ol' physical RAM. But when RAM runs low, but not low enough for the out-of-memory killer to be run, these pages are evicted from RAM. So you end up with a situation where pages are evicted -- at first, no problem, because they are evicted least-recently-used first and it kicks out pages you aren't using anyway. But then, it kicks out the ones you are using, just to have to page them right back in moments later. Thrash city.

-- https://serverfault.com/a/319818

## Usage

Just run the script. Output example:
```
$ file-starve
starting file-starve
process memory locked with MCL_CURRENT | MCL_ONFAULT
  target file lists (LRU) size:  15.0 MiB
  keep starved (timeout):        60.0s
  MemFree scale factor:          2.0
  chunk size:                    64 KiB
trying to reach the target...
target reached in 6.3s
  file lists size: 15.0M, free: 122.1M, t: 0.0s
  file lists size: 13.6M, free: 244.3M, t: 5.0s
  file lists size: 11.9M, free: 244.2M, t: 10.0s
  file lists size: 12.9M, free: 244.2M, t: 15.0s
  file lists size: 13.0M, free: 244.2M, t: 20.0s
  file lists size: 13.0M, free: 244.4M, t: 25.0s
  file lists size: 14.6M, free: 244.2M, t: 30.0s
  file lists size: 13.2M, free: 244.3M, t: 35.0s
  file lists size: 14.3M, free: 241.4M, t: 40.0s
  file lists size: 11.9M, free: 203.9M, t: 45.0s
  file lists size: 13.0M, free: 241.6M, t: 50.0s
  file lists size: 14.0M, free: 241.6M, t: 55.0s
  file lists size: 11.6M, free: 241.5M, t: 60.0s
kept starved during 60.0s
file lists and free memory sizes after reaching the target:
  file: min=9.5M, max=16.4M, average=13.4M
  free: min=120.6M, max=261.6M, average=238.4M
```

## Options

```
$ file-starve -h
usage: file-starve [-h] [-t TARGET] [-T TIMEOUT] [-s SCALE] [-c CHUNK] [-i INTERVAL]

optional arguments:
  -h, --help            show this help message and exit
  -t TARGET, --target TARGET
                        target size of file cache in MiB
  -T TIMEOUT, --timeout TIMEOUT
                        timeout in seconds after reaching the target
  -s SCALE, --scale SCALE
                        MemFree scale factor
  -c CHUNK, --chunk CHUNK
                        chunk size in KiB
  -i INTERVAL, --interval INTERVAL
                        output interval in seconds
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

