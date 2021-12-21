
# file-starve

[![Total alerts](https://img.shields.io/lgtm/alerts/g/hakavlad/file-starve.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/hakavlad/file-starve/alerts/)

Explore the impact of reducing [page cache](https://www.kernel.org/doc/html/latest/admin-guide/mm/concepts.html#page-cache) size: from moderate [thrashing](https://en.wikipedia.org/wiki/Thrashing_(computer_science)) to a complete UI freeze. This script tries to keep a specified size of LRU file lists (10 MiB by default) within a specified time (60s by default).

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
target reached
  file lists size: 14.7M, free: 122.2M, t: 0.0s
  file lists size: 14.1M, free: 244.4M, t: 5.0s
  file lists size: 15.1M, free: 199.0M, t: 10.0s
  file lists size: 10.1M, free: 244.5M, t: 15.0s
  file lists size: 14.8M, free: 244.5M, t: 20.0s
  file lists size: 12.7M, free: 244.4M, t: 25.0s
  file lists size: 12.7M, free: 244.5M, t: 30.0s
  file lists size: 13.3M, free: 244.4M, t: 35.0s
  file lists size: 12.5M, free: 244.4M, t: 40.0s
  file lists size: 13.3M, free: 244.4M, t: 45.0s
  file lists size: 14.1M, free: 244.5M, t: 50.0s
  file lists size: 11.5M, free: 244.5M, t: 55.0s
  file lists size: 11.5M, free: 244.5M, t: 60.0s
timeout expired: 60.0s have passed after reaching the target
file lists and free memory sizes after reaching the target:
  file: min=9.0M, max=16.5M, average=12.9M
  free: min=122.2M, max=247.9M, average=240.6M
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

