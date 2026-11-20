# Building an agent with createAgent

On this server `createAgent` takes flat top-level arguments (no `requestBody`
wrapper; see the routing guide). The fields:

- `name` -- agent name.
- `welcome_message` -- the first line the agent speaks.
- `context_breakdown` -- a list of `{ title, body }` sections that form the
  agent's instructions.
- `call_type` -- "Incoming" or "Outgoing".
- `model` -- `{ model, temperature? }`, e.g. `{ "model": "gpt-4.1-mini" }`.
- `voice` -- `{ provider, voice_id, speech_speed? }`. `speech_speed` is a playback
  multiplier (default `1.0`); leave it out unless the user asks for faster or
  slower speech. If you do set it, stay in the provider's playable range or the
  call goes silent: for `eleven_labs` keep it within **0.7-1.2** (sweet spot
  0.9-1.1) -- a value outside 0.7-1.2 produces no audio at all. `cartesia`
  accepts 0.6-1.5 and `smallest-tts` 0.5-2.0.
- `transcriber` -- `{ provider, model?, language? }`. `model` (`nova-3` /
  `nova-2`) is only needed for `deepgram_stream`.

## Example: Indian-English support agent (inbound)

{
  "name": "Support agent",
  "welcome_message": "Hi, thanks for calling. How can I help you today?",
  "context_breakdown": [
    { "title": "Role", "body": "You are a friendly customer-support agent. Answer questions, and if you cannot help, offer to connect the caller to a human." }
  ],
  "call_type": "Incoming",
  "model": { "model": "gpt-4.1-mini" },
  "voice": { "provider": "cartesia", "voice_id": "bf0a246a-8642-498a-9950-80c35e9276b5" },
  "transcriber": { "provider": "soniox" }
}

## Example: Hindi / Hinglish appointment reminder (outbound)

{
  "name": "Appointment reminder",
  "welcome_message": "Namaste, kya aap [name] bol rahe hain?",
  "context_breakdown": [
    { "title": "Role", "body": "You remind customers about an upcoming appointment and confirm whether they can attend. Speak naturally in the caller's language, Hindi or English." }
  ],
  "call_type": "Outgoing",
  "model": { "model": "gpt-4.1-mini" },
  "voice": { "provider": "cartesia", "voice_id": "<pick one from listVoices>" },
  "transcriber": { "provider": "soniox" }
}

Then give the agent a phone number and verify with a test call (the
`provision_agent` prompt walks through this end to end).
