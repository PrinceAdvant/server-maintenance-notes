# Rotating API tokens used by CI without a failed deploy

Deploy tokens stored in CI tend to live for years because rotating them feels risky. With an overlap period, rotation takes ten minutes and nothing breaks.

## The overlap method

1. **Create a second token** with the same scope as the current one. Most platforms allow several active tokens.
2. **Update the CI secret** to the new token.
3. **Trigger a deploy** and confirm it authenticates with the new token.
4. **Revoke the old token.**
5. **Watch for failures** for a day. Anything still using the old token, such as a forgotten cron job, shows up now, with a clear error.

## Webhook signing secrets

Webhook receivers can accept two secrets for a short period: verify the signature against the new secret first and fall back to the old one. Remove the fallback after the sender is updated.

## Scope tokens tightly

A token used only to trigger deploys for one project should not be able to read environment variables or delete services. Narrow scopes reduce the damage if a CI log leaks one.

## Know where tokens live

Keep a short list: which token, which CI system, which repository, who created it and when. Without it, rotation becomes archaeology.

## Schedule it

Rotate every few months and immediately when someone with access leaves. Put a recurring reminder in the team calendar.

## Never print tokens

Mask secrets in CI logs and avoid `set -x` in scripts that handle them.

The Peon docs on [webhooks and API tokens](https://peon.sh/docs/webhooks-and-api) describe how deploy triggers are authenticated. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.
