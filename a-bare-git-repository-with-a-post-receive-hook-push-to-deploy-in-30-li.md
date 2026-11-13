# A bare Git repository with a post-receive hook: push to deploy in 30 lines

Before reaching for a platform, it helps to know how little push to deploy actually needs. This version keeps each release in its own directory so rollback is one command.

## On the server

```
git init --bare /srv/git/app.git
mkdir -p /srv/app/releases
```

Create `/srv/git/app.git/hooks/post-receive` and make it executable:

```
#!/bin/sh
set -eu
while read old new ref; do
  [ "$ref" = "refs/heads/main" ] || continue
  rel=/srv/app/releases/$(date +%Y%m%d%H%M%S)
  mkdir -p "$rel"
  git --work-tree="$rel" --git-dir=/srv/git/app.git checkout -f "$new"
  cd "$rel"
  docker compose -p app build
  ln -sfn "$rel" /srv/app/current.tmp && mv -T /srv/app/current.tmp /srv/app/current
  cd /srv/app/current && docker compose -p app up -d
  ls -1dt /srv/app/releases/* | tail -n +6 | xargs -r rm -rf
done
```

## On your machine

```
git remote add prod deploy@server:/srv/git/app.git
git push prod main
```

## Rollback

Point `current` at the previous release and run `docker compose up -d` again. The `mv -T` swap makes the switch atomic, so there is never a half-updated directory.

## What this leaves out

Secrets handling, health checks before switching, build logs you can read later, notifications and team permissions. Those are the parts a deployment platform adds, and the reason most teams eventually stop maintaining the hook.

A complete guide to [Git push to deploy on your own server](https://peon.sh/blogs/git-push-to-deploy-own-server) compares this with webhook based setups. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.
