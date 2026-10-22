# Drive navigation and New dropdown

User supplied screenshots and requested a Google Drive style New dropdown,
clean sidebar, correctly placed top controls, consistent creation icons, and a
Workspace switcher that does not cover content. Implement locally; this request
does not authorize GitHub publication. Keep Actions disabled.

## Source state

Master base 4ec28349. Preserve the previous uncommitted Photos upload fix and its
handoff, PHOTOS.md, and unrelated drive-preview.png/frontend/drive-preview.html.
The rebuilt core intentionally retains the previous Photos fixes. No user data,
source permissions, sign-in registrations or mail/network settings are changed.

## Implementation

- Sidebar New passes an anchor ID to the existing ContextMenu, using a fixed,
  viewport-bounded dropdown with no desktop modal backdrop. Arrow keys, Home/End,
  Escape, Tab, outside dismissal and repeated New clicks work. Dismissal preserves
  the mobile drawer and restores focus appropriately; drawer layering stays above
  its backdrop. Other prompts and right-click menus keep their existing behavior.
- New contains folder creation, native file/folder pickers, DOCX/XLSX/PPTX/PDF
  creation and ordinary-file creation. Admin quota/access, sharing and multiple
  selection remain in other context menus. The new button requires create access
  and a writable source. Office operations retain their existing save paths.
- Account, Help, Settings, appearance and optional installation controls move to
  the top bar/account menu. Hide the inert desktop hamburger on a pinned listing;
  retain real navigation/back controls on phones and other views. Public-share
  login/options remain available through their existing share-specific controls.
- Private sidebar uses Home and real source/custom/app links, rather than the
  old utility-icon row, source combobox and expanded tree. Link customization is
  still in account settings. Existing folder navigation remains in the file view.
- DriveToolbar supplies the folder title, list/grid selector and folder info.
  Shelf ResizeObserver measures actual toolbar/breadcrumb height to offset files.
- Original document SVGs use shared blue/green/yellow/red semantic tokens.
  Folder/upload/plain-file actions use outlined Material icons with consistent
  24px boxes. Folder/plain-text cards get readable semantic icon colours.
- Workspace's existing data-workspace-apps-trigger contract provides a top-bar
  app grid and hides its floating launcher. Standalone Drive hides the grid when
  the shared shell is absent. No Workspace manager source change is required.
- DESIGN-SYSTEM.md documents placement, menu behavior and icon roles.

## Verification / delivery checkpoint

Production frontend/checker build passes with the existing dynamic-import
warnings. Targeted ESLint has only the pre-existing branded raw-text findings:
Photos/Image Studio in General.vue and Drive in bars/Default.vue; new icon-label
findings were corrected. No new package dependency or backend API is introduced.

An isolated loopback fixture uses only synthetic folders/text on I:, with a fresh
metadata DB and noauth bound strictly to loopback. Browser checks exercise the
real built UI and the existing Workspace navigation JS/CSS with a synthetic app
catalog. No owner sign-in, files or live installation serves as a test fixture.
Desktop/phone captures are in the drive-ui-20261022 tooling folder outside Git.
The final built frontend passed 26 browser assertions with no page errors,
covering menu anchoring, eight creation-only actions on desktop/phone, semantic
icon colours, arrow/Escape/outside/repeated-click behavior, native file and folder
pickers, folder form/details, list/grid, appearance controls, phone overflow and
Workspace dismissal. Desktop, dark, phone, New-menu and Workspace-overlay PNGs
were visually inspected. Fixture timing/chooser harness issues were corrected;
the final run uses readiness polling and an empty synthetic directory selection.

Windows build passed and was installed via normal manager stop, exact catalog
hash verification and a hidden restart. Only Drive binary/catalog metadata changed;
manager 0.3.0-preview.67, Photos and the other apps were retained. Drive version:
drive-v0.5.0-ui-fix.20261022. SHA256:
4c2c37998de5b579358fb835778ae87c1accbcec9d24b31109c7837d2f26a608.
The installed hash matches; anonymous loopback static-asset readback contains the
new account menu and folder toolbar. Protected config rollback is in the live
backups/drive-ui-20261022 folder; prior EXE is in the task tooling previous folder
on I:, avoiding a large C: archive. Go/frontend/cache/browser build work uses I:.

Whitespace check passes. The three lint findings above were also confirmed
against HEAD, so they predate this task. Live Workspace's served navigation JS
was checked to support the existing trigger contract. Final manager readiness,
remote availability and fixture process cleanup are complete. Startup reached
ready 27/27, with all 24 service entries running. Local and permanent HTTPS
health reported ok; remote public static readback returned 200 and contained the
new menu, account header and folder toolbar. The installed executable matches
the build hash above. The exact task-owned fixture process was stopped after
path/argument verification and loopback19374 has no listener. Layout.vue targeted
lint also passes. Browser acceptance is synthetic UI coverage; no claim of new
live-user file operations or full Office/auth regression acceptance is made.
Nothing committed, pushed or released and no Actions were run. Users should
refresh Drive (Ctrl+F5) to replace the old in-memory app shell.

## Release requested

User now explicitly authorizes pushing and releasing these changes. Publish the
UI and prior Photos upload fixes together as drive-v0.5.0-preview.4 in the public
oneworkspacestudio/drive repository, rebuilding both Windows x64 executables
locally with the release version/source commit. Include matching source,
licence/provenance assets, updater manifest and checksums. Actions remains
disabled. README now uses a fresh synthetic screenshot filename to avoid stale
image caching. Preserve both unrelated untracked preview files.
