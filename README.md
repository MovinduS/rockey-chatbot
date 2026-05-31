# Rockey Chatbot

Rockey Chatbot is a sci-fi themed web chat experience inspired by Rocky from *Project Hail Mary*. It serves a single-page communication UI and uses Google's Gemini API to respond in character.

This is an unofficial fan project and is not affiliated with Andy Weir, *Project Hail Mary*, Google, or Gemini.

## Features

- Streaming chat responses through `POST /api/chat`
- Optional voice channel through a WebSocket proxy at `/voice`
- Browser text-to-speech playback for responses
- Single-file frontend in `index.html`
- Express server for static hosting and API routes
- Local JSONL chat logs written to `logs/chat.log`

## Tech Stack

- Node.js 18+
- Express
- Server-sent events for streaming chat
- WebSocket proxy for Gemini Live voice responses
- `@google/generative-ai`
- Browser Web Speech API

## Project Structure

```text
.
|-- index.html          # Frontend UI, chat logic, and speech playback
|-- server.js           # Express server, Gemini chat endpoint, and voice proxy
|-- package.json        # npm scripts and dependencies
|-- package-lock.json   # Locked dependency versions
|-- .env.example        # Example environment variables
`-- README.md           # Setup and deployment guide
```

Local-only files such as `.env`, `logs/`, `node_modules/`, and `VPS_DEPLOY_GUIDE.md` are intentionally ignored.

## Requirements

- Node.js 18 or newer
- npm
- A Gemini API key from Google AI Studio

## Quick Start

Install dependencies:

```bash
npm install
```

Create your local environment file:

```bash
cp .env.example .env
```

Add your Gemini API key:

```env
GEMINI_API_KEY=your_gemini_api_key_here
GEMINI_MODEL=gemini-2.0-flash
PORT=3000
```

Start the app:

```bash
npm start
```

Open the app:

```text
http://localhost:3000
```

For development with automatic server reloads:

```bash
npm run dev
```

## Environment Variables

| Variable | Required | Default | Description |
|---|---:|---|---|
| `GEMINI_API_KEY` | Yes | None | Gemini API key used by the server. Never expose this in frontend code. |
| `GEMINI_MODEL` | No | `gemini-2.0-flash` | Gemini model used for text chat. |
| `PORT` | No | `3000` | HTTP server port. Most hosts set this automatically. |

## Deploying

This app can run on any Node-friendly host such as Render, Railway, Fly.io, a VPS, or a container platform.

General deployment steps:

1. Fork or clone this repository.
2. Install dependencies with `npm install`.
3. Set `GEMINI_API_KEY` in the host's environment variable settings.
4. Set `GEMINI_MODEL` only if you want to override the default.
5. Use `npm start` as the start command.
6. Let the platform provide `PORT`, or set `PORT=3000` for a VPS/manual deployment.

Important deployment notes:

- Do not commit `.env` or real API keys.
- Keep the Gemini API key on the server only.
- Make sure WebSockets are enabled if you want the voice tab to work.
- For VPS deployments, run the Node process behind a reverse proxy such as nginx or Caddy and enable HTTPS.
- The app writes chat logs to `logs/chat.log`; decide whether your host should persist, rotate, or disable those logs.

### Render Example

- Build command: `npm install`
- Start command: `npm start`
- Environment variables:
  - `GEMINI_API_KEY`
  - Optional: `GEMINI_MODEL`

### Railway Example

- Connect the GitHub repository.
- Add `GEMINI_API_KEY` in Variables.
- Railway will detect Node.js and run `npm start`.

### VPS Example

```bash
git clone https://github.com/your-username/rockey-chatbot.git
cd rockey-chatbot
npm install
cp .env.example .env
nano .env
npm start
```

For production, use a process manager such as `pm2` and put nginx or Caddy in front of the app.

## API

### `POST /api/chat`

Streams a text response as server-sent events.

Request body:

```json
{
  "messages": [
    { "role": "user", "content": "Hello Rocky!" }
  ]
}
```

Response stream:

```text
data: {"token":"Amaze"}
data: {"token":"!"}
data: {"done":true,"full":"Amaze!"}
```

### `WS /voice`

Proxies browser audio messages to Gemini Live. This route requires `GEMINI_API_KEY` and a deployment host that supports WebSockets.

## Security Checklist Before Publishing

- Confirm `.env` is ignored and never committed.
- Rotate any API key that was ever committed or shared.
- Keep `node_modules/` out of git; dependencies are restored from `package-lock.json`.
- Keep local VPS notes and private deployment commands out of git.
- Review `logs/chat.log` before sharing archives or deployment snapshots.

## Troubleshooting

- `GEMINI_API_KEY not set in .env`: create `.env` locally or set the environment variable in your host.
- `HTTP 500` from chat: check server logs for Gemini API errors.
- Voice tab does not connect: verify the host supports WebSockets and `GEMINI_API_KEY` is available.
- Port already in use locally: set another port, for example `PORT=4000 npm start`.
