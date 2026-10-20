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
