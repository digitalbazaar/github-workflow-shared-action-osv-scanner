# github-workflow-shared-action-osv-scanner Changelog

## Unreleased

### Changed
- Wrap `npm list` output in a collapsed `<details>` section, since it can be
  large when many vulnerabilities are found.

### Fixed
- Collapse PR comment to a single "no change" note when base and PR
  vulnerability scan results are identical, instead of always posting both
  in full.
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
