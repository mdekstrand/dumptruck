% DUMPTRUCK(8) | v0.1
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

xfsdump
:   Uses _xfsdump_(8) to dump the file system.  Only works with a single
    _PATH_, which must be the root of an XFS volume.

gnutar
:   Uses GNU _tar_(1)  to archive one or more paths, using
    `--listed-incremental` to implement differential dump-style backups.

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
:   Specify the backup level (0--9, but usually 0--2).  The default is automatically
    detected according to a backup schedule.

**-m, -\-method** _METHOD_ (`$BACKUP_METHOD`)
:   Use backup method _METHOD_ (**gnutar** or **xfsdump**).  Defaults to **gnutar**.

**-n, -\-dry-run**
:   Don't back up, only print what would be done.

**-N, -\-name** _NAME_ (`$BACKUP_NAME`)
:   Name backups _NAME_ (default: "backup").

**-\-rage**
:   Encrypt with _rage_(1) instead of _age_(1).  Support for _rage_ can also be
    invoked by setting the environment variable `AGE=rage`.

**-W, -\-work-dir** _DIR_ (`$BACKUP_WORK`)
:   Persistent work directory for backup cache files (default: _/var/lib/dumptruck_).
    Currently only needed by the **tar** backend (to store the files needed by `--listed-incremental`).

# REQUIREMENTS

**dumptruck** requires the following software installed:

- _zsh_(1) (_dumptruck_ is implemented as a Z shell script)
- _zstd_(1) to compress backups
- _age_(1) to encrypt backups
- GNU _tar_(1), when using the **tar** backup method
- _xfsdump_(8), when using the **xfsdump** backup method

# BUGS

- Only _zstd_(1) compression is supported (but why use anything else?).
- The expiration policy cannot yet be configured.
- The backup schedule cannot yet be configured.
- BSD _dump_(8) is not yet supported. There is no reason why it shouldn't work,
  it just hasn't been tested.

# SEE ALSO

_age_(1)
