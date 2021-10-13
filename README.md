
# file-starve

[![Total alerts](https://img.shields.io/lgtm/alerts/g/hakavlad/file-starve.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/hakavlad/file-starve/alerts/)

Freeze your Linux system due to eviction of file pages and file cache starvation.

This script makes it possible to artificially reproduce file cache starvation and thrashing by holding a specified amount of disk cache (10 MiB `Active(file)`+`Inactive(file)` by default) for a specified amount of time (60s by default).

## Background

Let's talk about the elephant in the room - the Linux kernel's inability to gracefully handle low memory pressure:
- https://lore.kernel.org/lkml/d9802b6a-949b-b327-c4a6-3dbca485ec20@gmx.com/

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
- In some cases, the UI may not respond even after the specified timeout has expired (the default is 60 seconds).

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

