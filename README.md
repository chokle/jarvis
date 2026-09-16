# J.A.R.V.I.S.

> Built on [adewaskar/jarvis](https://github.com/adewaskar/jarvis) by Aditya Dewaskar (MIT). This fork keeps the original interface idea and adds local bridge hardening, Windows helpers, and a more practical local setup.

JARVIS is a browser-based voice assistant with an Iron Man-style HUD. The frontend is a React/Vite/Three.js app; the local bridge is a Node process that connects the UI to your local agent tools and MCP servers.

This README has been rewritten for the current Codex-maintained version of the repository. The app itself still uses **Claude Code as the local agent runtime** through the bridge.

## What it does

- Wake on **“Hey Jarvis”**
- Speak and listen through browser speech by default
- Upgrade to **ElevenLabs** automatically when a key is available
- Drive MCP tools available from your local agent configuration
- Render HUD panels, images, and media inside the interface
- Run on Windows with included launcher scripts

## Requirements

- **Node.js 20+**
- **Chrome or Edge** in a real browser window
- **Claude Code installed and logged in**
- Optional: **ElevenLabs API key** for better voice + transcription

Install Claude Code and log in once before using bridge mode:

```bash
npm install -g @anthropic-ai/claude-code
claude
```

## Quick start

```bash
npm install
npm run setup
npm start
```

Then open the printed URL in **Chrome or Edge**, click **INITIALISE**, allow microphone access, and say **“Hey Jarvis”**.

### Split mode

Run the two sides separately if you prefer:

```bash
npm run bridge
npm run dev
```

### Writable mode

Effectful tools are disabled by default. To allow actions such as browser control or other write-capable tools:

- **Split mode:** run `npm run bridge:writes` instead of `npm run bridge`, and keep `npm run dev` in the second terminal
- **One-command mode:** start everything with:

```bash
npm start -- --writes
```

## Windows launchers

Files in the repository root:

| File | Purpose |
| --- | --- |
| `start-jarvis.cmd` | Starts the bridge and frontend in a visible Chrome window |
| `start-jarvis.vbs` | Starts JARVIS with the window hidden/off-screen |
| `show-jarvis.vbs` | Brings the hidden window back |
| `stop-jarvis.vbs` | Stops only the processes started by JARVIS |

For hidden Windows mode, the project uses a dedicated Chrome profile with microphone access pre-approved for localhost.

## How it works

JARVIS has two parts:

1. **Frontend** — voice input/output, wake handling, HUD, WebGL visuals
2. **Bridge** — local Node server at `bridge/server.mjs`

The bridge:

- listens on `127.0.0.1`
- exposes a local WebSocket/HTTP interface on port `8787` by default
- uses `@anthropic-ai/claude-agent-sdk`
- reads MCP servers from `~/.claude.json`
- gates write-capable tools behind `JARVIS_ALLOW_WRITES=1`

## Voice and speech

- **Default**: browser speech recognition + browser speech synthesis
- **With ElevenLabs key**: ElevenLabs voice + Scribe transcription
- **Wake word**: browser speech by default, or Porcupine when `VITE_PICOVOICE_ACCESS_KEY` is set in `.env.local`

The app detects available capabilities automatically at startup.

## Configuration

Frontend settings go in `.env.local` (copy from `.env.example`). Bridge settings come from environment variables.

### Bridge variables

| Variable | Default |
| --- | --- |
| `JARVIS_BRIDGE_PORT` | `8787` |
| `JARVIS_MODEL` | Optional model override |
| `JARVIS_EFFORT` | Optional effort override |
| `JARVIS_ALLOW_WRITES` | off |
| `JARVIS_ALLOWED_ORIGINS` | local dev origins |
| `JARVIS_ALLOW_NO_ORIGIN` | off |
| `JARVIS_FILE_ROOTS` | unset |
| `JARVIS_VOICE_ID` | Optional ElevenLabs voice override |
| `ELEVENLABS_API_KEY` | unset |

### Frontend variables

| Variable | Purpose |
| --- | --- |
| `VITE_BACKEND` | `bridge` or `direct` |
| `VITE_BRIDGE_URL` | Bridge WebSocket URL |
| `VITE_TTS_ENGINE` | `system` or `kokoro` |
| `VITE_KOKORO_VOICE` | Kokoro voice id |
| `VITE_USE_ELEVENLABS` | Force ElevenLabs TTS on |
| `VITE_PICOVOICE_ACCESS_KEY` | Enables Porcupine wake-word mode |
| `VITE_ANTHROPIC_API_KEY` | Required only for direct mode |

## Safety model

The project is intentionally conservative by default:

- the bridge only binds to loopback
- local browser origins are checked explicitly
- write-capable tools are denied unless enabled
- model-authored HUD HTML is sanitized before rendering
- remote media is proxied through guarded bridge endpoints

## Troubleshooting

**No microphone or voice**

- Use Chrome or Edge
- Open the app in a real browser window, not an embedded preview
- Recheck microphone permissions

**Bridge unreachable**

- Make sure `npm run bridge` or `npm start` is still running
- Check whether port `8787` is already in use

**Need environment help**

```bash
npm run setup
```

## Development scripts

```bash
npm run dev
npm run bridge
npm run bridge:writes
npm run build
npm run lint
```

## Credits and license

MIT.

Original project: [adewaskar/jarvis](https://github.com/adewaskar/jarvis) by [Aditya Dewaskar](https://github.com/adewaskar).

Music credits remain in [`public/audio/CREDITS.md`](public/audio/CREDITS.md).
