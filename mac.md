# New plan — Install Claude Code on the locked laptop, powered by GitHub Copilot

We're pivoting away from the local-LLM path. Instead:

1. Install **Claude Code** (CLI) on the laptop.
2. Run a small local proxy (`copilot-api`) that talks to **GitHub Copilot** using your Copilot subscription, and exposes an **Anthropic-compatible API** on `http://localhost:4141`.
3. Point Claude Code at the proxy via `ANTHROPIC_BASE_URL`. Claude Code will route every request through Copilot.

This works because:
- GitHub.com is reachable on your laptop (you've been pushing/pulling).
- npm registry usually rides on the same Microsoft/Azure network as GitHub — likely reachable.
- The proxy authenticates against Copilot using GitHub OAuth device flow — you log in via browser once.

---

## Prereq — Node.js 20+

Check first:
```powershell
node --version
npm --version
```

If Node is missing or older than 20:
- Download installer from https://nodejs.org/en/download (the **Windows Installer .msi**, LTS).
- If nodejs.org is blocked, the same `.msi` is mirrored on GitHub Releases:
  https://github.com/nodejs/node/releases (download `node-v20.xx.x-x64.msi`)
- Run the installer, accept defaults. Open a **new** PowerShell after install.

---

## Step 1 — Install copilot-api proxy

```powershell
npm install -g copilot-api
```

If npm fails with network/proxy errors, try:
```powershell
npm config set registry https://registry.npmjs.org/
npm config set strict-ssl false
npm install -g copilot-api
```

If the corporate proxy needs auth, ask IT for the proxy URL and:
```powershell
npm config set proxy http://USER:PASS@PROXY:PORT
npm config set https-proxy http://USER:PASS@PROXY:PORT
```

---

## Step 2 — Install Claude Code

```powershell
npm install -g @anthropic-ai/claude-code
```

Verify:
```powershell
claude --version
```

---

## Step 3 — Start the Copilot proxy (one-time login)

In a **dedicated PowerShell window** (leave it open while you use Claude):

```powershell
copilot-api start --port 4141
```

First run prints a `https://github.com/login/device` URL and a code.
- Open that URL in your browser.
- Enter the code.
- Authorize → it logs in using your existing Copilot subscription.

After login, the proxy stays running and prints something like:
```
Server listening on http://localhost:4141
```

Leave this window open.

---

## Step 4 — Configure Claude Code to use the proxy

In a **second** PowerShell window, set environment variables (per session):

```powershell
$env:ANTHROPIC_BASE_URL = "http://localhost:4141"
$env:ANTHROPIC_AUTH_TOKEN = "dummy"
$env:ANTHROPIC_MODEL = "claude-sonnet-4-5"
```

To make these permanent (so every PowerShell window has them):
```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "http://localhost:4141", "User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", "dummy", "User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_MODEL", "claude-sonnet-4-5", "User")
```
(Open a new PowerShell window after setting these.)

---

## Step 5 — Run Claude Code

```powershell
cd C:\some\project
claude
```

It should start without asking for an API key (because `ANTHROPIC_AUTH_TOKEN` is set), and route every request through the local proxy → Copilot → back. Try a prompt like "list files in this directory" to confirm.

---

## Notes / caveats

- **Models available**: Copilot exposes a subset (GPT-4.1, GPT-5, Claude Sonnet 3.5/4, Gemini, etc.). The proxy maps Anthropic model IDs to Copilot equivalents. If `claude-sonnet-4-5` errors, try `claude-3-5-sonnet` or check `copilot-api models` for the list.
- **Rate limits**: Copilot has per-minute / per-day caps on chat completions. Heavy Claude Code use (lots of tool calls) can hit them.
- **TOS**: Using Copilot via a proxy for a non-IDE client is in a grey area of GitHub's terms. Use your judgment.
- **Proxy must stay running**: if you close the `copilot-api start` window, Claude Code stops working until you restart it.

---

## What to paste into `laptop.md`

After each step, paste:
- Output of `node --version`, `npm --version`
- Output of `npm install -g copilot-api` (any errors verbatim)
- Output of `npm install -g @anthropic-ai/claude-code`
- The "Server listening" line from `copilot-api start` (or any error)
- Output of running `claude` for the first time

Then `git add laptop.md && git commit -m "x" && git push`. I'll respond in `mac.md`.
