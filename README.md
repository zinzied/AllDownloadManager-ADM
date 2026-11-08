<p align="center">
 <img width="1191" height="637" alt="image" src="https://github.com/user-attachments/assets/ed77ab4d-bc17-4ae6-a94d-eed09811b8bd" />

</p>

<p align="center">
  <img src="crates/app/ui/icon.png" width="120" alt="ADM">
</p>

<h1 align="center">ADM — All Download Manager</h1>

<p align="center">
  <b>Fast, reliable, cross-platform download manager for Windows.</b><br>
  Multi-connection acceleration, resume, video/audio extraction, torrents, FTP, and browser integration.
</p>

<p align="center">
  <a href="https://github.com/zinzied/AllDownloadManager-ADM/releases/latest"><img alt="Latest release" src="https://img.shields.io/github/v/release/zinzied/AllDownloadManager-ADM?style=flat-square&label=Release"></a>
  <a href="https://github.com/zinzied/AllDownloadManager-ADM/actions/workflows/build.yml"><img alt="CI" src="https://img.shields.io/github/actions/workflow/status/zinzied/AllDownloadManager-ADM/build.yml?style=flat-square&label=CI"></a>
  <a href="https://github.com/zinzied/AllDownloadManager-ADM/releases/latest/download/ADM-Setup.exe"><img alt="Windows installer" src="https://img.shields.io/badge/Windows-installer-blue?style=flat-square"></a>
</p>

<p align="center">
  <a href="README.md"><b>🇬🇧 English</b></a> · <a href="README.ar.md">🇸🇦 العربية</a>
</p>

---

## Features

### 🚀 Accelerated Downloads
- **Dynamic segmentation** — splits a file into multiple parts, each downloaded on its own connection
- **Work stealing** — when a connection finishes early it takes half of the biggest remaining segment
- **Per-host connection limits** — the engine learns a server's cap from `429/503` responses so you never get throttled or banned; a server that allows only a few connections at a time continues by itself with fewer, and that site's next downloads start with that many
- **No waiting for ever** — when a server stops answering ADM asks again by itself, so a video or file never stays at 0 KB/s or fails to start, without Pause / Resume
- **A link that expired** is renewed by itself and checked first, then the download goes on from where it was
- **The connection count changes at once**, even while a file downloads, and a connection much slower than the others is made again so the speed stays steady
- **Resume with integrity** — on restart, the server's `ETag` is compared with the stored one; a changed file clears old parts so nothing corrupt is resumed; a file that changed on the server while it was paused starts again instead of mixing old and new
- **Big files** — a second bar shows the joining of picture and sound and the moving of the file to its folder, big videos are joined in about half the time, and a folder picked in Download File Info takes the file there at once
- **Checksum verification** — after a download finishes, the file is hashed (`SHA-256`, `SHA-1`, `MD5`) against a sum from the URL fragment (`#sha256=…`) or a sidecar file (`.sha256` / `.sha1` / `.md5`)

