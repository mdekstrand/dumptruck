% DUMPTRUCK(1) | v0.1
% Michael Ekstrand
% July 30, 2025

# NAME

**dumptruck** --- make and manage dump-style backups

# SYNOPSIS

**dumptruck** \[_OPTION_\] _PATH_...

# DESCRIPTION

**dumptruck** creates and manages _dump_(8)-style backups of volumes or
directories, automatically handling levels and pruning of old backups.
It supports the following backup methods:

- _xfsdump_(8) (requires a single _PATH_ naming the root of an XFS volume).
- _tar_(1) (accepts multiple paths, requires GNU _tar_ and uses
  `--listed-incremental` to implement differential backups).

Backups are automatically compressed (with _zstd_(1)) and optionally encrypted
(with _age_(1)).  Notably, _dumptruck_ does **not** require access to the private
key at backup time, unlike most encrypted backup tools like _kopia_ or _restic_;
this comes with several tradeoffs, such as no support for deduplication.

# OPTIONS

Following are the options supported by _dumptruck_.  Many can also be configured
with environment variables, listed in parentheses; this is particularly useful
when setting up backups in a _systemd_ service.

**-c, -\-cleanup**
:   Clean up old backups according to the expiration policy.

**-d, -\-backup-dir** _DIR_ (`$BACKUP_DIR`)
:   Store backups in in _DIR_.

**-E, -\-encrypt-to** _FILE_ (`$RECIPIENT_FILE`)
:   Encrypt backups to the Age recipients listed in _FILE_.  Defaults to the file
    _recipients.txt_ in the backup directory.

**-L, -\-level** _LEVEL_ (`$BACKUP_LEVEL`)
:   Specify the backup level (0--9, but usually 0--2).

**-m, -\-method** _METHOD_
:   Use backup method _METHOD_ (**tar** or **xfsdump**).

**-n, -\-dry-run**
:   Don't back up, only print what would be done.

**-N, -\-name** _NAME_ (`$BACKUP_NAME`)
:   Name backups _NAME_ (default: "backup").

**-W, -\-work-dir** _DIR_ (`$BACKUP_WORK`)
:   Persistent work directory for backup cache files (default: _/var/lib/dumptruck_).
    Currently only needed by the **tar** backend (to store the files needed by `--listed-incremental`).

# SEE ALSO

_age_(1)