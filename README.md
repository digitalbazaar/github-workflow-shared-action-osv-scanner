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

## License

[BSD-3-Clause](LICENSE) Copyright 2025 Digital Bazaar, Inc.

Commercial support is available by contacting
[Digital Bazaar](https://digitalbazaar.com/) <support@digitalbazaar.com>.
