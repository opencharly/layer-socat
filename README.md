# socat

A socket-relay tool for OpenCharly images — relays an eth0-bound TCP port to the
same port on loopback.

The `socat` candy installs the `socat` binary plus `iproute` (the `ip` command)
and ships the `relay-wrapper` script at `/usr/local/bin/relay-wrapper`. Candies
that declare `port_relay:` pull it in automatically; the wrapper discovers the
container's non-loopback IP with `ip -4 addr` and execs
`socat TCP-LISTEN:PORT,bind=ETH_IP,fork TCP:127.0.0.1:PORT`.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `socat` |
| Binary | `/usr/bin/socat` |
| Wrapper | `/usr/local/bin/relay-wrapper` |
| Packages | `socat`, plus `iproute`/`iproute2` (per distro) |
| Distros | Fedora, Arch, Debian, Ubuntu |
| Service / port | none — driven by `port_relay:` consumers |

## How to use it

Typically not added directly — it is auto-included when a candy uses
`port_relay:` in its `charly.yml`. To add it explicitly, pin this repo in a box's
`candy:` list:

```yaml
my-box:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-socat:v2026.240.0229'
```

The candy's `plan:` asserts the `socat` binary, the executable wrapper, the
wrapper's usage error on a missing port, and the `iproute` package (mapped per
distro).

## Layout

- `charly.yml` — the `socat:` candy entity (the `package:`, the
  `distro.{arch,debian,fedora,ubuntu}:` sections, the wrapper `copy:` step, and
  the `check:` probes) and the embedded `socat-skill:` skill entity.
- `relay-wrapper` — the port-relay wrapper script.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-infrastructure:socat`
- VM console access: `/charly-infrastructure:virtualization`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
