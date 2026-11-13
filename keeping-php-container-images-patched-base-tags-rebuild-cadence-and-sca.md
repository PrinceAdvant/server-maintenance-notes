# Keeping PHP container images patched: base tags, rebuild cadence and scanning

PHP security fixes arrive through two channels: PHP point releases and updates to the OS packages in the base image. A container image built once and never rebuilt gets neither.

## Pick a tag that moves safely

Use a minor version tag such as `php:8.3-fpm-bookworm`. It receives point releases and Debian security updates when the image is republished, without jumping to a new minor version. Avoid `latest`, and avoid pinning a full patch version unless you also have a process to bump it.

## Rebuild on a schedule

Rebuild and redeploy weekly even without code changes, with `docker build --pull` so the newest base image is fetched. A scheduled CI job does this well.

## Know which PHP versions are supported

Each PHP minor version receives active support and then security-only fixes for a limited time. Check the supported versions page on php.net and plan upgrades before your version reaches end of life.

## Scan the image

Run a scanner such as Trivy or Grype on the built image in CI:

```
trivy image --severity HIGH,CRITICAL myapp:latest
```

Fail the build on critical findings that have a fix available.

## Do not forget Composer packages

Run `composer audit` in CI. Framework and library vulnerabilities are more common than PHP core ones.

## Reduce surface

Install only the extensions you use, keep `expose_php = Off` and `display_errors = Off` in production, and run PHP-FPM as a non-root user.

An overview of [PHP vulnerabilities in 2026](https://peon.sh/blogs/php-vulnerabilities-2026) lists recent issues worth knowing about. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.
