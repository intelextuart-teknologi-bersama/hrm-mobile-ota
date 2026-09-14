# HRM Mobile OTA

Repository distribusi Over-The-Air (OTA) untuk HRM Mobile ArsitekHijau. Repository ini menyimpan manifest dan bundle production yang dikonsumsi aplikasi melalui GitHub Pages.

> Perubahan di bawah `ota/` bersifat production-sensitive. Jangan mengubah manifest atau bundle tanpa memastikan platform, runtime version, version number, URL, dan SHA-256 konsisten.

## Integration Branch

Repository ini saat ini tidak memiliki branch `staging`, sehingga `main` menjadi integration branch aktif.

Workflow kontribusi:

1. Buat task/release branch dari `main`.
2. Generate atau siapkan artifact pada branch tersebut.
3. Validasi manifest, runtime, bundle, URL, dan hash.
4. Push branch.
5. Buat Pull Request ke `main`.
6. Jangan commit atau push langsung ke `main`.

Jika `staging` tersedia di masa depan, task branch dan Pull Request harus menggunakan `staging` sebagai integration branch. Lihat `AGENTS.md` untuk aturan lengkap AI-agent workflow.

## Current Production State

Android production manifest saat README ini diperbarui:

- Platform: Android
- Channel: `production`
- Runtime: `android-1.1.0-hermes-rn081`
- OTA version: `30`
- Update ID: `ota-android-production-v30`
- Disabled: `false`
- Mandatory: `false`

Manifest adalah source of truth untuk state OTA terkini; jangan mengandalkan angka versi di README untuk operasi release berikutnya.

## Repository Structure

```text
ota/
├── android/
│   └── production/
│       ├── manifest.json
│       └── bundles/
└── ios/
    └── production/
        ├── manifest.json
        └── bundles/

index.html               GitHub Pages entry
.nojekyll                Pages compatibility
README.md                human-facing operational guide
AGENTS.md                AI-agent rules and delivery policy
```

## GitHub Pages

Production artifacts are served from GitHub Pages using the `main` branch.

Typical manifest endpoints:

```text
Android: https://akwancakra.github.io/hrm-mobile-ota/ota/android/production/manifest.json
iOS:     https://akwancakra.github.io/hrm-mobile-ota/ota/ios/production/manifest.json
```

Because Pages serves from `main`, a merged Pull Request can become production-visible. Review OTA changes accordingly.

## Generating OTA Artifacts

Artifacts are generated from the `hrm-mobile-arsitekhijau` application repository, not manually assembled here.

From the mobile application repository:

```bash
# PowerShell
npm run publish:ota -- -Version 31 -Message "Describe the update"

# Bash
npm run publish:ota:bash -- \
  -v 31 \
  -o /path/to/hrm-mobile-ota \
  -m "Describe the update"
```

Platform-specific publishing is also supported by the mobile repository scripts.

The publishing process typically:

1. Exports Android/iOS JavaScript bundles.
2. Packages bundle/assets.
3. Calculates SHA-256.
4. Generates platform manifest metadata.
5. Copies artifacts into `ota/<platform>/production/`.

After generation, review the changes in this repository and deliver them through a task/release branch and Pull Request. Do not direct-push generated production artifacts to `main`.

## Manifest Format

Example:

```json
{
  "platform": "android",
  "channel": "production",
  "runtimeVersion": "android-1.1.0-hermes-rn081",
  "version": 30,
  "updateId": "ota-android-production-v30",
  "createdAt": "2026-09-13T14:46:31Z",
  "bundleUrl": "https://akwancakra.github.io/hrm-mobile-ota/ota/android/production/bundles/ota-android-production-v30.zip",
  "sha256": "<actual-sha256>",
  "mandatory": false,
  "disabled": false,
  "message": "Bug fix description"
}
```

### Required consistency checks

| Field | Validation |
|---|---|
| `platform` | Must match destination platform directory |
| `channel` | Must match intended release channel |
| `runtimeVersion` | Must match compatible mobile app runtime |
| `version` | Must represent the intended OTA release |
| `updateId` | Must correspond to the OTA version/platform/channel |
| `bundleUrl` | Must point to the intended bundle |
| `sha256` | Must match the actual bundle bytes |
| `disabled` | Must be intentional; acts as update kill switch |
| `mandatory` | Must reflect intended client behavior |

Optional fields such as `message`, `changelog`, and `bundleSize` should accurately describe the generated artifact.

## OTA vs Native Release

| Change | Delivery |
|---|---|
| JavaScript/business logic/UI only, compatible runtime | OTA may be appropriate |
| Native dependency/plugin change | New APK/IPA required |
| Expo SDK / React Native / Hermes runtime change | New APK/IPA required |
| Package/bundle identifier or signing change | New native release required |

Do not assume a change is OTA-safe without checking native/runtime impact in the mobile application repository.

## Release Validation

Before opening a Pull Request for production artifacts, verify all applicable items:

- manifest JSON parses successfully;
- platform is correct;
- runtime version matches the target mobile runtime;
- OTA version/update ID are correct;
- bundle URL resolves to the intended file;
- SHA-256 matches the actual bundle;
- `disabled` / `mandatory` values are intentional;
- changelog/message matches the actual change;
- no unrelated bundles or manifests were modified/deleted;
- no source code, credentials, signing material, or environment files are included.

## Rollback

Rollback is production-impacting and should also go through a task branch and Pull Request.

Typical approach:

1. Restore the intended previous manifest/artifact relationship on a rollback branch.
2. Re-verify runtime/platform/URL/hash.
3. Commit and push the rollback branch.
4. Open a Pull Request to the active integration branch (`main` while no `staging` exists).
5. Merge only after review.

Do not use direct `git push origin main` for rollback under the current repository policy.

## Kill Switch

Setting:

```json
{
  "disabled": true
}
```

causes clients to skip OTA updates according to current app behavior. Treat this as a production incident control and deliver the change through the same reviewed branch/PR flow.

## Security

This repository must not contain:

- application source code beyond generated/minified OTA artifacts;
- `.env` files;
- access tokens or credentials;
- keystores/signing keys;
- private keys;
- cloud/service account credentials.

SHA-256 integrity checks, runtime gating, platform guards, and kill-switch behavior are safety controls; do not weaken them without an explicit requirement.

## Contribution Workflow

Current workflow while `staging` does not exist:

```bash
git checkout main
git pull
git checkout -b release/ota-v31
# generate/copy and validate artifacts
git add ota/
git commit -m "release: prepare OTA v31 artifacts"
git push origin release/ota-v31
```

Then open a Pull Request from `release/ota-v31` to `main`.

If a `staging` branch is created later, branch from `staging` and target the Pull Request to `staging` instead.

## Troubleshooting

### Update not detected

Check:

1. `disabled` is not unintentionally `true`.
2. Runtime matches the installed app runtime.
3. OTA version is newer than the installed OTA version.
4. Platform matches the device.
5. Manifest and bundle URLs are accessible.
6. Bundle hash matches manifest metadata.

### Update causes regression

Prepare a reviewed rollback or kill-switch change through a dedicated branch and Pull Request. Do not directly rewrite `main` history.

## License

Private operational repository — ArsitekHijau.
