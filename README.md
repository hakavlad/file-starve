
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
Starting file-starve
  target file cache size:             10 MiB
  timeout after reaching the target:  60.0 sec
  MemFree scale factor:               2
  chunk size:                         64 KiB
User defined signal 1
```

## Options

```
$ file-starve -h
usage: file-starve [-h] [-t TARGET] [-T TIMEOUT] [-s SCALE] [-c CHUNK]

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

