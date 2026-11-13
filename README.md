# Server maintenance notes

Short, practical notes on keeping self-hosted servers, containers and deploy pipelines healthy. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.

- [fail2ban banned my own IP: finding and lifting the ban](fail2ban-banned-my-own-ip-finding-and-lifting-the-ban.md): How to tell that fail2ban is behind a refused SSH connection, unban yourself and stop it from happening again.
- [S3-compatible storage for Docker app uploads: SDK settings that trip people up](s3-compatible-storage-for-docker-app-uploads-sdk-settings-that-trip-pe.md): The endpoint, region and path-style settings that make AWS SDKs work with self-hosted S3-compatible storage.
- [Push monitors in Uptime Kuma for cron jobs and backups](push-monitors-in-uptime-kuma-for-cron-jobs-and-backups.md): Using Uptime Kuma push monitors so a silent cron job or failed backup raises an alert.
- [A bare Git repository with a post-receive hook: push to deploy in 30 lines](a-bare-git-repository-with-a-post-receive-hook-push-to-deploy-in-30-li.md): The classic Git push deployment with release directories and an atomic symlink switch for quick rollbacks.
- [Cache headers for a static site on your own server: hashed assets versus HTML](cache-headers-for-a-static-site-on-your-own-server-hashed-assets-versu.md): Setting long cache lifetimes for fingerprinted files and short ones for HTML so deploys show up immediately.
- [Keeping PHP container images patched: base tags, rebuild cadence and scanning](keeping-php-container-images-patched-base-tags-rebuild-cadence-and-sca.md): A routine for keeping PHP application images current with security fixes without surprise upgrades.
- [Ordering deploy steps: migrations, health checks and the traffic switch](ordering-deploy-steps-migrations-health-checks-and-the-traffic-switch.md): A safe order for the steps in a container deploy so a bad release never takes traffic.
- [Rotating API tokens used by CI without a failed deploy](rotating-api-tokens-used-by-ci-without-a-failed-deploy.md): A two-token overlap method for rotating deploy API tokens and webhook secrets used by CI pipelines.

Licensed CC BY 4.0.
