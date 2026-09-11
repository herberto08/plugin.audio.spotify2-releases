# Spotify2 releases

Public download repository for Spotify2 Kodi release packages.

> **Spotify Premium required:** A Spotify Premium account is required to use Spotify2.
>
> Historical Kodi.tv discussion/support thread for the predecessor project [`glk1001/plugin.audio.spotify`](https://github.com/glk1001/plugin.audio.spotify): [Kodi forum thread](https://forum.kodi.tv/showthread.php?tid=265356&highlight=premium).

## Downloads

Open the [Spotify2 releases](https://github.com/herberto08/plugin.audio.spotify2-releases/releases) page and download the ZIP matching your Kodi platform:

- `plugin.audio.spotify2-<version>-WINDOWS.zip` — Windows
- `plugin.audio.spotify2-<version>-ARM.zip` — ARM Linux/LibreELEC and Android ARM
- `plugin.audio.spotify2-<version>-X86-MACOS.zip` — Linux/Android x86 and macOS

Do not extract the selected platform ZIP. In Kodi, open **Add-ons → Install from zip file** and select the downloaded package.

> **Recommended:** Restart Kodi once after installing or updating Spotify2 before starting the add-on.

Direct installation does not provide automatic updates. Download and install a newer package manually when a later release is published.

> **Important:** Use one of the three platform ZIP files listed above. GitHub's automatically generated **Source code (zip)** and **Source code (tar.gz)** archives are repository snapshots and are not installable Kodi add-ons.

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

## Spotify Web API limits and quota handling

Spotify2 uses the [Spotify Web API](https://developer.spotify.com/documentation/web-api) for browsing, metadata, and optional account-profile information. Each request is associated with the configured application **Client ID**. Using a personal Client ID can avoid sharing one application's traffic with unrelated installations, but it does not bypass Spotify's API limits. Applications running in development mode are also subject to Spotify's allowlist and quota restrictions.

- **Rate limits:** Spotify calculates application-wide request rates over a rolling 30-second window and does not publish a single fixed request limit that applies to every application. Spotify2 therefore uses a process-shared API gate, enforces at least two seconds between requests, and applies a conservative local budget of eight requests per 30 seconds. HTTP `429` responses honor Spotify's `Retry-After` value, while cached data is reused where safe instead of immediately repeating requests.

- **`GET /v1/me`:** Spotify does not document an endpoint-specific limit of ten calls for this endpoint. Spotify2 uses `/v1/me` only for optional background profile restoration, reuses the persisted profile, and suppresses repeated profile requests for one hour. A failed or rate-limited profile request does not invalidate the user's credentials or playback token.

- **Artist albums:** Spotify's current documentation specifies a **default page size of 5 and a maximum of 10 items per request** for `GET /v1/artists/{id}/albums`. This is a response page-size restriction—not a limit of ten requests or ten albums in total. Spotify2 handles this restriction transparently and uses Spotify's pagination to assemble larger Kodi browse pages while keeping each individual API request within the endpoint's maximum of 10 items. Starting with v1.2.20, the combined artist album/single browse view also includes **compilations** (`album,single,compilation`). Market selection continues to follow the authenticated Spotify account; no country such as `SK` or `DE` is hard-coded. See [Get Artist's Albums](https://developer.spotify.com/documentation/web-api/reference/get-an-artists-albums).

- **Other endpoint-specific limits:** Spotify Web API limits are not necessarily identical across endpoints and may change over time. The artist-albums restriction must therefore not be treated as a global Web API limit. Spotify2 keeps endpoint-specific restrictions separate so that APIs allowing larger page sizes can continue to use them.

- **Quota modes:** [Development mode and extended quota mode](https://developer.spotify.com/documentation/web-api/concepts/quota-modes) are separate from the rolling rate limit. Development-mode requests can additionally be subject to endpoint quota buckets whose grouping and exact limits may be changed by Spotify. Spotify2 distinguishes ordinary rate limiting from `QUOTA_EXCEEDED`, stops further calls through the shared API gate when required, and avoids caching a quota-gated empty response as a successful result.

Spotify tightened several Web API restrictions in 2026. For example, the `/search` endpoint is limited to a maximum of **10 results per request**, while other endpoints continue to support larger page sizes. Spotify2 therefore treats documented page-size restrictions as **endpoint-specific compatibility requirements** rather than assuming one global maximum for all Spotify Web API calls.

For further details, see Spotify's current [Web API documentation](https://developer.spotify.com/documentation/web-api), [rate-limit documentation](https://developer.spotify.com/documentation/web-api/concepts/rate-limits), and the endpoint reference for [Get Artist's Albums](https://developer.spotify.com/documentation/web-api/reference/get-an-artists-albums).

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

Spotify2 is distributed under the [GNU General Public License v3.0](LICENSE).
