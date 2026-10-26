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
