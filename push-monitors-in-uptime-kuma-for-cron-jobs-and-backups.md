# Push monitors in Uptime Kuma for cron jobs and backups

Most Uptime Kuma monitors check something from the outside: an HTTP endpoint, a TCP port, a DNS record. Scheduled jobs need the opposite. A push monitor waits for the job to check in and alerts when it does not.

## Create the monitor

Add a monitor of type Push. Uptime Kuma shows a unique URL. Set the heartbeat interval to slightly longer than the job's schedule, for example 25 hours for a daily backup, so normal variation does not trigger alerts.

## Call it from the job

At the end of a successful run:

```
pg_dump ... | gzip > /backups/app.sql.gz && \
curl -fsS -m 10 --retry 3 "https://status.example.com/api/push/AbC123?status=up&msg=OK"
```

Because of `&&`, the push only happens if the backup succeeded. A failed dump means no heartbeat, which means an alert.

## Report failures explicitly

You can also push a down status with a message when the job knows it failed:

```
curl -fsS "https://status.example.com/api/push/AbC123?status=down&msg=disk%20full"
```

## Useful jobs to watch

- Database backups and their upload to object storage.
- Certificate renewal hooks.
- Log rotation and cleanup scripts.
- Scheduled imports and reports.

## Keep the token private

The push URL is a credential. Store it in the job's environment rather than in a script committed to Git.

The guide to [self-host Uptime Kuma](https://peon.sh/blogs/self-host-uptime-kuma) covers installation and notifications. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.
