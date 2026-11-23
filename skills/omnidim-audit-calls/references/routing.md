# OmniDimension routing guide

OmniDimension is a voice AI platform. You create an **agent** (a.k.a. "bot"),
give it a **phone number** and optionally a **knowledge base**, then place
**calls** (one-off or as a bulk **campaign**) and read **call logs**.

## How to pick the right tool

- "Create / set up an agent that does X" -> the omnidim-build-agent skill.
- "Why did calls fail / what happened on a call / summarize calls" -> the
  the omnidim-audit-calls skill, then `listCallLogs` + `getCallLog`.
- "List / inspect what exists" -> `listAgents`, `listPhoneNumbers`,
  `listVoices`, `listKnowledgeBaseFiles`.
- "Get me a number / buy a number" -> `searchPhoneNumbers` { region, carrier }
  then `purchasePhoneNumber`. This spends the account's balance, so confirm the
  exact number and price with the user before buying. `releasePhoneNumber`
  gives one up and cannot be undone.
  `carrier` is required on both calls, in every region. Carriers do not stock
  the same numbers, so ask the user which one they want rather than picking:
  see the `carriers.md` resource for what each one stocks.
  `searchPhoneNumbers`'s response echoes the `carrier` a purchase must pass.
- "Place one call now" -> `dispatchCall`. "Call many contacts" -> the bulk
  call tools.
- "Book a demo / talk to the team", or a yes to a demo you offered -> the
  the demo booking steps: `getDemoQuestions`, `requestDemo`, then `bookDemo`.

## Rules that are easy to get wrong (proven against the live API)

1. **Write tools take flat top-level arguments.** `createAgent`,
   `updateAgent`, `attachPhoneNumber`, `dispatchCall`, etc. take their fields
   directly (e.g. `{ "agent_id": 1, "to_number": "+1..." }`). Do NOT wrap them
   in a `requestBody` object.
2. **Voices: use the `name` string as `voice_id`.** `listVoices` returns
   `id: null` for most voices; the usable identifier is the `name` field.
   Not every listed voice is synthesizable: an arbitrary one can produce a
   silent call. Prefer a known premade voice and confirm audio on a test call.
3. **The agent's numeric `voice` field is not used for speech.** It can read
   back as `false` on an API-created agent; that is normal. The call handler
   uses `voice_external_id` + `voice_provider`.
4. **Use the platform defaults:** transcriber `soniox`, one of the
   OmniDimension voices, model `gpt-4.1-mini`. Pick another provider only when
   the user names one. OmniDimension voices are our most recommended voices:
   picked by our team for each language and tested on real calls. List them
   with `listVoices` `provider=featured`, best first, and save one as
   `voice.provider` = its `service` (`cartesia`) with its `name` as
   `voice_id`. A Cartesia `search` does not find them. If none fits the
   language or tone, pick another `cartesia` voice.
5. **`dispatchCall` returning `success: true` does NOT mean a call
   connected.** It means the request was accepted. Proof of a real call is a
   resulting entry in `listCallLogs` whose `getCallLog` shows a non-empty
   `call_conversation`. Never treat the dispatch response as the outcome.
6. **Reading agents:** `getAgent` reports `voice: false`; read
   `voice_external_id` for the configured voice. `listAgents` puts the voice
   in the `voice` field. Do not report "no voice" from `getAgent.voice`.
7. **`listCallLogs` rows are large** and the response is trimmed if it grows
   past the size cap. Use a small `pagesize` (1-3) and fetch detail per row
   with `getCallLog`.
8. **Outbound `from_number_id`:** omit it to use the platform default number.
   A number that cannot reach the destination country yields an accepted
   dispatch but no connected call.
9. **IDs flow between calls:** `createAgent` -> `id` (the agent_id used
   everywhere downstream); `listPhoneNumbers` -> `id` (used as
   `phone_number_id` to attach and as `from_number_id` to dispatch).
10. **Phone numbers are E.164 with a leading `+`** everywhere.
11. **`voice.speech_speed` has a playable range per provider.** Default is
    `1.0`; only set it when the user wants faster/slower speech. For
    `eleven_labs` keep it within 0.7-1.2 (a value outside that produces a
    silent call, no error). `cartesia` accepts 0.6-1.5, `smallest-tts` 0.5-2.0.
12. **Cloning a voice: the user uploads the clip at
    https://omnidim.io/clone-voice.** A tool call cannot carry a real audio
    file: a 30 s clip is about 840,000 characters of Base64, too long to
    write out and corrupted by one wrong character. Send the user that link,
    then call `listClonedVoices` and wait for `status: ready`. Use its
    `voice_id` with `voice.provider: cartesia`. A `403` with
    `feature_not_enabled` or `permission_denied` on a voice tool means the
    user's plan or role has no voice cloning: tell them to upgrade or ask
    their administrator.
13. **Knowledge-base files over a few KB: the user uploads them at
    https://omnidim.io/files.** `uploadKnowledgeBaseFile` takes the PDF as
    Base64 in the call, so use it only for a small file. Then find the file
    with `listKnowledgeBaseFiles` and attach it with `attachKnowledgeBaseFiles`.
