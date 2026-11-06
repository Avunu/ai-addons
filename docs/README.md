---
title: "Avunu AI addons"
description:
  "Cloudflare Workers AI as a provider for MuK AI, optionally routed through a named
  Cloudflare AI Gateway for usage tracking."
---

Cloudflare Workers AI as a provider for MuK AI, optionally routed through a named
Cloudflare AI Gateway for usage tracking. It is an Odoo 18.0 module built and maintained
by [Avunu LLC](https://avunu.net) for our own client work and published for anyone to
use. The repository sits alongside [OCA/ai](https://github.com/OCA/ai), but its module
builds on MuK AI (`muk_ai`, from the third-party
[muk-it/odoo-modules](https://github.com/muk-it/odoo-modules)) rather than on an OCA
module.

## Modules

| Module                                                      | What it does                                                                                                                | Licence           |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| [`muk_ai_cloudflare_gateway`](muk_ai_cloudflare_gateway.md) | Adds Cloudflare Workers AI as a MuK AI provider, optionally routed through a named Cloudflare AI Gateway for usage tracking | AGPL-3.0-or-later |

Each module's README explains why it exists and how it works.

## Branches

`develop` is the default branch and the one pull requests go to. Its code targets Odoo
18.0, the series named in `.odoo-series`. `18.0` follows `develop` automatically (a
workflow fast-forwards it after every push) and is the branch to install or pin. When a
newer Odoo series is maintained, it gets its own branch (`19.0`, …) and changes flow up
to it from `develop`.

## Install

Requires Odoo 18.0 and a PostgreSQL database. Installing a module pulls in the modules
it depends on (see below), and those must be on your `addons_path` first.

With a plain Odoo checkout, clone this repository on its `18.0` branch and add it to the
addons path:

```sh
git clone --branch 18.0 https://github.com/Avunu/ai-addons.git
odoo-bin --addons-path=odoo/addons,/path/to/ai-addons,<your other addons paths> \
  -d <database> -i muk_ai_cloudflare_gateway --stop-after-init
```

With [odoo-nix](https://github.com/Avunu/odoo-nix), add the repository from the dev
shell. `odoo module add` accepts an `owner/repo` and a branch, adds it as a submodule,
and the addons path is regenerated from the folders present (run `direnv reload`
afterwards):

```sh
odoo module add Avunu/ai-addons 18.0
```

`muk_ai` is not an OCA module, so add its repository the same way:
`odoo module add muk-it/odoo-modules 18.0`.

## Dependencies

Each module's `__manifest__.py` is the source of truth. `muk_ai_cloudflare_gateway`
lists no Odoo core module directly; it depends on the following. The repository listed
is the 18.0 branch; clone it, along with whatever its modules need in turn (their own
manifests list it).

| Module                      | Needs from OCA or others                                                          | Python packages |
| --------------------------- | --------------------------------------------------------------------------------- | --------------- |
| `muk_ai_cloudflare_gateway` | `muk_ai` ([muk-it/odoo-modules](https://github.com/muk-it/odoo-modules), not OCA) | none            |

`muk_ai` in turn needs `muk_mcp` and `muk_web_utils`, from the same repository.

## Development

The repository carries its own development environment, built by
[odoo-nix](https://github.com/Avunu/odoo-nix) in addons mode. OCB and the repositories
these modules depend on are flake inputs pinned in `flake.lock`, and the Python
environment is locked in `nix/`. You need Nix with flakes, and optionally direnv.

```sh
git clone https://github.com/Avunu/ai-addons.git && cd ai-addons
direnv allow                  # or: nix develop --no-pure-eval
devenv up                     # PostgreSQL, Odoo and Mailpit
odoo db provision             # a database with every module here installed
odoo test muk_ai_cloudflare_gateway
```

After `nix flake update`, or when a module gains a `depends` or a Python requirement,
run `odoo project relock` and commit `flake.lock` and `nix/`. A new dependency on
another repository also needs its input and an `externalSources` entry in `flake.nix`.
See odoo-nix's
[A repository of addons](https://github.com/Avunu/odoo-nix/blob/main/docs/scaffolding/addons-repository.md).

## Tests

Odoo runs a module's tests when you install or update it with `--test-enable`. Select a
module's tests with a `--test-tags` filter and use a throwaway database:

```sh
odoo-bin -c odoo.conf -d <testdb> -i muk_ai_cloudflare_gateway \
  --test-enable --test-tags /muk_ai_cloudflare_gateway --stop-after-init
```

`muk_ai_cloudflare_gateway` has automated tests. Its README has no further notes on
running them.

## Licence

Every module is licensed under the
[GNU Affero General Public License v3.0 or later](https://github.com/Avunu/ai-addons/blob/18.0/LICENSE),
as its `__manifest__.py` also states (`AGPL-3`, Odoo's identifier for version 3).

## Provenance

`muk_ai_cloudflare_gateway` moved here, with its history, from
[Avunu/avunu-odoo-addons](https://github.com/Avunu/avunu-odoo-addons) in November 2026,
when Avunu split that repository into one repository per OCA area.
