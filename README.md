# Renovate Configuration

This repository provides the shared Renovate preset and the Jenkins job that runs Renovate against Forgejo repositories.

## How It Works

The Jenkins organization-folder job runs Renovate as a one-shot container through the nested rootless Podman service. It runs hourly on `main`, can be started manually, and accepts Forgejo webhooks for targeted repository scans. Webhooks created by `renovatebot` are ignored to prevent trigger loops.

Jenkins pulls the digest-pinned Renovate image and runs it read-only with dropped capabilities, bounded resources, a temporary `/tmp`, and persistent named volumes for its cache and checked-out repositories. The container is removed when the run finishes.

## Credentials

Configure these Jenkins Secret text credentials:

- `renovate-token`
- `renovate-github-com-token`
- `renovate-webhook-token` (used for the Generic Webhook Trigger)

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE).
