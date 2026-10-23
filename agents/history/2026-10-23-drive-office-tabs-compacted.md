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
