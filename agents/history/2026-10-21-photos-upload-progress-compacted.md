# Photos upload destination and progress recovery

User reported eight photos stuck at `0/8 saved` with a nearly full progress bar.
Repository master base 4ec28349; unrelated untracked drive-preview.png and
frontend/drive-preview.html preserved. No publication authorization for this fix.

## Diagnosis

Read-only inspection of the installed Drive log found the first matching upload
rejected with HTTP 403. Photos had selected My Clips Originals, an app-owned
view/download-only source, because it selected the first source in the API map.
My Drive has create permission. No permissions or owner files were changed.
The bar measured the current file, while the caption counted the entire batch.
The UI also waited for library refresh before displaying errors/completion;
fetch requests had no timeout, so that wait could hide a settled upload state.

## Changes

- Shared Photos API uses source readOnly and effective profile scope permissions,
  chooses a writable initial source (prefers My Drive), and rechecks access before
  transferring bytes. Read-only libraries remain browsable without upload controls.
- Batch progress is `(completed + provisional current fraction) / total`;
  provisional file progress is capped at 99% until strict chunk acknowledgement.
  UI separates batch/file progress, confirmed saves, elapsed time and saving phase.
- Metadata/library fetch deadline is 30s; XHR has 60s transfer-inactivity and
  120s server-confirmation deadlines (10min hard chunk limit), early HTTP-error
  handling, and abort cleanup. Stop upload retains the in-memory remaining queue.
- Failures/completion are displayed before library refresh. Permission failures
  offer an explicit retry to the writable library. Existing duplicate naming,
  confirmed offsets, no-overwrite behavior and uncertain-save warning remain.
- PHOTOS.md documents these behaviors. No new dependency or backend API needed.

## Build / delivery boundary

Photos production Vite and Windows Go builds passed. Integrated Drive production
Vite/checker build passed with existing dynamic-import warnings. No tests added
or run; personal photos were not used as fixtures. Build material is outside Git
under the task's photos-upload-20261021 tooling folder on I:. Drive build requires
`-buildvcs=false` here because Go's implicit Git status lacks the safe-directory
override; explicit commit metadata identifies the dirty working-tree source.
Windows Drive compilation also passed, retaining the existing embedded Office
and Studio engines. The installed manager was stopped normally; only Photos and
Drive executables and their local catalog hashes/versions were replaced, then
the existing manager restarted hidden. Executable rollback copies are in the
task tooling folder; the small config rollback is in the protected installation
backups/photos-upload-ui-20261021 folder. No personal data was copied or modified.

Installed versions: photos-v0.5.0-upload-fix.20261021 and
drive-v0.5.0-photos-fix.20261021. SHA256 respectively:
02938e215a0a3d1d544a0767a68f30c6bc7563ef80bdf47b4c9722cb5d6bd613,
81b334afe53a7e7c1268e4727eb33cee2af5eb9c6dcef1379cfd0b8c6bd25851.
Installed hashes matched. Photos served the expected new batch/read-only/stop
controls in its compiled JS; Drive health returned 200. Manager startup completed
27/27 steps. Both local and permanent HTTPS health endpoints reported status ok,
all 24 service entries running including Photos, Drive and Cloudflare. Anonymous
remote static-asset access was correctly denied by Workspace's sign-in gate;
the new compiled assets were inspected directly on the loopback Photos service.
An unrelated Mail inbound-network warning occurred during normal startup; a
running Mail process is not evidence of Internet mail delivery.

Nothing committed, pushed or released; no GitHub Actions. These are local fixes;
the public release still predates them. Refreshing the browser loads the new UI
and discards any old in-memory file selection. Ask the owner to reselect files;
do not claim an eight-file upload acceptance run that was not performed.
