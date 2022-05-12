# 👩‍💻 action-sshd-cloudflared

> [GitHub Action] to debug via SSH over a [Cloudflare Tunnel].

[GitHub Action]: https://github.com/features/actions
[Cloudflare Tunnel]: https://developers.cloudflare.com/cloudflare-one/applications/non-http/arbitrary-tcp/

## Usage

```yaml
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: valeriangalliat/action-sshd-cloudflared@master
```
