# Renovate configuration

Nextest uses [Renovate](https://docs.renovatebot.com/) to keep its dependencies up-to-date. This
repository contains our Renovate configuration.

## Self-hosting Renovate

We use a [self-hosted
instance](https://docs.renovatebot.com/getting-started/running/#self-hosting-renovate) of Renovate.
This is so that we can run [post-upgrade
commands](https://docs.renovatebot.com/self-hosted-configuration/#allowedpostupgradecommands),
specifically updating the  
[`workspace-hack` crate](https://github.com/nextest-rs/nextest/tree/main/workspace-hack) managed by
[`cargo hakari`](https://docs.rs/cargo-hakari).

* The instance runs as a [periodic GitHub
  workflow](https://github.com/nextest-rs/renovate/actions/workflows/renovate.yml) -- can also be
  manually triggered via the `workflow_dispatch` UI).
* The source code is at [`.github/workflows/renovate.yml`](.github/workflows/renovate.yml).

### Global Renovate configuration

The global (or administrator) Renovate configuration is at [`global-config.json`](global-config.json) ([reference](https://docs.renovatebot.com/self-hosted-configuration
)). Comments:

* The list of repositories to fetch must be listed out manually.
* While it is possible to specify repository-specific configuration in the global config, as a matter of policy we restrict `global-config.json` to only contain global config items. Default repository-specific configuration must go in `default.json`.

### Repository-specific Renovate configuration

Default repository-specific configuration goes in [`default.json`](default.json). This gets imported into repositories using Renovate's [config presets](https://docs.renovatebot.com/config-presets/) feature ([example](https://github.com/nextest-rs/datatest-stable/blob/main/.github/renovate.json)):

```json
{
    "extends": ["github>nextest-rs/renovate"]
}
```

Comments:

* [`ignorePaths`](https://docs.renovatebot.com/configuration-options/#ignorepaths) is non-additive, so if you wish to ignore a new path in a repo it's best to add it to the default `ignorePaths`.

Extra, commonly used sets of repository-specific configuration go in this repository within other files. For example, if a post-upgrade script needs to be run, check it in as an executable `scripts/renovate-post-upgrade.sh` ([example](https://github.com/nextest-rs/nextest/blob/main/scripts/renovate-post-upgrade.sh)) and import [`github>nextest-rs/renovate:post-upgrade`](post-upgrade.json).

## Adding a new repository to Renovate

You must do the following three things:

1. Add the repository to the list of repositories in [`global-config.json`](global-config.json).
2. Check in a `.github/renovate.json` file which extends from `default.json` ([example](https://github.com/nextest-rs/datatest-stable/blob/main/.github/renovate.json)).
3. Give [@nextest-bot](https://github.com/nextest-bot) write access to the repository in its settings.

Renovate will only manage dependencies for the repository if all three conditions are met.
