<!--
Copyright 2025 Digital Bazaar, Inc.
SPDX-License-Identifier: BSD-3-Clause
-->

# Shared OSV Scanner Workflow for GitHub Actions

There are two shared workflows for using [OSV](https://osv.dev/) for doing
Software Composition Analysis (SCA) using a `package-lock.json` (which is
generated as part of the workflow).

`.github/workflows/osv-scanner-pr.yaml` runs OSV on any Pull Request and
reports any found vulnerabilities on the PR as a comment. Any updates during the
PR review process will result in an additional comment being added (reflecting
any changes made along the way).

`.github/workflows/osv-scanner-main.yaml` uses the same OSV scanning process,
but only scans the git base ref (typically the `main` branch of the repo). Once
scanning is complete any vulnerabilities found will be added to a
`Vulnerabilities as of ${NOW}` issue.

Because these are shared workflows the actual scheduled or triggered use of them
is set in the calling workflow, so it may vary across repos.

## Usage

Update the workflow below by changing `{{SHA}}` to the preferred SHA of this
repo (see the releases table below). Use of a SHA value is preferred to using a
tag name due to the risk of "[artipacking](https://docs.zizmor.sh/audits/#artipacked)".

Once updated, save the file as `.github/workflows/osv-scanner.yaml` in the repo
of your choice.

```yaml
# OSV-Scanner PR scanning reusable workflow, can be used as a PR action to
# detect new vulnerabilities being introduced.
name: Use OSV to do SCA on main (daily) and PRs

on:
  pull_request:
    branches: [main]
  merge_group:
    branches: [main]
  schedule:
    - cron: 0 0 * * *
  push:
    branches: [main]

jobs:
  ## run the following on PRs
  osv-scan-pr:
    uses: digitalbazaar/github-workflow-shared-action-osv-scanner/.github/workflows/osv-scanner-pr.yaml@{{SHA}}
    permissions:
      contents: read
      pull-requests: write

  ## run the following only on the main branch
  osv-scan-main:
    uses: digitalbazaar/github-workflow-shared-action-osv-scanner/.github/workflows/osv-scanner-main.yaml@{{SHA}}
    permissions:
      contents: read
      issues: write
```

## Private repository dependencies

By default the workflows generate `package-lock.json` with no credentials, which
is all a project with public dependencies needs. Projects that depend on
*private* GitHub repositories, or on packages served from a private npm
registry such as GitHub Packages, can opt in with the inputs below. Repos that
leave them unset are unaffected.

| Input | Description |
| ----- | ----------- |
| `private-repo-orgs` | Whitespace-separated list of GitHub organization or user names whose private repositories are used as npm dependencies. git URLs for these orgs are rewritten to token-authenticated HTTPS so `npm install --package-lock-only` can resolve them. |
| `npm-registry-url` | npm registry URL (e.g. `https://npm.pkg.github.com`) to authenticate against. |

| Secret | Description |
| ------ | ----------- |
| `PRIVATE_REPO_TOKEN` | Token for `private-repo-orgs`. Needs `contents: read` on the private repositories named there. |
| `NPM_REGISTRY_TOKEN` | Token for `npm-registry-url`. Needs `read:packages`. Defaults to `PRIVATE_REPO_TOKEN` when unset. |

The two are separate so each can be scoped to just what it needs, and so they
can be rotated independently. A single token carrying both scopes still works:
pass it as `PRIVATE_REPO_TOKEN` and leave `NPM_REGISTRY_TOKEN` unset. The
fallback is one-way — `NPM_REGISTRY_TOKEN` is never used for git.

Setting `private-repo-orgs` without also providing `PRIVATE_REPO_TOKEN` fails
the run with an explanatory error rather than an obscure npm authentication
failure. Settings that would silently do nothing — a registry URL with no
token, or a registry token with no URL — produce a warning.

```yaml
jobs:
  osv-scan-pr:
    uses: digitalbazaar/github-workflow-shared-action-osv-scanner/.github/workflows/osv-scanner-pr.yaml@{{SHA}}
    permissions:
      contents: read
      pull-requests: write
    with:
      private-repo-orgs: my-org
      npm-registry-url: https://npm.pkg.github.com
    secrets:
      PRIVATE_REPO_TOKEN: ${{ secrets.MY_GIT_PAT }}
      NPM_REGISTRY_TOKEN: ${{ secrets.MY_NPM_PAT }}

  osv-scan-main:
    uses: digitalbazaar/github-workflow-shared-action-osv-scanner/.github/workflows/osv-scanner-main.yaml@{{SHA}}
    permissions:
      contents: read
      issues: write
    with:
      private-repo-orgs: my-org
      npm-registry-url: https://npm.pkg.github.com
    secrets:
      PRIVATE_REPO_TOKEN: ${{ secrets.MY_GIT_PAT }}
      NPM_REGISTRY_TOKEN: ${{ secrets.MY_NPM_PAT }}
```

The rewrite is written to the runner's global git config for the duration of
the scan and removed again afterwards, so this is intended for ephemeral
runners.

## Releases

| Release | SHA |
| ------- | --- |
| v4.0.5  | b65a7545b2887e10d287fc8541a2ee9bb5110630 |

<details>
  <summary>Older releases</summary>

| Release | SHA |
| ------- | --- |
| v4.0.4  | dad8ba639b7281f1346833f5e6422f5490e578d2 |
| v4.0.3  | 7a493ff60d51b097b1a0cc489f23a8ec422c7384 |
| v4.0.2  | e17ef462f806dd97f973a3fd5c67bd761179c983 |
| v4.0.1  | 8599d9f4d0964fa3ea83a50f6e50d69524adc7ab |
| v4.0.0  | 7bf5b9c80f41e5fde94cb0ad74a1a0ceec036726 |
| v3.1.0  | 6f08463b590bb2034203bf68cde70530e43b8a26 |
| v3.0.0  | 536972bc49c10826182731e6d7ec5d69100e14ca |
| v2.0.0  | 5367fe2df1bbed52b3cc34ebde6599d990e92ece |
| v1.0.0  | a3f075f418e548dc2d55220acd7de23bdf8c4e70 |

</details>

See the [CHANGELOG](CHANGELOG.md) for release details.

## License

[BSD-3-Clause](LICENSE) Copyright 2025 Digital Bazaar, Inc.

Commercial support is available by contacting
[Digital Bazaar](https://digitalbazaar.com/) <support@digitalbazaar.com>.
