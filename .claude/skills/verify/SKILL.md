---
name: verify
description: How to run and verify the Gemini parameter playground (single-file index.html, no build)
---

# Verifying the llm playground

Single static file, no build step, no tests. Surface = browser GUI.

## Launch

```bash
cd /home/andrzey/git-claude/llm && python3 -m http.server 8642 --bind 127.0.0.1
```

Open `http://127.0.0.1:8642/index.html` (serve over localhost, not file://, so
`navigator.clipboard` works). Drive with claude-in-chrome; if `Page.captureScreenshot`
times out (minimized window), ref-based clicks + `javascript_tool` DOM readbacks still work.
If the extension is not connected, fall back to headless Chrome over CDP
(`google-chrome --headless=new --no-sandbox --user-data-dir=<scratch>/prof --remote-debugging-port=9333`,
then a Node 22 script: `PUT /json/new`, `Page.navigate`, `Runtime.evaluate`). Disable the cache
(`Network.setCacheDisabled`) and never redeclare `const` across separate `Runtime.evaluate` calls —
the second one throws and the probe reads stale state.

## Flows worth driving

- **Request preview**: toggle a param switch (`.param[data-key=...] .switch input`),
  read `#reqBody` textContent — only enabled params must appear.
- **Send without API key** → error box "Brak klucza API" in `#output`. Ctrl+Enter also sends.
- **No live API key available**: stub `window.fetch` in the page and click the real
  Send button (`#sendBtn`). Non-stream: return a JSON `Response`. Stream: enable
  `#streamToggle`, return a `Response` whose body is `data: {...}\n\n` SSE lines
  (supports multi-candidate via `index`, mid-stream `{error:{...}}` chunks,
  `promptFeedback.blockReason`).
- **HTML-injection regression**: model text containing `<img onerror=...>` must render
  as literal text in `#output` (no elements created, no handler fired). Same for
  thoughts and error details.
- **Model discovery (`#modelRefresh`)**: no key → `#modelStatus` says "Wklej klucz API" with
  `data-state="error"`. With a key, stub `window.fetch` to return `models.list` JSON
  (`{models:[{name:'models/gemini-4-flash', supportedGenerationMethods:['generateContent']}], nextPageToken}`;
  a second page when `pageToken=` is in the URL). The select is REPLACED by the 3 newest
  text Flash models (`gemini-<n>*flash*` with `generateContent`; image/live/tts/audio variants
  and Pro/embedding models excluded; version desc, base before -lite/-preview). The 3 ids land in
  `localStorage.gemini_models_flash` and survive reload; a selected model that drops out of the
  top 3 falls back to the newest one and the request preview URL follows. Second click → "Lista jest
  aktualna". A 4xx body `{error:{...}}` → status `data-state="error"`, list untouched, button re-enables.
- **Stale saved model**: `localStorage.setItem('gemini_model','<removed-model>')` +
  reload → select must fall back to the default option, not go empty.

## Gotchas

- The extension's DLP may redact raw `#output` text in tool results — read booleans
  (`textContent.includes(...)`) instead of dumping page text.
- Copy-button label reverts after 1.6 s; read it within ~1 s of the click.
