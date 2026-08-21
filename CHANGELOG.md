# github-workflow-shared-action-osv-scanner Changelog

## 4.0.3 - 2026-08-21

- Fix `osv-scanner-main.yaml`'s issue body/comment text joining the OSV
  report and the npm audit/list report with a single space instead of a
  newline, so the OSV table's last cell ran directly into `### npm audit`.

## 4.0.2 - 2026-08-21

### Fixed
- Fix `osv-scanner-main.yaml` failing on every `push`/`schedule` run with
  `fatal: empty string is not a valid pathspec`. It passed
  `github.base_ref` as the ref to scan, which is only populated for
  `pull_request` events; on `push`/`schedule` this was always empty. Use
  `github.sha` instead, which is what's actually being scanned there.
- Fix the same class of bug in `osv-scanner-pr.yaml`'s base-branch scan for
  `merge_group`-triggered runs: `github.base_ref` isn't populated for
  `merge_group` either, only `pull_request`. Fall back to
  `github.event.merge_group.base_ref`.
- `scan-branch` action now fails with a clear `::error::` if its `ref`
  input is empty, instead of a cryptic git pathspec error.

## 4.0.1 - 2026-08-20

### Fixed
- Fix `npm audit` report table showing a bare version number in the "Fix
  Available" column when the fix requires updating a different (usually
  ancestor) package instead of the vulnerable package itself; it now shows
  `name@version` so it's clear which package to update.

## 4.0.0 - 2026-08-20

### Changed
- Wrap `npm list` output in a collapsed `<details>` section, since it can be
  large when many vulnerabilities are found.
- Drop `continue-on-error` from steps where it could only mask a real bug
  (never an expected condition like "vulnerabilities found"), and add
  `::warning::` annotations when the remaining `continue-on-error`-guarded
  npm audit/list report steps fail, so problems are no longer silently
  invisible.
- Extract the checkout/osv-scanner/npm-audit/npm-list sequence, previously
  duplicated once in `osv-scanner-main.yaml` and twice in
  `osv-scanner-pr.yaml`, into a shared local composite action
  (`.github/actions/scan-branch`), referenced via the `$/` self-repository
  syntax. Internal refactor only; no change for consumers of the two
  reusable workflows.

### Fixed
- Collapse PR comment to a single "no change" note when base and PR
  vulnerability scan results are identical, instead of always posting both
  in full.
- Stop `npm list` from showing the entire dependency tree when there are no
  vulnerabilities; it now shows nothing in that case.
- Fix `npm audit` step to actually capture `npm audit --json` output. It was
  previously never executed (a quoting bug swallowed the command), and only
  appeared to work because an earlier, insecure version of the `npm audit`/
  `npm list` report steps happened to re-run it via unsafe template
  expansion; fixing that injection risk exposed this.

## 3.1.0 - 2026-08-17

### Changed
- Update actions.

### Fixed
- Improve npm audit `fixAvailable` handling.

## 3.0.0 - 2026-04-16

### Added
- README example.

### Changed
- Various fixes.
- Add OSV label if missing.
- Update actions.

## 2.0.0 - 2025-10-11

### Added
- npm audit and list report.

## 1.0.0 - 2025-07-19

### Added
- Initial release, see individual commits for history.
