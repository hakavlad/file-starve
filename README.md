
# file-starve

[![Total alerts](https://img.shields.io/lgtm/alerts/g/hakavlad/file-starve.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/hakavlad/file-starve/alerts/)

Explore the impact of reducing [page cache](https://www.kernel.org/doc/html/latest/admin-guide/mm/concepts.html#page-cache) size: from moderate [thrashing](https://en.wikipedia.org/wiki/Thrashing_(computer_science)) to a complete UI freeze. This script tries to keep a specified size of LRU file lists (15 MiB by default) within a specified time (60s by default).

## Background

Let's talk about the elephant in the room - the Linux kernel's inability to gracefully handle low memory pressure:
- https://lore.kernel.org/lkml/d9802b6a-949b-b327-c4a6-3dbca485ec20@gmx.com/

>...in normal operation you will have nearly all of your executables nad libraries sitting in good ol' physical RAM. But when RAM runs low, but not low enough for the out-of-memory killer to be run, these pages are evicted from RAM. So you end up with a situation where pages are evicted -- at first, no problem, because they are evicted least-recently-used first and it kicks out pages you aren't using anyway. But then, it kicks out the ones you are using, just to have to page them right back in moments later. Thrash city.

-- https://serverfault.com/a/319818

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

## Usage

Just run the script. Output example:
```
$ file-starve
process memory locked with MCL_CURRENT | MCL_ONFAULT
  target file lists (LRU) size:  15.0M
  keep starved (timeout):        60.0s
  MemFree scale factor:          2.0
  chunk size:                    64K
trying to reach the target...
target reached in 7.1s
  file: 14.4M, free: 136.2M, passed: 0.0s
  file: 12.5M, free: 251.1M, passed: 10.0s
  file: 14.1M, free: 247.5M, passed: 20.0s
  file: 12.4M, free: 247.4M, passed: 30.0s
  file: 15.0M, free: 241.7M, passed: 40.0s
  file: 12.6M, free: 245.1M, passed: 50.0s
  file: 15.3M, free: 205.0M, passed: 60.0s
kept starved during 60.0s
file lists and free memory sizes after reaching the target:
  file: min=10.3M, max=17.3M, average=13.5M
  free: min=122.5M, max=272.8M, average=237.4M
vmstat values in the last 60.0s:
  pgpgin:  607.8M, avg: 10.1M/s (paged in from disk)
  pgpgout: 0.0M, avg: 0.0M/s (paged out to disk)
  pswpin:  0.0M, avg: 0.0M/s (swapped in)
  pswpout: 0.0M, avg: 0.0M/s (swapped out)
pressure stall information in the last 60.0s:
  some io:      50.6s, 84.3%
  full io:      48.0s, 80.0%
  some memory:  14.9s, 24.8%
  full memory:  14.7s, 24.5%
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

