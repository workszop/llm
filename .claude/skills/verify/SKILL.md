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
- **Stale saved model**: `localStorage.setItem('gemini_model','<removed-model>')` +
  reload → select must fall back to the default option, not go empty.

## Gotchas

- The extension's DLP may redact raw `#output` text in tool results — read booleans
  (`textContent.includes(...)`) instead of dumping page text.
- Copy-button label reverts after 1.6 s; read it within ~1 s of the click.
