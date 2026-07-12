# Codex AI Presence — showcase

A standalone visual showcase for the [Codex AI Presence skill](https://github.com/walkingIssue/Codex-AI-presence): local Kokoro voice output, the waveform-reactive Strand Orb, and the project-local workflow around them.

View the [live showcase](https://walkingissue.github.io/Codex-AI-presence-pages/).

The site is a dependency-free static page built with HTML, CSS, and JavaScript. Its recordings and orb captures document the installation flow, voice configuration, and presence experience across Codex sessions.

## Custom avatar API

The project-local runtime now has an experimental `codex-ai-presence/avatar/v0.1` renderer contract. A custom presence is a self-contained bundle with an `avatar.json` manifest and a relative HTML entry point loaded inside Electron.

```json
{
  "schema": "codex-ai-presence/avatar/v0.1",
  "id": "my-avatar",
  "name": "My Presence",
  "version": "0.1.0",
  "entry": "index.html",
  "capabilities": ["activity", "audio", "move-mode"]
}
```

The entry page consumes the local event bridge:

```js
window.orbApi.onAudioEvent((event) => {
  if (event.type === "activity") setMode(event.state);
  if (event.type === "state") setSpeaking(event.state === "speaking");
  if (event.type === "audio") setVoiceFeatures(event.amplitude, event.bands);
});

window.orbApi.onMoveMode((enabled) => {
  document.body.classList.toggle("move-mode", enabled);
});
```

The host sends activity states (`idle`, `thinking`, `tool`, `skill`, `cli`, `waiting`, `error`), playback lifecycle (`speaking` or `idle`), and normalized audio features (`amplitude`, `rms`, `peak`, and spectral `bands`). The renderer owns the interpretation and can be an SVG, Canvas, WebGL, CSS, Live2D, VRM adapter, or any other visual surface Electron can render from a local entry page. The bridge does not expose Node.js, assistant text, tool names, paths, or raw tool output to the avatar.

The contract is intentionally small and experimental. For installation and configuration details, see the [skill repository](https://github.com/walkingIssue/Codex-AI-presence); the live API shape is also shown in the [Avatar API section](https://walkingissue.github.io/Codex-AI-presence-pages/#avatar-api).
