---
name: transcribe-captions
description: Transcribing audio to generate captions in Remotion
metadata:
  tags: captions, transcribe, whisper, audio, speech-to-text
---

# 音声の文字起こし (Transcribing audio)

Remotion は caption 生成用の audio 文字起こしに複数の組み込みオプションを提供する:

- `@remotion/install-whisper-cpp` — Whisper.cpp でサーバー上ローカル文字起こし。高速・無料だが server インフラが必要。
  <https://remotion.dev/docs/install-whisper-cpp>

- `@remotion/whisper-web` — WebAssembly でブラウザ内文字起こし。server 不要・無料だが WASM overhead で遅い。
  <https://remotion.dev/docs/whisper-web>

- `@remotion/openai-whisper` — OpenAI Whisper API でクラウド文字起こし。高速・server 不要だが有料。
  <https://remotion.dev/docs/openai-whisper/openai-whisper-api-to-captions>
