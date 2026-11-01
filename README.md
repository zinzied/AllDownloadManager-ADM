<p align="center">
 <img width="1191" height="637" alt="image" src="https://github.com/user-attachments/assets/ed77ab4d-bc17-4ae6-a94d-eed09811b8bd" />

</p>

<h1 align="center">ADM — All Download Manager</h1>

<p align="center">
  <b>Fast, reliable, cross-platform download manager for Windows.</b><br>
  Multi-connection acceleration, resume, video/audio extraction, torrents, FTP, and browser integration.
</p>

<p align="center">
  <a href="https://github.com/zinzied/AllDownloadManager-ADM/releases/latest"><img alt="Latest release" src="https://img.shields.io/github/v/release/zinzied/AllDownloadManager-ADM?style=flat-square&label=Release"></a>
  <a href="https://github.com/zinzied/AllDownloadManager-ADM/actions/workflows/build.yml"><img alt="CI" src="https://img.shields.io/github/actions/workflow/status/zinzied/AllDownloadManager-ADM/build.yml?style=flat-square&label=CI"></a>
  <a href="https://github.com/zinzied/AllDownloadManager-ADM/blob/main/LICENSE"><img alt="License" src="https://img.shields.io/github/license/zinzied/AllDownloadManager-ADM?style=flat-square"></a>
  <a href="https://github.com/zinzied/AllDownloadManager-ADM/releases/latest/download/ADM-Setup.exe"><img alt="Windows installer" src="https://img.shields.io/badge/Windows-installer-blue?style=flat-square"></a>
</p>

---

## Features

### 🚀 Accelerated Downloads
- **Dynamic segmentation** — splits a file into multiple parts, each downloaded on its own connection
- **Work stealing** — when a connection finishes early it takes half of the biggest remaining segment
- **Per-host connection limits** — the engine learns a server's cap from `429/503` responses so you never get throttled or banned
- **Resume with integrity** — on restart, the server's `ETag` is compared with the stored one; a changed file clears old parts so nothing corrupt is resumed
- **Checksum verification** — after a download finishes, the file is hashed (`SHA-256`, `SHA-1`, `MD5`) against a sum from the URL fragment (`#sha256=…`) or a sidecar file (`.sha256` / `.sha1` / `.md5`)

### 📺 Video & Audio
- **YouTube / generic extractor** — paste a video page, pick quality, ADM fetches the direct streams with `yt-dlp`
- **DASH (video + audio)** — downloads both streams in parallel, merges with `ffmpeg` into one file
- **HLS (m3u8)** — joins all segments into a single `.ts` (or `.mp4` when `ffmpeg` is available)
- **Audio-only / MP3** — extract the sound track and encode to MP3 at once

### 🌐 Protocol Support
| Protocol | Details |
|---|---|
| **HTTP / HTTPS** | Multi-connection, ranges, redirects, cookies, auth, proxy |
| **FTP / FTPS** | Passive mode, explicit TLS, `REST` resume, single connection |
| **BitTorrent** | `.torrent` files and magnet links, file selection, seeding limits, uses bundled `aria2c` |

### 🧠 Smart Queue & Scheduler
- Multiple named queues, each with its own parallelism limit
- Start / stop queues, move files between queues
- Scheduler: run downloads only in certain time windows, shut down / sleep / hibernate when the queue finishes

### 🔌 Browser Integration
- **Chrome / Edge / Firefox** extension (Manifest V3)
- Right-click a link → "Download with ADM"
- Toolbar badge: shows how many downloads are running; turns blue when the current page has detectable media
- **Drop zone** — drag links or selected text from the browser onto the ADM window; all URLs and magnet links are picked up automatically
- Intercepts browser downloads and hands them to ADM (with referrer & cookies so login-required files work)

### 🛡️ Safety & Integrity
- Every helper binary (`yt-dlp`, `aria2c`, `ffmpeg`) is **SHA-256 verified** against the publisher's official checksums before use
- Signed self-updates (Ed25519) — the updater only accepts releases signed with the project's private key
- Mark-of-the-Web on downloaded files; optional virus scan on completion

### 🎨 UI / UX
- 9 built-in themes (Light, Dark, Ocean blue, Midnight purple, Forest green, Black, Sunset, Teal night, Rose)
- Per-category folders, custom columns, column reorder / resize
- Multiple selection, drag-to-reorder queue, drag finished file out to Explorer
- System tray: hide to tray, shows progress of hidden downloads, limiter toggle, scheduler, about
- High-DPI aware, RTL layout for Arabic

---

## Installation

### Windows (recommended)
1. Download **`ADM-Setup.exe`** from the [latest release](https://github.com/zinzied/AllDownloadManager-ADM/releases/latest)
2. Run the installer — it creates a Start Menu entry and registers the browser extension
3. Open ADM → **Help ▸ Add ADM to my browser…** to install the extension

### Portable
- Download **`ADM-Portable.zip`**, unzip anywhere, run `ADM.exe`
- No admin rights, no registry writes, settings stay in the folder

### Browser Extension (manual)
- **Chrome / Edge**: `chrome://extensions` → Developer mode → Load unpacked → select `extension/`
- **Firefox**: `about:debugging` → This Firefox → Load Temporary Add-on → select `extension/manifest.firefox.json`

---

## Building from Source

```bash
# prerequisites
# - Rust 1.80+ (stable)
# - Windows 10/11 SDK (for the native tray / drag-drop code)

git clone https://github.com/zinzied/AllDownloadManager-ADM.git
cd AllDownloadManager-ADM
cargo build --release
# executable: target/release/ADM.exe
```

The one-click build script (Windows PowerShell):
```powershell
.\build-windows.ps1
# produces ADM-Setup.exe and ADM-Portable\ADM.exe
```

---

## Configuration

All settings are stored in `%APPDATA%\ZieDev\ADM\` (or next to the portable exe):
- `settings.json` — UI, queues, scheduler, limiter, file types, proxy, etc.
- `downloads.json` — the download list (resumes across restarts)

**Migration**: on first run ADM copies `settings.json` / `downloads.json` from older folders (`%APPDATA%\Ezoo\EzooDownload`, `%APPDATA%\SwiftGet`, `%APPDATA%\AllDownloader`) so nothing is lost.

---

## Project Structure

```
crates/
  app/           # Slint UI, system tray, win32 drag-drop, extension bridge
  engine/        # Core downloader: HTTP, FTP, HLS, DASH, torrent, checksum, host-limits
extension/       # Browser extension (MV3) — background, content, popup, icons
installer/       # Inno Setup script (adm.iss), update signing key
dist/            # Portable package (ADM.exe + extension + tools)
```

---

## License

MIT — see [LICENSE](LICENSE).  
Third-party notices: [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt)

---

## Credits

- **Rust crates**: tokio, reqwest, serde, chrono, slint, ring, suppaftp, sha2, sha1, md-5, and many more (MIT / Apache-2.0)
- **External tools** (bundled, verified at runtime):
  - `yt-dlp` — video extraction (Unlicense)
  - `aria2c` — BitTorrent (GPL-2.0+)
  - `ffmpeg` — media merge / transcode (LGPL-3.0+)
  - `lame` — MP3 encoding (LGPL)

---

## Links

- **Releases**: https://github.com/zinzied/AllDownloadManager-ADM/releases
- **Issues**: https://github.com/zinzied/AllDownloadManager-ADM/issues
- **Source**: https://github.com/zinzied/AllDownloadManager-ADM
