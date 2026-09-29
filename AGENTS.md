# AGENTS.md — layer-socat

Standalone candy repo for the `socat` layer — the socket-relay tool plus the
`relay-wrapper` script that relays an eth0-bound TCP port to loopback. The candy
lives in `charly.yml` at the repo root: the `package:`, the per-distro package
sections, the wrapper `copy:` step, the `check:` probes, and the embedded
`skill:` entity projected into the marketplace corpus as
`/charly-infrastructure:socat`.

Canonical files:

- `charly.yml` — the `socat:` candy entity and the `socat-skill:` skill entity.
- `relay-wrapper` — the port-relay wrapper script.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:socat` — the owning skill. The `port_relay:` consumer
  model, the wrapper's discovery logic, and the distro package mapping. Load
  before editing or troubleshooting the candy.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `copy:`/`check:`, `distro:` sections, `package_map`).
  Load before editing any entity field or plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- `charly box validate` at the repo root checks the manifest parses and
  validates.
- The candy's `plan:` `check:` steps are the functional evidence: the `socat`
  binary, the executable wrapper, the wrapper's usage error on a missing port,
  and the `iproute` package via its per-distro `package_map`.
- Four distro arms (`arch`, `debian`, `fedora`, `ubuntu`) are declared; a package
  change must keep each arm's name and the matching `check:` probe honest.

## Modify this repo

- Edit the `socat:` candy entity AND the `socat-skill:` skill entity in
  `charly.yml` together. The skill is the projected usage source, so a package or
  path change not mirrored in the skill leaves the corpus stale.
- The wrapper is the `port_relay:` contract; keep its usage error and its
  `ip -4 addr` discovery behaviour intact.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
