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

## Releases

| Release | SHA | Notes |
| ------- | --- | ----- |
| v2.0.0  | 5367fe2df1bbed52b3cc34ebde6599d990e92ece | Added npm audit/list |
| v1.0.0  | a3f075f418e548dc2d55220acd7de23bdf8c4e70 | Initial release |

## License

[BSD-3-Clause](LICENSE) Copyright 2025 Digital Bazaar, Inc.

Commercial support is available by contacting
[Digital Bazaar](https://digitalbazaar.com/) <support@digitalbazaar.com>.