### 📺 Video & Audio
- **YouTube / generic extractor** — paste a video page, pick quality, ADM fetches the direct streams with `yt-dlp`
- **Built-in video player** ⭐ — watch a torrent streaming directly from peers inside ADM's own player window (no external program): play, pause, seek (the seek target is fetched first, so skipping is instant), speed, and fullscreen where supported; in-torrent SRT/WebVTT subtitles show via the **CC** control
- **DASH (video + audio)** — downloads both streams in parallel, merges with `ffmpeg` into one file
- **HLS (m3u8)** — joins all segments into a single `.ts` (or `.mp4` when `ffmpeg` is available)
- **Audio-only / MP3** — extract the sound track and encode to MP3 at once, at the quality you pick
- **Playlists** — a whole playlist at 240p / 144p too
- **Subtitles** — download a video's subtitles as well (Options ▸ Downloads)
- **Download a part of a video** ⭐ — choose from / to, or "Now" while it plays, look at the part first, then only that part is downloaded, with a quick cut or an exact cut (also in the add-in's quality list and Tasks ▸ Download a part of a video)
- **Protected videos (DRM)** — said plainly with a lock instead of an unclear error
- **Closed groups / friends-only posts** — Facebook and Instagram videos are found with the browser's sign-in; an AV1 video (sound without picture in older players) says so

### 🌐 Protocol Support
| Protocol | Details |
|---|---|
| **HTTP / HTTPS** | Multi-connection, ranges, redirects, cookies, auth, proxy |
| **FTP / FTPS** | Passive mode, explicit TLS, `REST` resume, single connection |
| **BitTorrent** | `.torrent` files and magnet links, file selection, seeding limits, uses bundled `aria2c` for downloads and `librqbit` for the built-in streaming player |

**Torrents in detail:** a magnet link shows its files first so you can choose them; a `.torrent` link in the browser opens the torrent window at once and a `.torrent` file can be dropped on the window; public trackers find many more people to download from, and the window shows how many are connected and tells you when nobody shares the torrent. **Watch in ADM** streams the selected video straight from the swarm inside ADM's own player window (`librqbit` downloads pieces from peers on demand, with in-torrent SRT/WebVTT subtitles via the CC control) — no external player, no waiting for the whole file.

### 🧠 Smart Queue & Scheduler
- Multiple named queues, each with its own parallelism limit
- Start / stop queues, move files between queues
- Scheduler: run downloads only in certain time windows, shut down / sleep / hibernate when the queue finishes

### 🔌 Browser Integration
- **Chrome / Edge / Firefox** extension (Manifest V3)
- Right-click a link → "Download with ADM"
- Toolbar badge: shows how many downloads are running; turns blue when the current page has detectable media
- **Drop zone** — drag links or selected text from the browser onto the ADM window; all URLs and magnet links are picked up automatically
- **Intercepts browser downloads** and hands them to ADM (with referrer & cookies so login-required files work); a click on a file link goes straight to ADM without the browser's own Save As window (Brave too)
- **PDF links** go straight to ADM — no second window asking where to save from the PDF viewer of Edge and Brave
- **Sites behind Cloudflare (HTTP 403)** that refused ADM while the browser and other download managers got the file now get asked the way the browser asks; if a site still refuses, the browser is asked for the file and hands it back with the site's sign-in
- **Links that work once** (ChatGPT files, iLovePDF and other links that need the page's sign-in) stay in the browser instead of failing
- **The add-in survives updates** — the setup puts it in again whole, Options ▸ Browsers has **"Repair the add-in"**, and its window has "Connect again"
- **Firefox** — the add-in is signed again and updates by itself

### 🛡️ Safety & Integrity
- Every helper binary (`yt-dlp`, `aria2c`, `ffmpeg`) is **SHA-256 verified** against the publisher's official checksums before use; `ffmpeg.exe` is downloaded again by itself if an antivirus removed it
- Signed self-updates (Ed25519) — the updater only accepts releases signed with the project's private key
- **The add-in talks to the program on a port chosen at random** on this computer, and every request is signed with a secret key (the key itself is never sent); watching the clipboard is off until you turn it on
- Mark-of-the-Web on downloaded files; optional virus scan on completion
- Every part is checked before it is written, and a moved file never replaces another one

### 🎨 UI / UX
- 9 built-in themes (Light, Dark, Ocean blue, Midnight purple, Forest green, Black, Sunset, Teal night, Rose)
- Per-category folders, custom columns, column reorder / resize
- Multiple selection, drag-to-reorder queue, **drag the finished file out** ("Drag me" onto the desktop, a folder, a chat or any program)
- System tray: hide to tray, shows progress of hidden downloads, limiter toggle, scheduler, about
- High-DPI aware, RTL layout for Arabic; the shortcuts (Ctrl+A, Ctrl+N…) work with the Arabic keyboard too
- Windows that Windows had cleared are drawn whole again; the toolbar grows with bigger text

---

## Installation

### Windows (recommended)
1. Download **`ADM-Setup.exe`** from the [latest release](https://github.com/zinzied/AllDownloadManager-ADM/releases/latest)
2. Run the installer — it creates a Start Menu entry
3. Open ADM → **Help ▸ Add ADM to my browser…** to install the extension

### Portable
- Download **`ADM-Portable.zip`**, unzip anywhere, run `ADM.exe`
- No admin rights, no registry writes
- Settings and the download list live in `%APPDATA%\ZieDev\ADM` (the same place the installed copy uses, so the two share them)
- A portable copy **does not update itself** — to upgrade, replace the files

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

## Troubleshooting

**360 Total Security (or another antivirus) alerts on `yt-dlp.exe` → `_MEI…\ucrtbase.dll`**
This is a false positive. `yt-dlp` is packed with PyInstaller, so it unpacks itself — including
`ucrtbase.dll` — into a temporary `_MEI<number>` folder on every run. ADM now keeps those files
in the **system** temporary folder (where such programs normally live and where antivirus products
expect them) and sweeps old folders away by itself. If the alert persists, add ADM's folder to
the antivirus exclusions.

**The program closes at start** — Options ▸ General → turn software drawing back on.

**The add-in does not connect** — Options ▸ Browsers → **"Repair the add-in"**.

**Joining picture and sound fails** — an antivirus may have removed `ffmpeg.exe`; ADM downloads it again by itself.

---

## License

This is a private build of ADM, maintained by ZieDev for personal use. It is
not distributed under an open-source license and is not intended for
redistribution. Third-party components remain under their own licenses:
[THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt)

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
