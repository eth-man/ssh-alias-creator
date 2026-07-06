# sac — SSH Alias Creator

A tiny bash script that appends a `Host` entry to `~/.ssh/config`, so you can
type `ssh web1` instead of `ssh deploy@203.0.113.10`.

## Install

```
git clone https://github.com/eth-man/ssh-alias-creator.git
chmod +x ssh-alias-creator/sac
ln -s "$(pwd)/ssh-alias-creator/sac" /usr/local/bin/sac   # or anywhere on your PATH
```

## Usage

```
sac <alias> [<user>@]<hostname> [-p <port>] [-X]
```

| Argument      | Description                                                              |
|---------------|---------------------------------------------------------------------------|
| `<alias>`     | Short name to use for `ssh <alias>`                                       |
| `<hostname>`  | Hostname or IP to connect to, optionally prefixed with `user@`            |
| `-p <port>`   | SSH port (default: 22)                                                    |
| `-X`          | Disable X11 forwarding (on by default — see below)                       |
| `-h`          | Show help                                                                 |

### Examples

```
sac web1 deploy@203.0.113.10
sac web2 root@10.0.0.5 -p 2222
sac web3 10.0.0.9 -X
```

Running the first example adds this to `~/.ssh/config`:

```
Host web1
  HostName 203.0.113.10
  User     deploy
  ForwardX11 yes
  ForwardX11Trusted yes
```

After that, `ssh web1` connects as `deploy@203.0.113.10`.

## Behavior

- Creates `~/.ssh` (mode `700`) and `~/.ssh/config` (mode `600`) if they don't
  already exist.
- Refuses to add an alias or hostname that's already present in the config
  (case-insensitive exact match), so you won't get silent duplicate entries.
- Exits non-zero on any error (bad usage, invalid port, duplicate alias/host),
  so `sac ... || echo failed` works as expected in scripts.
- X11 forwarding is enabled by default on new entries — this is mainly useful
  for clipboard sharing over SSH in terminal tools like vim. Pass `-X` to
  disable it per-alias.
- `sac` only ever appends to `~/.ssh/config`; it doesn't edit or remove
  existing entries. To change or remove an alias, edit `~/.ssh/config`
  directly.

## Requirements

Bash and standard Unix tools (`grep`, `sed`) — no other dependencies.
