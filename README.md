# xtrabackup snap package

[Snap](https://snapcraft.io) package for xtrabackup open source backup tool by Percona.

## Build

```console
snapcraft
```

## Install

```console
sudo snap install ./xtrabackup_8.0.30-23_amd64.snap --dangerous
```

## Usage

It's necessary to manually connect the `system-backup` interface:

```console
sudo snap connect xtrabackup:system-backup
```

A example script for backing up to S3 is:

```bash
USER="<user>"
PASSWORD="<password>"
S3_BUCKET="backups"
S3_PATH="$(date -Iminutes)-backup"
S3_KEY="<s3-key>"
S3_SECRET="<s3-secret>"
HOST="127.0.0.1"

xtrabackup --defaults-group=mysql --no-version-check \
    --parallel=$(grep -c processor /proc/cpuinfo) \
    --user=${USER} \
    --password=${PASSWORD} \
    --host=${HOST} \
    --lock-ddl \
    --no-server-version-check \
    --backup \
    --stream=xbstream \
    --xtrabackup-plugin-dir=/usr/lib/mysql/plugin \
    --target-dir=/tmp | \
    xtrabackup.xbcloud put \
    --curl-retriable-errors=7 --insecure --storage=s3 --parallel=10 --md5 \
    --s3-bucket="$S3_BUCKET" \
    --s3-access-key=$S3_KEY \
    --s3-secret-key=$S3_SECRET \
    "$S3_PATH"
```


Refer to [official docs from Percona](https://www.percona.com/doc/percona-xtrabackup/2.1/manual.html)

