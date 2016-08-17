django-backup
=============

.. image:: https://travis-ci.org/django-backup/django-backup.svg?branch=develop
    :target: https://travis-ci.org/django-backup/django-backup

http://github.com/django-backup/django-backup

Backup, compress and restore database and media files. Transfer them via email or FTP and maintain a set number of dated versions on remote FTP server.

Authors
-------

* project started by Dmitriy Kovalev (http://code.google.com/p/django-backup/ http://code.google.com/u/dmitriy.kovalev/)
* based off of backupdb command by msaelices (http://www.djangosnippets.org/snippets/823/)
* and also snippets from http://www.yashh.com/blog/2008/sep/05/django-database-backup-view/
* with minor modifications by Michael Huynh (mike@mikexstudios.com) http://github.com/mikexstudios/django-backup
* Major modifications in this fork by Andy Baker (andy@andybak.net and Chen Zhe (fruitschen@gmail.com)
* Lots more improvements including tests and packaging by Horst Gutmann (https://zerokspot.com/weblog/)


New Features in this fork
-------------------------

- Facility to backup media directories in addition to backing up SQL dump
- Transfer backups to remote FTP site
- cleanmedia and cleandb options allow you to only retain a set number of backups on the remote ftp site. Can specify via settings different values for days, weeks and years to retain (see below)
- 'manage.py restore' pulls down the latest backup from FTP and feeds it to mysql
- option to delete alllocal backups
- if using FTP you can opt not to retain local copy of backups
- Unfortunately Postgres support hasn't been kept up to date in this version. It shouldn't be that hard to replace.


Supported options for manage.py backup
--------------------------------------

::

    --email
    default=None
    Sends email with attached dump file

    --compress -c
    default=False
    Compress SQL dump file

    --zipencrypt -z
    default=False
    Uses zip to package the backup and encrypts it with a password
    provided in the BACKUP_PASSWORD environment variable.

    --ftp -f
    default=False
    Store backup on remote FTP server

    --no-database -d
    default=False
    Don't restore the database from the remote server
    (useful if you just want the media)

    --media -m
    default=False
    Backup media dirs as well as SQL dump

    --rsync -r
    default=False
    Backup media dirs with rsync

    --nolocal
    default=False
    Keep local copies of backup

    --deletelocal
    default=False
    Delete all local backups

    --cleandb
    default=False
    Clean up surplus database backups

    --cleanmedia
    default=False
    Clean up surplus media backups

    --cleanlocaldb
    default=False
    Clean up surplus local database backups

    --cleanlocalmedia
    default=False
    Clean up surplus local media backups

    --cleanremotedb
    default=False
    Clean up surplus remote database backups

    --cleanremotemedia
    default=False
    Clean up surplus remote media backups

    --cleanrsync
    default=False
    Clean up broken rsync backups

    --cleanlocalrsync
    default=False
    Clean up local broken rsync backups

    --cleanremotersync
    default=False
    Clean up remote broken rsync backups

When rsync flag is combined with ftp flag data will be backed up using rsync to a remote server.
When rsync flag is used without the ftp flag data will be backed up to the local machine.

Extra Settings
--------------
::

  BACKUP_SQLDUMP_PATH = '/path/to/mysqldump' # mysqldump binary location
  BACKUP_LOCAL_DIRECTORY = '/path/to/backups' # Where to store local backups

  BACKUP_FTP_SERVER = 'example.com'
  BACKUP_FTP_USERNAME = 'username'
  BACKUP_FTP_PASSWORD = 'password'
  BACKUP_FTP_DIRECTORY = '/path/to/backups/mysite' # If you store multiple backups on the same remote server ensure each one is in a different directory
  RESTORE_FROM_FTP_DIRECTORY = '/path/to/backups/mysite' # Where does the restore

  # How many db backups should we keep on remote FTP? i.e. 1 per day for the last 7 days plus 1 per week for the last 4 weeks etc.
  BACKUP_DATABASE_COPIES = {
     'daily': 7,
     'weekly': 4,
     'monthly': 12,
  }

  # Same as above
  BACKUP_MEDIA_COPIES = {
     'daily': 1,
     'weekly': 2,
     'monthly': 4,
  }

Note that the settings which include FTP in their name will also be used for rsync.

Examples
--------------

  A db-only backup
    python manage.py backup --ftp

  db plus rsync media backup
    python manage.py backup --media --rsync --ftp

  db plus SFTP media backup
    python manage.py backup --media --ftp

  Restore the most recent backup including media
    python manage.py restore --media

  db plus rsync media backup, validate remote rsync backups, clearn surplus media and db backs, and do not keep local copies of backups.
    python manage.py backup --media --rsync --ftp --deletelocal --cleanremotedb --cleanremotemedia --cleanremotersync

    or

    call_command("backup", ftp=True, media=True, delete_local=True, clean_remote_db=True, clean_remote_media=True, clean_remote_rsync=True)

