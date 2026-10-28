# Security policy for this fork

This repository is a fork of iptv-org/iptv and should be treated as an untrusted-data and supply-chain boundary.

## Before running project code

- Do not add production credentials, personal access tokens, or deployment secrets to this repository.
- Review GitHub Actions workflows before enabling them or configuring secrets. Upstream workflows may target the original iptv-org repositories or expect its GitHub App credentials.
- Install dependencies with lifecycle scripts disabled during the initial review: `npm ci --ignore-scripts`. Inspect any lifecycle script before deciding whether to run it.
- Do not treat publicly reachable stream URLs as evidence of permission to redistribute a broadcast.

## Security checks

The initial audit workflow runs dependency auditing, linting, tests, and CodeQL on manual dispatch or relevant pull requests. A passing workflow is useful evidence, not a guarantee that the project is vulnerability-free. Review results and fix confirmed findings before deployment.

## Reporting

Do not publish credentials, private stream access details, or exploitable vulnerabilities in public issues. Rotate any exposed credential immediately and report sensitive findings privately through GitHub's security reporting feature when available.
