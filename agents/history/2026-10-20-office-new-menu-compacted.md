# Office creation in Drive's New menu

User authorized new Maps/Meet apps, Drive Office creation, local installation,
org push and releases; no GitHub Actions. Existing Office creation was buried
inside New file. ContextMenu.vue now exposes DOCX/XLSX/PPTX/PDF directly using the
existing createOfficeDocument path and normal Drive permissions/storage.

Source branch master at a1c42f5d before this change. Push only to
oneworkspacestudio/drive, never the upstream origin. Unrelated untracked
drive-preview.png and frontend/drive-preview.html must be preserved.

Production Vite/TypeScript and Windows Go development builds passed. Existing
embedded Office/Studio engine assets are retained unchanged; this turn does not
repeat the earlier full document-format acceptance. Intended release:
drive-v0.5.0-preview.3, integrated into Workspace53. Release material on
I:/Projects/tooling/maps-meet-20261020. Complete source, license/provenance,
publication and installed-update receipts before claiming delivery.

## Delivery checkpoint

Source1319b08b and tagdrive-v0.5.0-preview.3 are pushed to the organization.
The raw EXE and other large release assets are still uploading to draft418809950.
Four actual main-menu operations created ordinary DOCX/XLSX/PPTX/PDF files and
reported saved in an isolated synthetic folder; evidence is in the release
tooling directory. Engine edit/collaboration coverage was not repeated.
The normal backed-up owner53-to54 update installed this exact Drive EXE, with
Photos bytes/version retained,24 apps and23 services healthy. The final combined
suite55 adds a release-ordering repair; its publication remains pending.
Preview processes/data were removed from C. Preserve the two unrelated previews.

Manager 54-to-55 update is now complete and preserved all 24 app packages/versions,
identity and paths; 23 services healthy and newest rollback verified. Drive 3 is
installed unchanged. Its draft and full Workspace 55 release are still retrying
large GitHub uploads after HTTP 408 failures; do not report them public yet.

Final delivery complete: Drive 3 is public with all 15 release assets verified,
including its managed-update manifest. Workspace 55 is public with 21 verified
assets and includes this exact Drive executable. Installed Drive SHA256 matches
the public asset; all 23 suite service health entries are running. Source/docs
pushed without Actions. Photos bytes/version remain unchanged. Preserve the two
unrelated untracked preview files; no further owner update is needed.
