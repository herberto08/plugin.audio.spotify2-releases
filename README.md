# Spotify2 releases

Public download repository for Spotify2 Kodi release packages.

> **Spotify Premium required:** A Spotify Premium account is required to use Spotify2.
>
> Historical Kodi.tv discussion/support thread for the predecessor project [`glk1001/plugin.audio.spotify`](https://github.com/glk1001/plugin.audio.spotify): [Kodi forum thread](https://forum.kodi.tv/showthread.php?tid=265356&highlight=premium).

## Downloads

Open the [Spotify2 releases](https://github.com/herberto08/plugin.audio.spotify2-releases/releases) page and download the ZIP matching your Kodi platform:

- `plugin.audio.spotify2-<version>-WINDOWS.zip` — Windows 10/11 **64-bit (x64)**
- `plugin.audio.spotify2-<version>-WINDOWS-32BIT.zip` — Windows 10 **32-bit (x86)**
- `plugin.audio.spotify2-<version>-ARM.zip` — ARM Linux/LibreELEC and Android ARM
- `plugin.audio.spotify2-<version>-X86-MACOS.zip` — Linux/Android x86 and macOS

The Windows packages are architecture-specific. Use `WINDOWS.zip` with a 64-bit Kodi installation and `WINDOWS-32BIT.zip` only with a 32-bit Kodi installation on Windows 10. Windows 11 does not provide a 32-bit edition.

Do not extract the selected platform ZIP. In Kodi, open **Add-ons → Install from zip file** and select the downloaded package.

> **Recommended:** Restart Kodi once after installing or updating Spotify2 before starting the add-on.

Direct installation does not provide automatic updates. Download and install a newer package manually when a later release is published.

> **Important:** Use one of the four platform ZIP files listed above. GitHub's automatically generated **Source code (zip)** and **Source code (tar.gz)** archives are repository snapshots and are not installable Kodi add-ons.

## Spotty architecture

Spotify2 intentionally separates Spotty roles and generations.

### Legacy Spotty v1.x / librespot 0.4.2

Used for Device Connect/authentication/token operations where required, and as the current complete runtime on some legacy X86/macOS branches.

### Modern Spotty v2.x / librespot 0.8.x

Used for dedicated playback on supported Windows and ARM targets.

### Windows 10 32-bit package

v1.2.21 additionally provides a dedicated package for **Windows 10 32-bit (x86)**:

`plugin.audio.spotify2-v1.2.21-WINDOWS-32BIT.zip`

This package is intended only for a 32-bit Kodi installation. It contains two native PE32/i386 binaries:

- **Spotty v1.3.1 / librespot 0.4.2** for Device Connect, authentication and token operations.
- **Spotty v2.1.2 / librespot 0.8.0** for playback and audio prefetch.

The modern playback binary was built from [`michaelherger/librespot` commit `3b817a9`](https://github.com/michaelherger/librespot/commit/3b817a95728bac7c3b11e6a3621c725ca65854fa), matching the Spotty v2.1.2 source generation used by the existing Windows x64 playback package.

The 32-bit package contains no x64 executable and no x64 `msvcr110.dll`. Apart from the architecture-specific Windows binaries and removal of the unused x64 runtime DLL, its add-on files are byte-identical to the regular v1.2.21 Windows package.

### Current platform policy

- Windows 10/11 x64: legacy x64 authentication payload for Device Connect/auth/token plus a dedicated modern Windows x64 playback payload.
- Windows 10 x86/32-bit: dedicated native 32-bit package containing legacy Spotty v1.3.1 / librespot 0.4.2 for Device Connect/auth/token and modern Spotty v2.1.2 / librespot 0.8.0 for playback and prefetch. The package contains no x64 executable or x64 runtime DLL. **Not user-tested:** the package passed build, PE architecture, Python and `addon.xml` validation, but has not yet been runtime-tested on a real Windows 10 32-bit system.
- ARM Android: legacy auth payload plus dedicated modern ARMv7/AArch64 playback payloads.
- ARM Linux/LibreELEC: legacy auth-compat Device Connect/token path plus platform playback payload.
- Linux X86/i386/`x86_64`: native legacy Spotty for auth/token/playback in the current released branch. **Not user-tested:** no user test has yet been completed for this branch.
- macOS Intel/Apple Silicon: universal legacy Spotty for auth/token/playback in the current released branch. **Not user-tested:** no user test has yet been completed for this branch.
- Android X86/`x86_64`: legacy auth/token payloads only; no silent legacy playback fallback. **Not user-tested:** no user test has yet been completed for this branch.

## Spotify Web API limits and quota handling

Spotify2 uses the [Spotify Web API](https://developer.spotify.com/documentation/web-api) for browsing, metadata, and selected account-profile information. Web API requests use the configured application **Client ID**. Using a personal Client ID can separate an installation's normal app-level rate-limit traffic from installations using another Client ID, such as the add-on's shared/default application, but it does not bypass Spotify's API limits.

Applications running in **Development Mode** are additionally subject to Spotify quota restrictions. Since Spotify's July 2026 quota changes, Development Mode quota usage is counted **per developer account rather than per individual Client ID**, so multiple Development Mode Client IDs owned by the same developer share the same quota pool.

- **Rate limits:** Spotify calculates application-wide request rates over a rolling **30-second window** and does not publish a single fixed request limit that applies to every application. Spotify2 therefore adds its own conservative protection on top of Spotify's limits: Web API calls are serialized across Kodi processes, spaced by at least **two seconds**, and restricted by a local budget of **eight requests per 30 seconds**. These values are Spotify2 safeguards, not Spotify-defined limits. HTTP `429` responses honor Spotify's `Retry-After` value, while cached data is reused where safe instead of immediately repeating requests.

- **`GET /v1/me`:** Spotify does not document an endpoint-specific limit of ten calls for this endpoint. Normal Spotify2 navigation does **not** call `/v1/me`. Spotify2 primarily restores the account identity from its persisted local profile and Device Connect state. After a successful Device Connect, or when an explicit controlled profile refresh is allowed, Spotify2 can perform one `/v1/me` request and persist the returned profile information. Repeated profile requests are suppressed for **one hour**. A failed or rate-limited profile request does not invalidate the user's Device Connect credentials or playback token.

- **Artist albums:** Spotify's current documentation specifies a **default page size of 5 and a maximum of 10 items per request** for `GET /v1/artists/{id}/albums`. This is a response page-size restriction—not a limit of ten requests or ten albums in total. Spotify2 handles this restriction transparently and uses Spotify's pagination to assemble larger Kodi browse pages while keeping each individual API request within the endpoint's maximum of 10 items. Starting with v1.2.20, the combined artist release view supports **albums, singles, appearances and compilations** (`album,single,appears_on,compilation`). Market selection continues to follow the authenticated Spotify account. See [Get Artist's Albums](https://developer.spotify.com/documentation/web-api/reference/get-an-artists-albums).

- **Search:** Spotify changed the `GET /search` endpoint for Development Mode in 2026. Its current maximum is **10 results per item type per request**, with a default of 5. Spotify2 therefore fetches larger Kodi search pages through pagination instead of requesting more than 10 items in one Search API call.

- **Other endpoint-specific limits:** Spotify Web API limits are not necessarily identical across endpoints and may change independently over time. The 10-item restrictions for Artist Albums and Search must therefore not be treated as a global Web API limit. Spotify2 keeps endpoint-specific restrictions separate so that APIs allowing larger page sizes can continue to use their documented limits.

- **Quota modes:** [Development Mode and Extended Quota Mode](https://developer.spotify.com/documentation/web-api/concepts/quota-modes) are separate from the normal rolling rate limit. Development Mode can additionally be subject to quota buckets whose endpoint grouping and exact limits may be changed by Spotify. Since July 2026, these Development Mode quotas are aggregated across the Client IDs belonging to the same developer account. When the quota is exceeded, Spotify can return HTTP `429` with `reason: "QUOTA_EXCEEDED"`. Spotify2 distinguishes this condition from an ordinary rate-limit response, stops further calls through the shared API gate when required, and avoids caching a quota-gated empty response as successful API data.

Spotify introduced several Web API compatibility changes during 2026, including reduced endpoint-specific page sizes and Development Mode quota changes. Spotify2 therefore treats documented page-size restrictions, ordinary rate limiting, and Development Mode quota exhaustion as **separate mechanisms** rather than assuming one global maximum for all Spotify Web API calls.

For further details, see Spotify's current [Web API documentation](https://developer.spotify.com/documentation/web-api), [rate-limit documentation](https://developer.spotify.com/documentation/web-api/concepts/rate-limits), [quota-mode documentation](https://developer.spotify.com/documentation/web-api/concepts/quota-modes), the [February 2026 Development Mode migration guide](https://developer.spotify.com/documentation/web-api/tutorials/february-2026-migration-guide), and the endpoint reference for [Get Artist's Albums](https://developer.spotify.com/documentation/web-api/reference/get-an-artists-albums).

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
