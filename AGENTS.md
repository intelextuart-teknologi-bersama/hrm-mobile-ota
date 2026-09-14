# HRM Mobile OTA — Agent Instructions

> This file is written for AI coding agents. Follow it before making changes. If a scoped `AGENTS.md` exists deeper in the directory tree, the more specific file takes precedence for that scope.

## Project

This repository distributes production OTA artifacts for HRM Mobile through GitHub Pages. Treat every change under `ota/` as production-sensitive because clients may download manifests and bundles directly from this repository.

## Important Paths

| Area | Path | Rule |
|---|---|---|
| Android production OTA | `ota/android/production/` | Contains production manifest and bundles |
| iOS production OTA | `ota/ios/production/` | Contains production manifest and bundles |
| GitHub Pages entry | `index.html` | Do not change unless the task explicitly requires it |
| Pages compatibility | `.nojekyll` | Keep unless the hosting strategy explicitly changes |
| Repository guidance | `README.md` | Human-facing operational reference; do not edit unless requested |

## OTA Artifact Rules

- Preserve the existing directory layout under `ota/<platform>/production/`.
- Treat `manifest.json` as production metadata.
- Keep `platform`, `runtimeVersion`, `version`, `updateId`, `bundleUrl`, and `sha256` internally consistent.
- Never invent a SHA-256 value. It must match the actual bundle artifact.
- Do not manually alter an OTA bundle unless the task explicitly requires artifact replacement.
- Do not publish a bundle built for one runtime/platform as another runtime/platform.
- Do not delete older bundles or manifests unless retention cleanup is explicitly requested and compatibility impact has been checked.
- Do not change production CDN/Pages URLs, runtime-version conventions, or channel semantics unless explicitly requested.
- A rollback or kill-switch change is production-impacting and must be explicitly requested or clearly required to resolve an active production incident.

## Publishing Relationship

OTA artifacts are generated from the `hrm-mobile-arsitekhijau` application repository using its publishing/export scripts. This repository should normally receive generated artifacts rather than source application changes.

The human-facing README currently documents commands such as:

- `npm run publish:ota`
- `npm run publish:ota:bash`
- platform-specific OTA publishing commands

Run those commands from the mobile application repository, not from this artifact repository.

## Security and Change Boundaries

- NEVER commit application source code, `.env` files, signing keys, keystores, credentials, private keys, access tokens, or secrets here.
- Do not modify GitHub Pages, workflow, CDN, or release infrastructure unless the task explicitly requires it.
- Do not remove or rewrite unrelated user/developer changes.
- Keep changes limited to the requested task.
- Do not create extra documentation, changelogs, reports, or README updates unless explicitly requested.

# Mandatory AI Agent Delivery Workflow

The rules in this section override any conflicting Git workflow guidance elsewhere in the repository, including older README examples that show direct pushes to `main`.

## 1. Before Changing Files

1. Read this file and inspect the relevant manifest/bundle state.
2. Confirm the target platform, runtime version, OTA version, channel, and requested operation.
3. Check repository status and identify the current branch.
4. Use the latest `staging` branch as the integration baseline when `staging` exists.
5. Never overwrite uncommitted work that you did not create.

## 2. Branch Policy

- NEVER commit directly to `main`, `master`, or `staging`.
- NEVER push directly to `main`, `master`, or `staging`.
- Create a dedicated task branch before making changes.
- When `staging` exists, create the task branch from the latest `staging`.
- Use a clear branch prefix such as:
  - `feat/<short-description>`
  - `fix/<short-description>`
  - `chore/<short-description>`
  - `release/<ota-description>`
- Do not force-push, rewrite shared history, delete other developers' branches, or use destructive Git operations unless the user explicitly requests them.

## 3. Validation Before Delivery

For OTA changes, verify all applicable items before delivery:

- manifest JSON is valid;
- platform matches the destination directory;
- runtime version matches the intended app runtime;
- OTA version/update ID are correct;
- bundle URL points to the intended artifact;
- SHA-256 matches the actual bundle;
- changelog/message accurately describes the update;
- `disabled` and `mandatory` values are intentional;
- no unrelated artifact was changed or deleted.

Do not claim a bundle/hash/runtime check passed unless it was actually verified.

## 4. Commit and Push Policy

- Do not push partial, unverified, or known-broken OTA work unless the user explicitly requests a checkpoint branch.
- Commit only task-related changes.
- Review staged changes before committing.
- Use clear Conventional Commit-style messages where practical, for example:
  - `fix: correct android OTA manifest`
  - `release: prepare OTA v13 artifacts`
  - `chore: update agent workflow rules`
- Push only the dedicated task branch.
- Re-check that no secrets or application source files are included before pushing.

## 5. Pull Request Policy

- A Pull Request is the required delivery mechanism after the task is fully complete.
- The Pull Request base MUST be `staging`.
- NEVER open a Pull Request to `main` or `master` for normal task delivery.
- NEVER bypass the Pull Request by pushing directly to `staging`.
- Create the Pull Request only after implementation/artifact preparation, validation, self-review, commit, and task-branch push are complete.
- The Pull Request description must include:
  - target platform(s);
  - OTA/runtime version;
  - validation performed, including hash verification when applicable;
  - rollback/kill-switch considerations;
  - known risks or limitations.
- Do not merge your own Pull Request unless the user explicitly requests the merge.

## 6. If `staging` Does Not Exist

- Do not create `staging` automatically.
- Do not substitute `main` or `master` as the Pull Request target.
- Keep completed work on the dedicated task branch and report that delivery is blocked until a maintainer creates or designates `staging`.

## Definition of Done

A task is considered complete only when all applicable items are true:

- Requested OTA/repository change is fully prepared.
- Manifest and artifact relationships are validated.
- Hash/runtime/platform checks are complete when applicable.
- The final diff has been self-reviewed.
- No known task-related issue remains.
- No secrets, source code, or unrelated changes are included.
- Changes are committed on a dedicated task branch.
- The completed task branch is pushed.
- A Pull Request targeting `staging` is created when `staging` exists.
- The agent has not directly pushed to or committed on `main`, `master`, or `staging`.

## Response Style

- Communicate with the user in Indonesian unless the user requests another language.
- Keep completion summaries concise and state exactly what was changed and what validation was performed.
