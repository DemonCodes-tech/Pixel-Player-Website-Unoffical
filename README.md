# PixelPlayer
2
3	A feature-rich web music player that lets users play their local audio files directly in the browser — no uploads, no backend, completely private.
4
5	![License](https://img.shields.io/badge/license-MIT-blue.svg)
6	![Node](https://img.shields.io/badge/node-24-green.svg)
7	![TypeScript](https://img.shields.io/badge/typescript-5.9-blue.svg)
8
9	---
10
11	## Features
12
13	- Import music folders or individual files (MP3, FLAC, AAC, OGG, WAV, M4A, OPUS, WMA, AIFF)
14	- Browse library by Songs, Albums, Artists, Genres, Folders, or Playlists
15	- Full-featured player: shuffle, repeat (none / all / one), seek, volume, queue management
16	- Mini player bar always visible while music plays; tap to open full-screen player
17	- Synced scrolling lyrics powered by [LRCLIB](https://lrclib.net)
18	- Create and manage playlists
19	- Dark / light / system theme toggle
20	- Library stats in Settings
21	- Equalizer via Web Audio API nodes
22
23	---
24
25	## Tech Stack
26
27	| Layer | Technology |
28	|---|---|
29	| Package manager | pnpm workspaces |
30	| Runtime | Node.js 24 |
31	| Language | TypeScript 5.9 |
32	| Frontend framework | React + Vite |
33	| Routing | Wouter |
34	| Styling | Tailwind CSS v4 + shadcn/ui |
35	| State management | React Context (PlayerContext, LibraryContext, ThemeContext) |
36	| Persistence | IndexedDB via `idb` |
37	| Metadata parsing | `music-metadata-browser` |
38	| Lyrics | LRCLIB API (free, no auth) |
39
40	> **No backend required** — this is a pure frontend application.
41
42	---
43
44	## Project Structure
45
46	```
47	Pixel-Player-Web/
48	├── artifacts/
49	│   ├── pixel-player/          # Main web app (React + Vite)
50	│   │   └── src/
51	│   │       ├── App.tsx                  — router + context providers
52	│   │       ├── contexts/
53	│   │       │   ├── PlayerContext.tsx    — audio playback state & controls
54	│   │       │   ├── LibraryContext.tsx   — music library management (IndexedDB)
55	│   │       │   └── ThemeContext.tsx     — dark/light/system theme
56	│   │       ├── pages/
57	│   │       │   ├── Home.tsx             — home screen with quick actions + recents
58	│   │       │   ├── Library.tsx          — tabbed library view
59	│   │       │   ├── Search.tsx           — search across songs/albums/artists
60	│   │       │   ├── Settings.tsx         — library stats, import, appearance
61	│   │       │   ├── AlbumDetail.tsx      — album track listing
62	│   │       │   ├── ArtistDetail.tsx     — artist albums + play all
63	│   │       │   └── PlaylistDetail.tsx   — playlist management
64	│   │       ├── components/
65	│   │       │   ├── layout/Layout.tsx        — main shell (MiniPlayer + BottomNav)
66	│   │       │   ├── layout/BottomNav.tsx     — Home/Library/Search/Settings tabs
67	│   │       │   ├── player/MiniPlayer.tsx    — persistent mini player bar
68	│   │       │   ├── player/FullPlayer.tsx    — full-screen player
69	│   │       │   ├── player/QueueSheet.tsx    — full-screen queue list
70	│   │       │   └── player/LyricsPanel.tsx   — synced/plain lyrics
71	│   │       ├── lib/
72	│   │       │   ├── db.ts           — IndexedDB helpers
73	│   │       │   ├── metadata.ts     — audio metadata parsing + color extraction
74	│   │       │   └── palette.ts      — color palette utilities
75	│   │       ├── types/music.ts      — Song, Album, Artist, Playlist types
76	│   │       └── index.css           — dark/light theme (purple accent, charcoal bg)
77	│   ├── api-server/            # Optional API server (Express + TypeScript)
78	│   └── mockup-sandbox/        # UI mockup/prototyping sandbox
79	├── lib/
80	│   ├── api-client-react/      # Generated React API client hooks
81	│   ├── api-spec/              # OpenAPI spec + Orval config
82	│   ├── api-zod/               # Zod schemas generated from OpenAPI
83	│   └── db/                    # Drizzle ORM schema + migrations
84	├── scripts/                   # Workspace utility scripts
85	├── package.json               # Root workspace config
86	├── pnpm-workspace.yaml        # pnpm workspace definition
87	├── tsconfig.json              # Root TypeScript config
88	├── tsconfig.base.json         # Shared TypeScript base config
89	```
90
91	---
92
93	## Getting Started
94
95	### Prerequisites
96
97	- [Node.js 24+](https://nodejs.org/)
98	- [pnpm](https://pnpm.io/) — install with `npm install -g pnpm`
100
101	### Installation
102
103	```bash
104	# Clone the repository
105	git clone https://github.com/your-username/Pixel-Player-Web.git
106	cd Pixel-Player-Web
107
108	# Install all workspace dependencies
109	pnpm install
110	```
111
112	### Development
113
114	```bash
115	# Start the main web app (available at http://localhost:19057)
116	pnpm --filter @workspace/pixel-player run dev
117
118	# Type-check the entire workspace
119	pnpm run typecheck
120	```
121
122	### Build
123
124	```bash
125	# Build all packages and apps
126	pnpm run build
127	```
128
129	---
130
131	## Architecture Notes
132
133	- **Pure frontend, no backend** — music files are loaded via the [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API) (`showDirectoryPicker`) or `<input type="file">` fallback. Audio is played via HTML5 `<audio>` with EQ via Web Audio API nodes.
134	- **IndexedDB for persistence** — parsed metadata and album art blobs are stored locally so the library survives page reloads. File handles must be re-selected each session due to browser security constraints.
135	- **Lyrics via LRCLIB** — free, no-auth API at `lrclib.net` for synced (LRC) and plain lyrics.
136	- **Color extraction** — canvas-based average color from album art is used to tint the full-screen player background.
137	- **Blob URLs for album art** — cover art is stored as a Blob in IndexedDB and served as a `blob:` URL to avoid re-parsing on each render.
138
139	---
140
141	## Browser Compatibility
142
143	| Browser | File System Access API | Fallback (`<input>`) |
144	|---|---|---|
145	| Chrome / Edge | Supported | Supported |
146	| Firefox | Not supported | Supported |
147	| Safari | Not supported | Supported |
148
149	---
150
151	## Known Limitations
152
153	- `music-metadata-browser` is deprecated but still functional for browser use. The Node.js replacement (`music-metadata`) does not run in-browser.
154	- Album art Blob URLs are created fresh each session and must be regenerated from IndexedDB on mount (handled in `getAlbumArt()` in `db.ts`).
155	- `music-metadata-browser` returns `Buffer` for picture data — must cast through `Uint8Array` before passing to `Blob` constructor to satisfy TypeScript.
156
157	---
158
159	## Contributing
160
161	We welcome contributions! Please see our [CONTRIBUTING.md](CONTRIBUTING.md) for details on how to get involved.
162
163	## License
164
165	This project is licensed under the [MIT License](LICENSE).
166	
