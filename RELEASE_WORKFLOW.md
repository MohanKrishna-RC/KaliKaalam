# Release workflow for KaliKaalam

This project keeps the application source private while publishing browser-ready ZIP files and a public README. Follow these steps for each release.

## 1. Check the repository before editing

- Confirm the project root and read `package.json`, `README.md`, and `scripts/ship-extension.mjs`.
- Check `git status` and preserve any user edits already in progress.
- Identify the current public `main` commit before publishing. The local checkout may have a different history, so do not assume its `main` branch is safe to push.
- Do not publish the application source (`src/`, `extension/`, build configuration, dependencies, or local agent files). The public repository is for the README, release downloads, and approved public assets.

## 2. Prepare the release

1. Choose the next version and update `package.json` plus the top-level project version fields in `package-lock.json`.
2. Update `README.md`: download links, displayed version, installation links, and the release summary. Remove feature claims that no longer match the app.
3. Build from a Linux writable temporary directory if the mounted project folder causes Vite permission or temporary-file errors. Copy the project inputs there and link its `node_modules` to the installed dependencies; do not copy dependencies into the release ZIPs.

Example setup from the project root:

```sh
BUILD_DIR="/tmp/kalikaalam-build-VERSION"
mkdir -p "$BUILD_DIR"
cp package.json package-lock.json index.html postcss.config.js tailwind.config.js vite.config.js "$BUILD_DIR/"
cp -a src extension scripts "$BUILD_DIR/"
ln -s "$PWD/node_modules" "$BUILD_DIR/node_modules"
```

Replace `VERSION` with the selected release number. Then run:

```sh
cd "$BUILD_DIR"
npm run build
node scripts/ship-extension.mjs
```

The build creates `dist/app`, `dist/chrome`, and `dist/firefox`. The packager writes browser ZIP files into `release/` when the `zip` command is available. It creates Manifest V3 packages for Chromium browsers and Firefox.

If `zip` is unavailable, create archives with Python's standard library. Run this from the temporary build directory, replacing `VERSION`:

```sh
python3 - <<'PY'
from pathlib import Path
from zipfile import ZIP_DEFLATED, ZipFile

version = "VERSION"
for browser in ("chrome", "firefox"):
    source = Path("dist") / browser
    archive = Path("release") / f"kalikaalam-{browser}-{version}.zip"
    with ZipFile(archive, "w", ZIP_DEFLATED) as output:
        for file in source.rglob("*"):
            if file.is_file():
                output.write(file, file.relative_to(source))
PY
```

## 3. Update local release folders first

Copy the three generated build folders into the project `dist/`, replacing stale generated files. Keep older versioned archives in `release/` and add the new ZIPs there. Do not clean unrelated project files.

```sh
rsync -a --delete "$BUILD_DIR/dist/app/" dist/app/
rsync -a --delete "$BUILD_DIR/dist/chrome/" dist/chrome/
rsync -a --delete "$BUILD_DIR/dist/firefox/" dist/firefox/
cp "$BUILD_DIR/release/kalikaalam-chrome-VERSION.zip" release/
cp "$BUILD_DIR/release/kalikaalam-firefox-VERSION.zip" release/
```

Confirm both `dist/chrome/manifest.json` and `dist/firefox/manifest.json` show the new version. Check each ZIP with `ZipFile.testzip()` and ensure its root contains `manifest.json` and `index.html`.

## 4. Publish without exposing source

Publish only the approved public files to the existing GitHub `main` branch:

- `README.md`
- `RELEASE_WORKFLOW.md`
- `release/kalikaalam-chrome-VERSION.zip`
- `release/kalikaalam-firefox-VERSION.zip`

Use the connected GitHub tools to read the current `main` commit/tree, create blobs for those files, create a new tree based on the existing tree, create a commit with the current main commit as its parent, and advance `main` with an expected-head check. This preserves all existing public files and avoids pushing the local source tree. If the expected head changed, inspect the new head and rebuild the tree on top of it; do not force-push.

Do not use `git add -A` or push the entire local checkout. Do not create a downloads branch. Do not remove prior versioned downloads as part of a normal release.

## 5. Verify the publication

- Read the remote README and confirm its version and links point to the new archives.
- Fetch both remote ZIP paths and confirm the commit contains only the approved files listed above.
- Confirm the published ZIPs match the local `release/` files and pass archive integrity checks.
- Report the commit and direct download links.

## Current release notes

As of version 1.0.6, the page-wide ambient animation has been removed. The clock card's portal animation remains. Keep README wording clear about this distinction.
