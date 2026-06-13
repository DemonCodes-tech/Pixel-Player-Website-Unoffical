# PixelPlayer

A feature-rich web music player that lets users play their local audio files directly in the browser — no uploads, no backend, completely private.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Node](https://img.shields.io/badge/node-24-green.svg)
![TypeScript](https://img.shields.io/badge/typescript-5.9-blue.svg)

---

## Features

- Import music folders or individual files (MP3, FLAC, AAC, OGG, WAV, M4A, OPUS, WMA, AIFF)
- Browse library by Songs, Albums, Artists, Genres, Folders, or Playlists
- Full-featured player: shuffle, repeat (none / all / one), seek, volume, queue management
- Mini player bar always visible while music plays; tap to open full-screen player
- Synced scrolling lyrics powered by [LRCLIB](https://lrclib.net)
- Create and manage playlists
- Dark / light / system theme toggle
- Library stats in Settings
- Equalizer via Web Audio API nodes

---

## Tech Stack

| Layer | Technology |
| :--- | :--- |
| Package manager | pnpm workspaces |
| Runtime | Node.js 24 |
| Language | TypeScript 5.9 |
| Frontend framework | React + Vite |
| Routing | Wouter |
| Styling | Tailwind CSS v4 + shadcn/ui |
| State management | React Context (PlayerContext, LibraryContext, ThemeContext) |
| Persistence | IndexedDB via `idb` |
| Metadata parsing | `music-metadata-browser` |
| Lyrics | LRCLIB API (free, no auth) |

> **No backend required** — this is a pure frontend application.

---

## Project Structure

```
Pixel-Player-Web/
├── artifacts/
│   ├── pixel-player/          # Main web app (React + Vite)
│   │   └── src/
│   │       ├── App.tsx                  — router + context providers
│   │       ├── contexts/
│   │       │   ├── PlayerContext.tsx    — audio playback state & controls
│   │       │   ├── LibraryContext.tsx   — music library management (IndexedDB)
│   │       │   └── ThemeContext.tsx     — dark/light/system theme
│   │       ├── pages/
│   │       │   ├── Home.tsx             — home screen with quick actions + recents
│   │       │   ├── Library.tsx          — tabbed library view
│   │       │   ├── Search.tsx           — search across songs/albums/artists
│   │       │   ├── Settings.tsx         — library stats, import, appearance
│   │       │   ├── AlbumDetail.tsx      — album track listing
│   │       │   ├── ArtistDetail.tsx     — artist albums + play all
│   │       │   └── PlaylistDetail.tsx   — playlist management
│   │       ├── components/
│   │       │   ├── layout/Layout.tsx        — main shell (MiniPlayer + BottomNav)
│   │       │   ├── layout/BottomNav.tsx     — Home/Library/Search/Settings tabs
│   │       │   ├── player/MiniPlayer.tsx    — persistent mini player bar
│   │       │   ├── player/FullPlayer.tsx    — full-screen player
│   │       │   ├── player/QueueSheet.tsx    — full-screen queue list
│   │       │   └── player/LyricsPanel.tsx   — synced/plain lyrics
│   │       ├── lib/
│   │       │   ├── db.ts           — IndexedDB helpers
│   │       │   ├── metadata.ts     — audio metadata parsing + color extraction
│   │       │   └── palette.ts      — color palette utilities
│   │       ├── types/music.ts      — Song, Album, Artist, Playlist types
│   │       └── index.css           — dark/light theme (purple accent, charcoal bg)
│   ├── api-server/            # Optional API server (Express + TypeScript)
│   └── mockup-sandbox/        # UI mockup/prototyping sandbox
├── lib/
│   ├── api-client-react/      # Generated React API client hooks
│   ├── api-spec/              # OpenAPI spec + Orval config
│   ├── api-zod/               # Zod schemas generated from OpenAPI
│   └── db/                    # Drizzle ORM schema + migrations
├── scripts/                   # Workspace utility scripts
├── package.json               # Root workspace config
├── pnpm-workspace.yaml        # pnpm workspace definition
├── tsconfig.json              # Root TypeScript config
└── tsconfig.base.json         # Shared TypeScript base config
```

---

## Getting Started

To get a local copy of the project up and running, follow these simple steps:

### Prerequisites

- [Node.js 24+](https://nodejs.org/)
- [pnpm](https://pnpm.io/) — install with `npm install -g pnpm`

### Installation

```bash
# Clone the repository
git clone https://github.com/your-username/Pixel-Player-Web.git
cd Pixel-Player-Web

# Install all workspace dependencies
pnpm install
```

### Development

```bash
# Start the main web app (available at http://localhost:19057)
pnpm --filter @workspace/pixel-player run dev

# Type-check the entire workspace
pnpm run typecheck
```

### Build

```bash
# Build all packages and apps
pnpm run build
```

---

## Architecture Notes

- **Pure frontend, no backend** — music files are loaded via the [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API) (`showDirectoryPicker`) or `<input type="file">` fallback. Audio is played via HTML5 `<audio>` with EQ via Web Audio API nodes.
- **IndexedDB for persistence** — parsed metadata and album art blobs are stored locally so the library survives page reloads. File handles must be re-selected each session due to browser security constraints.
- **Lyrics via LRCLIB** — free, no-auth API at `lrclib.net` for synced (LRC) and plain lyrics.
- **Color extraction** — canvas-based average color from album art is used to tint the full-screen player background.
- **Blob URLs for album art** — cover art is stored as a Blob in IndexedDB and served as a `blob:` URL to avoid re-parsing on each render.

---

## Browser Compatibility

| Browser | File System Access API | Fallback (`<input>`) |
|---|---|---|
| Chrome / Edge | Supported | Supported |
| Firefox | Not supported | Supported |
| Safari | Not supported | Supported |

---

## Known Limitations

- `music-metadata-browser` is deprecated but still functional for browser use. The Node.js replacement (`music-metadata`) does not run in-browser.
- Album art Blob URLs are created fresh each session and must be regenerated from IndexedDB on mount (handled in `getAlbumArt()` in `db.ts`).
- `music-metadata-browser` returns `Buffer` for picture data — must cast through `Uint8Array` before passing to `Blob` constructor to satisfy TypeScript.

---

## Contributing

We welcome contributions! Please see our [CONTRIBUTING.md](CONTRIBUTING.md) for details on how to get involved.

## License

This project is licensed under the [MIT License](LICENSE).
