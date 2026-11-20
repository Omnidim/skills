# Choosing a voice

`listVoices` returns the catalog. Use a voice's `name` field as the `voice_id`
you pass to `createAgent`. Quality varies by language and not every voice
synthesizes cleanly, so always place a short test call and listen before launch.

Filter `listVoices` by `provider` (`cartesia`, `eleven_labs`, `google`,
`sarvam`). ElevenLabs also supports `language`, `accent`, and `gender`.

By provider:

- **`cartesia`** -- low-latency multilingual voices; the platform default.
  Example voice_id
  `bf0a246a-8642-498a-9950-80c35e9276b5` ("Sophie", English female).
- **`eleven_labs`** -- premium, expressive voices; the model is implied by the
  voice. Example voice_id `JBFqnCBsd6RMkjVDRZzb` ("George").
- **`sarvam`** -- natural prosody for Indian languages.
- **`google`** -- broad language coverage.
