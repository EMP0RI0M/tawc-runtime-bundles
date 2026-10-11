# TAWC runtime bundles (arm64)

Durable, anonymously-fetchable host for the **signed runtime-bundle overlays** the
TAWC Android app installs into its own on-device Linux (tawcroot) guest — the
gateway binary, Goose agent, CLI proxy, core runtime and skills.

## Trust model
The app does **not** trust this host. `BundleClient` fetches `manifest.json` +
`manifest.json.sig`, verifies the Ed25519 signature against a **public key baked
into the APK** (`BundleSigningKeys.SPKI_PEM`, fingerprint
`9e007e8abd480a62894890b7ed73551ade74ad0a93044e4b9827765f939881b8`), then re-hashes
every archive against the signed manifest **before extracting** (fail closed).
Serving files here therefore requires no secret — only the (private, off-repo)
signing key can produce a manifest the app accepts. This is the same model as a
Debian/apt mirror.

## Layout (flat, per Release tag)
`releases/download/<tag>/` must contain:
- `manifest.json`, `manifest.json.sig`, `manifest.pub.pem`
- one `.tar.zst` per overlay: `core`, `gateway`, `agent-goose`, `proxy`, `skills`

Built by `tawc-platform/tools/build-runtime-bundles.sh`.
