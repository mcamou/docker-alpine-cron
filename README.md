# docker-alpine-cron

Dockerfile and scripts for creating image with Cron based on Alpine. It adds the following packages:

* `dcron`
* `curl`
* `wget`
* `rsync`
* `ca-certificates`

## Usage

### Environment variables

**CRON_STRINGS** - strings with cron jobs. Use "\n" for newline (Default: undefined).
**CRON_TAIL** - if defined the cron log file will be sent to *stdout* by *tail*. Additionally, if set to the value *no_logfile*, no log file will be created and logging will be to *stdout* only. (Default: undefined).
**CRON_CMD_OUTPUT_LOG** - if defined the output of the commands executed by cron will also be sent to *stdout*, otherwise they will be ignored (Default: undefined).
**LOG_LEVEL** - cron daemon log level. See [dubiousjim/dcron](https://github.com/dubiousjim/dcron/blob/master/main.c#L51) for allowable values. The default is `notice`.

`crond` always runs in the foreground

### Cron files

`/etc/cron.d` - place to mount custom crontab files

When the image runs, any files in */etc/cron.d* will copied to */var/spool/cron/crontab*.
If *CRON_STRINGS* is defined the image will create the file */var/spool/cron/crontab/CRON_STRINGS* 

### Log files

The log file will be placed in `/var/log/cron/cron.log`, unless `CRON_TAIL` is set to `no_logfile`.

### Simple usage

```bash
docker run --name="alpine-cron-sample" -d \
-v /path/to/app/conf/crontabs:/etc/cron.d \
-v /path/to/app/scripts:/scripts \
mcamou/docker-alpine-cron
```

### With scripts and CRON_STRINGS

```bash
docker run --name="alpine-cron-sample" -d \
-e 'CRON_STRINGS=* * * * * /scripts/myapp-script.sh'
-v /path/to/app/scripts:/scripts \
mcamou/docker-alpine-cron
```

### Get URL by cron every minute

```bash
docker run --name="alpine-cron-sample" -d \
-e 'CRON_STRINGS=* * * * * wget --spider https://sample.dockerhost/cron-jobs'
mcamou/docker-alpine-cron
```

## Building

`mcamou/docker-alpine-cron` is a multi-platform image. To properly create it you need to use [buildx](https://www.docker.com/blog/multi-platform-docker-builds/) and QEMU:

```bash
docker buildx build --platform $(echo linux/{amd64,arm64,arm/v7,arm/v6}|sed 's/ /,/g') --tag mcamou/docker-alpine-cron:latest --push .
```
