# 👩‍💻 action-sshd-cloudflared

> [GitHub Action] to debug via SSH over a [Cloudflare Tunnel].

[GitHub Action]: https://github.com/features/actions
[Cloudflare Tunnel]: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/

## Overview

This GitHub Action starts an ad hoc SSH server (inspired by
[`sshd` on the go](https://github.com/valeriangalliat/sshd-on-the-go)),
and creates a [Cloudflare Tunnel] to expose it (because we can't
directly open ports on a public IP address from the runner VM).

Then by using the `cloudflared` command you can bind that tunnel to a
local port and SSH there.

It's similar to the [debugging with
SSH](https://github.com/marketplace/actions/debugging-with-ssh) action
([action-upterm](https://github.com/lhotari/action-upterm)), but instead
of using [Upterm](https://upterm.dev/), it's simply backed by
`/usr/sbin/sshd`, `cloudflared` and [100 lines of commented shell script](setup-ssh).

The main difference is that the community `uptermd` server on
`uptermd.upterm.dev` (the default if you don't specify your own server)
technically has full visibility on your unencrypted SSH traffic,
whereas thanks to `cloudflared` allowing to [tunnel raw TCP traffic](https://developers.cloudflare.com/cloudflare-one/applications/non-http/arbitrary-tcp/),
action-sshd-cloudflared is end-to-end encrypted.

## Usage

```yaml
name: CI
on:
  - push
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: valeriangalliat/action-sshd-cloudflared@master
```

Example output:

```
Downloading `cloudflared` from <https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64>...
Configured SSH key(s) for user: valeriangalliat
Creating SSH server RSA key...
Creating SSH server config...
Starting SSH server...
Starting tmux session...
Starting Cloudflare tunnel...
Run the following commands to connect:

    cloudflared access ssh --hostname condo-receptor-maintaining-prominent.trycloudflare.com --url localhost:2222
    ssh -p 2222 runner@localhost

```

You can download `cloudflared` from
[here](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/)
or [here](https://github.com/cloudflare/cloudflared/releases).

When logging in, you'll be automatically attached to a tmux session.
Whenever you exit the session, the action will stop the tunnel and SSH
server and continue its steps.
