# fail2ban banned my own IP: finding and lifting the ban

A few wrong key attempts from a script, a misconfigured CI job or a shared office IP, and fail2ban blocks you from your own server. The symptom looks like a network problem, so it is easy to waste time in the wrong place.

## Recognise it

- SSH from your usual network times out or is refused, but works from your phone hotspot or another server.
- Your provider's web console still works.
- The problem started right after several failed logins.

## Confirm from the console

```
sudo fail2ban-client status sshd
```

The output lists currently banned IPs. If yours is there, that is the cause.

## Lift the ban

```
sudo fail2ban-client set sshd unbanip 203.0.113.10
```

## Stop it happening again

Add trusted addresses to `ignoreip` in `/etc/fail2ban/jail.local`:

```
[DEFAULT]
ignoreip = 127.0.0.1/8 ::1 203.0.113.10
```

Then `sudo systemctl restart fail2ban`. Only add static IPs you control. A whole office range is fine if the office really owns it.

## Also check

- `bantime` and `findtime`: a ten minute ban is plenty for most servers. Repeat offenders can get longer bans with `bantime.increment = true`.
- CI jobs that SSH in: make sure they use the right key, or each run adds failures to your count.
- Cloud firewalls: if fail2ban is not the cause, the provider firewall or `ufw` may be.

A longer checklist to [fix SSH connection refused](https://peon.sh/blogs/ssh-connection-refused-fix) covers the other causes. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.
