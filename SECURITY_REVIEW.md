# Initial security review — 2026-10-28

Repository: `Aditya264-cr/iptv` (fork of `iptv-org/iptv`)

## Confirmed checks

The new `Security audit` workflow completed successfully on branch `security/initial-audit`:
- `npm ci --ignore-scripts`
- `npm audit --audit-level=high`
- ESLint
- Vitest test suite
- CodeQL JavaScript/TypeScript analysis

Run: https://github.com/Aditya264-cr/iptv/actions/runs/40723820852

These results are scoped to the workflow's tested commit and configuration. They do not establish that every dependency, workflow, or historical commit is secure.

## Workflow review findings

### High-priority hardening

1. `.github/workflows/update.yml` runs on a daily schedule and has deploy steps with hardcoded target repositories `iptv-org/iptv` and `iptv-org/api`. The deploy steps are guarded by `github.ref == 'refs/heads/master'`, but do not explicitly verify `github.repository == 'iptv-org/iptv'`. In a fork, keep this automation disabled or add an explicit upstream-repository guard before ever adding credentials.
2. `.github/workflows/format.yml` can commit and push to the fork's `master` branch if expected GitHub App credentials are configured. Do not configure upstream app credentials in this fork; disable or explicitly guard the workflow unless fork-specific automation is intentionally needed.
3. `.github/workflows/check.yml` interpolates `steps.files.outputs.all_changed_files` directly into shell commands. This is a potential shell-injection pattern if a changed path contains shell metacharacters. Pass the value through an environment variable and handle paths as safely quoted arguments instead of embedding expression output in the shell script.
4. The repository's `package.json` has a `postinstall` script that runs `npm run api:load`, which executes project code and downloads API data. Initial audit correctly used `npm ci --ignore-scripts`. Inspect lifecycle behavior before enabling it in credential-bearing environments.

### Other observations

- Workflow actions are generally pinned to commit SHAs, which is preferable to mutable tags.
- Several inherited issue/discussion workflows expect `APP_ID` and `APP_PRIVATE_KEY`. Leave those secrets unset in this fork unless the workflows are deliberately adapted and reviewed.
- Do not add personal access tokens, production credentials, or deployment keys to this repository.
- Public stream URLs and the repository's own license do not by themselves establish permission to retransmit the underlying broadcasts.

## Secret scanning status

**Not completed.** A filename-based check of the current repository tree did not reveal obvious secret-named files, but that is not a content scan and does not cover Git history. The connected GitHub interface did not provide access to the repository's Dependabot or secret-scanning alert endpoints.

Before deployment, run a dedicated secret scanner (for example, Gitleaks) against both the current working tree and full Git history in a trusted environment. If a credential is found, revoke/rotate it immediately; removing it from the latest commit alone is insufficient.

## Recommended order

1. Keep this pull request unmerged until reviewing the workflow findings.
2. Disable/guard the inherited scheduled publishing and formatting workflows in the fork.
3. Fix the shell interpolation in `check.yml`.
4. Run a full-history secret scan and inspect any findings privately.
5. Rerun CI and review results before using this repository as a data source for Yocare Entertainment.
