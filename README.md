# Spotify2 releases

Public download repository for Spotify2 Kodi release packages.

> **Spotify Premium required:** A Spotify Premium account is required to use Spotify2.

## Downloads

Open the [Spotify2 releases](https://github.com/herberto08/plugin.audio.spotify2-releases/releases) page and download the ZIP matching your Kodi platform:

- `plugin.audio.spotify2-<version>-WINDOWS.zip` — Windows
- `plugin.audio.spotify2-<version>-ARM.zip` — ARM Linux/LibreELEC and Android ARM
- `plugin.audio.spotify2-<version>-X86-MACOS.zip` — Linux/Android x86 and macOS
- `plugin.audio.spotify2-<version>-source.zip` — corresponding source and build material

Do not extract the selected platform ZIP. In Kodi, open **Add-ons → Install from zip file** and select the downloaded package.

Direct installation does not provide automatic updates. Download and install a newer package manually when a later release is published.

## Spotty architecture

Spotify2 intentionally separates Spotty roles and generations.

### Legacy Spotty v1.x / librespot 0.4.2

Used for Device Connect/authentication/token operations where required, and as the current complete runtime on some legacy X86/macOS branches.

### Modern Spotty v2.x / librespot 0.8.x

Used for dedicated playback on supported Windows and ARM targets.

### Current platform policy

- Windows: legacy authentication payload for Device Connect/auth/token plus a dedicated modern Windows x64 playback payload.
- ARM Android: legacy auth payload plus dedicated modern ARMv7/AArch64 playback payloads.
- ARM Linux/LibreELEC: legacy auth-compat Device Connect/token path plus platform playback payload.
- Linux X86/i386/`x86_64`: native legacy Spotty for auth/token/playback in the current released branch. **Not user-tested:** no user test has yet been completed for this branch.
- macOS Intel/Apple Silicon: universal legacy Spotty for auth/token/playback in the current released branch. **Not user-tested:** no user test has yet been completed for this branch.
- Android X86/`x86_64`: legacy auth/token payloads only; no silent legacy playback fallback. **Not user-tested:** no user test has yet been completed for this branch.

## Repository purpose

This repository contains only official release downloads, minimal installation documentation and licensing information. Issues, pull requests, projects, wiki and discussions are disabled.

## Disclaimer and support

Spotify2 is an independent, unofficial community project and is provided **as is**, without warranty, guarantee of functionality or any commitment to provide support. It is not affiliated with, endorsed, sponsored or approved by Spotify, the Kodi project or the maintainers of its predecessor projects. Use it at your own risk.

Existing license terms and attribution files remain authoritative.

## Project origin

Spotify2 began from [**`glk1001/plugin.audio.spotify` v1.3.14**](https://github.com/glk1001/glk1001.github.io/tree/master) (2025-02-10), itself part of the long-running Kodi Spotify add-on lineage. The early Spotify2 README also preserved attribution to Marcel Veldt's earlier add-on work and thanked Ldsz, Elkropac and FernetMenta for the Python 3.9+ transition.

## Upstream and acknowledgements

Spotify2 builds on the work of the Kodi Spotify add-on maintainers, including the Kodi community line and the later `glk1001/plugin.audio.spotify` updates. Historical project documentation also credits Marcel Veldt, Ldsz, Elkropac and FernetMenta.

Spotty/librespot work by [Michael Herger](https://github.com/michaelherger/librespot) and the [librespot contributors](https://github.com/librespot-org/librespot) provides the native Spotify transport/playback foundation used by this project. Existing license and attribution files remain authoritative.

Spotify2 is distributed under the [GNU General Public License v3.0](LICENSE). Corresponding source and required build material are published with each release.
