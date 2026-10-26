# Photos mutable state outside Apps

Workspace update-efficiency work requires programs and mutable data to stay
separate. Read-only installed inventory found Photos.log actively written beside
Photos.exe. No user photo store is owned by this companion; Drive owns files.

Local source change in apps/photos/main.go uses WORKSPACE_APP_DATA_DIR when
managed, otherwise the user's config directory / One Workspace / Photos. Refuse
state inside the executable directory. Read Photos.json from that state folder;
standalone legacy portable JSON remains a read-only fallback. Create the log only
after --version exits. Manager captures stderr if private log creation fails.
The Workspace manager companion change injects the private per-app directory.

Windows x64 compilation with Go1.27.3/CGO0 and Git whitespace checking passed.
No tests or live runtime/deployment checks were requested/run. Build artifact on
I:/Projects/tooling/update-efficiency-20261026/Photos.exe. No binary/source publish,
version bump, release or install; current task authorizes source implementation.
Installed legacy Photos/log remains unchanged until a later authorized rollout.
Preserve unrelated drive-preview.png and frontend/drive-preview.html.

## Authorized publication and installation

The later user request authorized release and installation. Org master source
is ee090cbd389191975ffc8f24c6dbb364c923f9e3 (origin remains upstream; org pushes
must use the explicit oneworkspacestudio/drive URL). Public immutable release:
https://github.com/oneworkspacestudio/drive/releases/tag/drive-v0.5.0-preview.6
All 15 published assets matched local size/SHA256; Actions disabled. Photos.exe
is 7,780,352 bytes, SHA256
9ffc67cd8d133658c7866a727b9c9750aec62c0aee22f5474754bc548bb0e759.
Drive executable remains byte-identical to preview5. Public Workspace update
channel promotion and anonymous asset/manifest retrieval verified.

Primary normal suite update installed Photos6, with private Photos.log confirmed;
subsequent manager92 audit retained its exact public bytes and app contract.
No legacy Apps log or user photo files were deleted/moved. Secondary installation
is pending the Workspace92 manager rollout; matching suite pins now reuse the
standalone Photos asset instead of downloading the entire suite bundle. Original
source-only/nonpublication statements above describe the earlier task phase.
No automated tests or native rollback exercise was requested/run. Artifacts and
sanitized receipts are under I:/Projects/tooling/update-efficiency-20261026.

Secondary completion: manager92 resolved the exact7,780,352byte direct Photos.exe
asset instead of1.08GB suite ZIP. Normal Photos6 apply completed in3m17s with
all27 public services healthy. Manager retained other installed app versions;
no user photo files or legacy logs were moved/deleted. Earlier pending statements
above are historical. No automated tests/native rollback exercise performed.
