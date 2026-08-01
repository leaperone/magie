# Magie Desktop release

Magie publishes macOS Desktop builds from `leaperone/magie` through `.github/workflows/release.yml`.

## Release contract

- Push a tag named `desktop-vX.Y.Z` from verified `main`.
- The workflow validates the tag, runs check/typecheck/tests, builds arm64 and x64 macOS DMG + ZIP artifacts, merges the two `latest-mac.yml` manifests, and creates the GitHub Release.
- ZIP and blockmap assets remain on the Release, so `electron-updater` can fetch the GitHub update metadata and use differential downloads.
- The packaged app checks for updates shortly after launch and then at the normal polling interval. It downloads and installs only after the user confirms in the app.

## macOS signing

Unsigned builds can be published, but are not a supported automatic-update delivery path. To produce signed and notarized builds, configure these repository values before tagging a production release:

- secrets: `CSC_LINK`, `CSC_KEY_PASSWORD`, `APPLE_API_KEY`, `APPLE_API_KEY_ID`, `APPLE_API_ISSUER`, `MACOS_PROVISIONING_PROFILE`
- variables: `APPLE_TEAM_ID`, plus the existing Clerk values when passkeys are enabled

The workflow adds `--signed` only when the complete set is present; otherwise it publishes an unsigned build. Verify the downloaded app's signature and a real update from one released Magie version to the next before treating automatic installation as production-ready.
