btrfs-snapshots-diff
====================

About
-----
`btrfs-snapshots-diff.py` is a simple Python3 script that displays the differences 
between two Btrfs snapshots (from the same subvolume obviously).

Uses `btrfs send --no-data` to compute the differences, decodes the stream and 
displays the differences.

Usage
-----
       usage: btrfs-snapshots-diff.py [options] -p PARENT -c CHILD
              btrfs-snapshots-diff.py [options] -f FILE
    
       Display differences between 2 Btrfs snapshots
    
       optional arguments:
         -h, --help            Show this help message and exit
         -p, --parent PATH     Path to PARENT (older) snapshot (must be readonly)
         -c, --child PATH      Path to CHILD (newer) snapshot 
         -f, --file FILE       Path to diff file generated by 
                               "btrfs send --no-data -p first second > FILE"
         -t, --filter          Do not display temporary files or any time modifications (just latest)
         -a, --by_path         Group commands by path
         -s, --csv             CSV output
         -j, --json            JSON output (commands only)
         -b, --bogus           Add bogus renamed_from action (used only when grouping by path)


* `--json` (`-j`), available for commands only, will output a list of 
commands in JSON format.

* `--csv` (`-s`) will produce on line for each modification, instead of 
formatted output: the first column is the path, then each action taken on the 
file is in a new column. Separator is ";".

* `--by_path` (`-a`) groups command by path, giving a better view of what 
happenned on the file system.

* `--bogus` (`-b`)  adds a bogus command to the stream, to better track renaming of 
files / dir (only usefull with `--by_path`).

* With option `--filter` (`-t`), the script tries to be a bit smarter (only usefull 
with `--by_path`):
    * it does not display temporary files created by send stream,
    * it displays 'created' or 'rewritten' on the files renamed from temporary files,
    * it displays only the latest time modifications, if there are two or more.

Example
-------
See [./example.output](./example.output) file as the example of actions made by [./create-example.sh](./create-example.sh): 

```bash
touch file
mkdir dir
mkfifo fifo
ln file hardlink
ln -s file symlink
echo 'Hello Btrfs' > 'xxx;yyy;zzz'
mv file file2
```

Tests
-----

Verify all tests by running: 

```bash
./test.sh
```

Requirements
------------
No requirements besides Python-3 (>=3.6) and `btrfs` command obviously.

Bugs
----
There are probably bugs, though it works for me on my own snapshots.


License
-------
GPL v2, see LICENSE file.

Made in beautiful Tahiti (French Polynesia) by [SysNux](http://www.sysnux.pf/ "Systèmes Linux en Polynésie française").

Copyright (c) 2016 Jean-Denis Girard <jd.girard@sysnux.pf>
