# Create and manage dump-style backups

This is a script for creating and managing dump-style differential backups,
using either `xfsdump` or GNU `tar`'s `--listed-incremental` option.

Dump backups have the concept of “levels”, where a level 0 backup is a full
backup, and a level *N* backup contains all files that have been modified since
the last level *N-1* backup.  This script implements such a backup scheme.

## Installation

```console
$ ./tasks/build-manpage
$ sudo ./tasks/install
```

Installation requires `pandoc` to build the man page.

To run, `dumptruck` requires the following:

- `zsh` (`dumptruck` is implemented as a Z shell script).
- `zstd` to compress backups (not configurable).
- `age` to encrypt backups (optional).
- GNU `tar` for the `gnutar` backend (will detect a `gtar` binary if needed,
  e.g. on macOS or *BSD).
- `xfsdump` for the `xfsdump` backend.

## Configuration

`dumptruck` can be configured with command-line options and environment
variables.  The environment variables are particularly useful for scheduling
backups with `systemd`.

See the [man page](dumptruck.md) (`man dumptruck` after installation) for
details on command-line usage.

## Scheduling

When no level is specified, `dumptruck` automatically determines the target
backup level on a schedule:

- Level 0 on the first of the month
- Level 1 on the first of the week
- Level 2 daily

It also adjusts the backup level based on the last backup.  If the first
of the month is on a Tuesday, that will be an L0 backup, and on Wednesday the
last backup was L0 instead of L1, so it takes an L1 backup instead of L2.

## Backup Pruning

The `--cleanup` (`-c`) option tells `dumptruck` to prune old backups after
completing the backup, according to the following retention schedule:

- keep 10 L2 backups
- keep 5 L1 backups
- keep 12 L0 backups

The retention schedule is currently not configurable.