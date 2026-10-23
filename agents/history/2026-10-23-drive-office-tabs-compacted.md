# New Office documents in browser tabs

User requested that Drive's New document actions open a browser tab instead of
the floating editor prompt, then push and release without Actions.

ContextMenu resolves authenticated /office/new/:type URLs through Vue Router,
including its deployment base URL, source and destination folder. window.open
runs synchronously in the action handler with noopener. NewOfficeDocument renders
the existing DocumentEditor full height with notifications and the user's theme.
The existing cookie authentication guard applies; backend filesystem permissions
still enforce access. Source/path queries are passed as scalar props, not external
navigation destinations. Close navigates the editor tab to the destination folder;
the original Drive listing remains open. No new file is written until the editor
saves. Original save/autosave/collaboration/history implementation is retained.
Public-share creation keeps its existing embedded flow; this change targets Drive.

Preserve unrelated untracked drive-preview.png and frontend/drive-preview.html.
Release target drive-v0.5.0-preview.5, Windows x64. Photos preview4 and vendor
Office/Studio payloads are unchanged. Large build/release artifacts on I: under
tooling/drive-tabs-20261023. No automated tests requested or run for this change;
production compilation and packaging integrity checks are the release checks.
Full Office editing/auth/clean-host acceptance is not implied by compilation.

## Publication receipt

Committed source pushed to organisation master as
7d1d93160733fb2da559648c554413673ba6ed3d; annotated preview5 tag points there.
Production Vite/vue-tsc compilation and Windows Go compilation passed. Published
Windows x64 prerelease 2026-10-23:
https://github.com/oneworkspacestudio/drive/releases/tag/drive-v0.5.0-preview.5

All 15 uploaded asset sizes and GitHub SHA256 digests match local files. Portable
ZIP CRC, unique-entry, embedded executable and updater package/notice checks pass.
Drive EXE SHA256:
9b8249b0511aff740782dd05fce5d04e61209a2b6b137f865d5569c02a3b20ef.
Photos remains preview4 with its previous hash and catalog app version. Original
vendor source pins, archives, licence notices and reproducibility limits retained.
Release carries schema2/integration3 workspace-update.json; no Workspace manager
bundle release or local live-install change was performed. Actions remains
disabled; all builds local on I:. README release link updated in follow-up docs
commit. No automated tests added/run and no new full editing/auth acceptance
claimed. Unrelated untracked preview files preserved.
