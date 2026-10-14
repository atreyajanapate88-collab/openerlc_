# OpenERLC

ER:LC-only AI chatbot. Answers from the ER:LC Fandom wiki (live lookups) and shows live server stats through the ER:LC MCP server. The browser UI is in `index.html`; the backend, MCP server, and diagnostic tool are JavaScript (Node.js).

## Run locally
```
copy .env.example .env
npm install
set GROQ_API_KEY=...                # Windows Command Prompt; or use .env / env.txt
set ERLC_SERVER_KEY=...             # in-game: Settings > API key (omit to disable live stats)
npm start
```
Open http://localhost:8000

Requires Node.js 22 or newer. The app reads `.env` and then fills any missing values from
`env.txt` in the project folder. Start with `.env.example`. Set
`OPENERLC_MODEL=openai/gpt-oss-120b` there to select the Groq model. Run `npm run check`
to test the Groq key, wiki, optional ER:LC API key, and MCP server.

## Deploy
Deploy the complete app to Render using `render.yaml`, or use the Dockerfile
on Railway, Fly.io, or another Node.js host. The Node server delivers `index.html`
and the API together. Set `GROQ_API_KEY` and,
optionally, `ERLC_SERVER_KEY` as host environment variables. For Render, enable live
stats by setting `ERLC_SERVER_KEY` and `OPENERLC_LIVE_ENABLED=true`. Never put API
keys in `config.js` or other browser-delivered files.

For Vercel, deploy the project root with `vercel.json` and the `api/` directory
included, and set `GROQ_API_KEY` in the project environment variables. The browser
uses same-origin `/api` routes by default. After deployment, verify
`https://<your-deployment>/api/health` returns JSON with `"ok": true`.

If serving the UI from a separate web host, set
`window.OPENERLC_API_BASE` in `config.js` to the backend URL (no trailing slash).
Verify `https://<your-service>/api/health` returns JSON with `"ok": true`.

## Notes
- Live tools are read-only; `run_command` is force-disabled for the chatbot.
- Public visitors can see live player data, so set `OPENERLC_LIVE_ENABLED=false` if that's not OK.
- Each visitor message costs AI tokens; a per-IP limit is built in (`OPENERLC_RATE_PER_MIN`). Add authentication before sharing widely.
- Live calls are cached for 10 seconds so many users don't exhaust the ER:LC API rate limit.
