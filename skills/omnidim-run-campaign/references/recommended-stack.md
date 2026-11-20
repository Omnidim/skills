# Recommended provider stacks

Each agent uses a transcriber (speech-to-text), a voice (text-to-speech), and a
language model, all set on `createAgent`. The pairings below work well in
production, grouped by the language your callers speak. Use `listSTTProviders`,
`listTTSProviders`, `listLLMProviders`, and `listVoices` for the live catalog.

## Language model

- Default: `gpt-4.1-mini` -- fast and accurate across languages, a good first
  choice for almost any agent.
- Alternatives: `gemini-2.5-flash`, `gpt-4o` (premium), `gpt-4.1-nano` (lighter).

## By caller language

- **Indian English (en-IN):** transcriber `soniox`; voice `cartesia`
  (default) or `google`.
- **Hindi / Hinglish (mixed Hindi and English):** transcriber `soniox`
  (handles code-mixed speech in one stream); voice `cartesia`.
- **Other Indian languages (Telugu, Bengali, etc.):** transcriber `soniox` or
  `sarvam` (tuned for Indian languages); voice `cartesia` or `sarvam`.
- **US / UK English:** transcriber `soniox` or `deepgram_stream`; voice
  `cartesia`.

## Field notes

- `transcriber.provider` is one of `deepgram_stream`, `azure_stream`, `soniox`,
  `sarvam`, `cartesia`. `deepgram_stream` also needs a `model` (`nova-3` or
  `nova-2`); the others do not.
- `voice.provider` is one of `cartesia`, `eleven_labs`, `google`, `sarvam`.
  Send only `provider` and `voice_id`: the platform picks the voice model.

## Responsiveness

- Keep the system prompt focused. Long prompts add latency to every turn.
- Verify your chosen voice on a short test call before launch (see
  `voices.md`).
