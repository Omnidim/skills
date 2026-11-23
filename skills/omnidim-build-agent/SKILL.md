---
name: omnidim-build-agent
description: "Design, build, test and change an OmniDimension voice agent: ask about the gaps and get the call script approved, build the full prompt and settings, give it a number, test it with a real call, apply fixes safely, and roll back if a change broke it. Use when the user wants a new voice agent, a bot for calls, to fix or change an agent, or to undo agent changes."
---

Needs the OmniDimension MCP server: hosted at `https://mcp.omnidim.io/mcp`, or local with `npx -y @omnidim-ai/mcp-server` (see the omnidim-connect skill). If a field, error or feature is unclear, call `searchDocs` before guessing.

## Design a new voice agent's calls before building it: ask about the gaps, stress-test the plan, then write the call script for the user to approve.

Design the calls for a new OmniDimension voice agent, before anything is built:

"<what the user wants the agent to do>"

Work in three phases, in order. This playbook builds nothing.

Phase 1: understand and ask
1. Read everything the user gave: requirements, documents, old prompts, notes, call goals. Pull out who the agent is and who it speaks for, who it talks to, the call direction, the goal and what counts as success, the languages, the voice gender and tone, the facts it may use and the things it must never say, numbers and limits, the flow, handoff or callback, the data to capture, when the call should end, and every gap or contradiction.
2. Open with an "Understanding" block of 5 lines or fewer: who, what, goal, success, and the biggest gap.
3. Ask about the gaps in one message. Number every question, give one line on why it matters, a suggested answer, and lettered options where they help. Ask only about real gaps, never about something the material already answers. Tell the user they can reply "default" to accept every suggestion. Ask one question at a time only if the user asks for that. Log a skipped question as an assumption.

Phase 2: stress-test the plan
4. In one more message, check the plan from each angle that applies, each finding with a suggested fix: contradictions (quote both parts); the other person's objections, pushback, hostile or trick questions; edge cases (wrong person, busy, silence, unclear audio, "are you a bot?", asks for a human, off-topic, a stop request, a language switch); partial outcomes (maybe, later, a counteroffer, a vague yes); loops, each with a cap; invented claims, over-promising and compliance risk; math errors; lines that sound robotic when spoken.
5. Then give a confirmation summary: the goal and the outcomes, hard rules and limits, the assumptions, and the fixes you will make to the source. Ask "Confirm, or tell me what to change." Write no script until the user confirms.

Phase 3: the call design, for the user to proofread
6. Write it in this structure:
   - Basics, from the confirmed summary, so the build step has them all: the agent's name, role, company and who it speaks for; voice gender and tone; languages and the terms that stay in English; AI disclosure; the forms of address allowed; the goal, what counts as success, and any fallback; hard rules and limits; banned phrases; the facts the agent may use; the never-say and never-promise list; the call direction; when the call may end; the transfer number and when to transfer, if any; the voicemail line, for outbound calls; the two lines the agent says when the caller is silent; where the results go after each call; and the number plan (an existing number, or buy one: the region, and landline or mobile).
   - Welcome message. If the user gave a welcome message, use it word for word. Otherwise, for an outbound call, write a name check of 8 words or fewer, such as "Hi, am I speaking with [name]?", and say who is calling and why on the next turn. For an inbound call, greet, say who they reached, and ask how you can help. Mark a message you wrote [DRAFT].
   - Intents, grouped: Opening, FAQ, Objections, Escalation or final ask, Counteroffers and partial answers, Agreement and close, Special situations, Language switch. Drop a group that does not apply, and add one the use case needs. Number every intent as **N. `intent_name`**, with Trigger (3 to 5 realistic phrases in the caller's language mix), **Bot:** (the exact spoken line) and Next (where the call goes, and the outcome if the call ends).
   - Script rules for every bot line: 1 to 3 sentences written to be spoken; everyday words in the caller's language mix; numbers spelled out as words, never digits or symbols; verb forms that match the voice's gender; only facts from the user, with anything uncertain marked [VERIFY]; no fact repeated across intents; at most one question per turn; dynamic values in [square_brackets].
   - Then: the state variables (a table), the outcomes (a table), a pronunciation guide, the changes from the source (one line each, with the reason), and the open items (everything marked [VERIFY] or [DRAFT]).
7. End with "Proofread the intents. Tell me which number to change, add or remove." After that, change only what the user names.
8. When the user approves, build it with the build steps of the omnidim-build-agent skill, using this design as the approved design.

Throughout: be direct, and never invent facts, numbers, results or policies. If the material holds a script for a different bot, take its requirements, not its lines. If the user adds information midway, say in one line what changed, then continue.

## Build a voice agent from its approved design: the full prompt and settings, a number, results after each call, and a test call to prove it speaks.

Build a production-ready OmniDimension voice agent for this purpose:

"<what the user wants the agent to do>"

The source of truth is the call design the user approved with the design steps of the omnidim-build-agent skill. If there is no approved design yet, run the design steps of the omnidim-build-agent skill first. Build without one only if the user says to skip it, and then list your assumptions. The user's other material (documents, brochures, notes) only fills in facts the design already refers to, such as an exact spelling, an address, or a number the design names. It never changes behaviour, tone, flow, limits or approved lines. If the agent needs something neither gives, write [MISSING: what] and report it.

Follow these steps in order. Write tools take flat top-level arguments (no `requestBody` wrapper).

1. Conflict check. Show a table: conflict or gap | the design says | the material says | resolution. If there are none, write "No conflicts found." If a conflict changes the goal, a limit or price, the agent's identity, AI disclosure, legal claims or compliance, stop after the table and ask the user. Otherwise continue.
2. Write the prompt in the eight sections of the `references/agent-prompt.md` resource, one `context_breakdown` entry per section. Copy approved lines (welcome, objection replies, closings, voicemail) word for word. Put no fact marked [VERIFY] in the prompt: list each one as open. Before you write it, read the `references/call-tips.md` resource: it lists what the best agents do on real calls.
3. Welcome message: use the design's approved welcome word for word. Only without a design: If the user gave a welcome message, use it word for word. Otherwise, for an outbound call, write a name check of 8 words or fewer, such as "Hi, am I speaking with [name]?", and say who is calling and why on the next turn. For an inbound call, greet, say who they reached, and ask how you can help.
4. Voice and models: Start with the OmniDimension voices, our most recommended: call `listVoices` with `provider=featured` and pick one whose `featured_languages` include the primary language and whose `gender` matches the agent's voice gender. Use its `name` as voice_id and its `service` as the voice provider. If no voice has those fields, show the user a few names with their `sample_url` and let them pick. Model `gpt-4.1-mini` at temperature 0.3. Transcriber `soniox`, or the one the `references/recommended-stack.md` resource gives for the caller's language. List every language the agent speaks in `languages`.
5. Create the agent with `createAgent`, setting only what the design uses:
   {
     "name": "<short name>",
     "welcome_message": "<from step 3>",
     "context_breakdown": [ { "title": "1. Identity and objective", "body": "..." }, "... one per section" ],
     "call_type": "Outgoing",
     "languages": ["<primary language>"],
     "model": { "model": "gpt-4.1-mini", "temperature": 0.3 },
     "voice": { "provider": "<its service>", "voice_id": "<name from listVoices provider=featured>" },
     "transcriber": { "provider": "soniox", "should_apply_noise_reduction": true, "interruption_min_words": 1, "first_ideal_message": "<silence nudge>", "is_first_ideal_message_dynamic": false, "second_ideal_message": "<second nudge>", "is_second_ideal_message_dynamic": false },
     "end_call": { "enabled": true, "condition": "<when the call may end>", "message_type": "prompt", "message_prompt": "<a closing line for each outcome>" },
     "voicemail": { "enabled": true, "message": "<the voicemail line>" }
   }
   - `end_call.condition` lists when the call may end, and what is NOT a reason to end: a first no, "I'll think about it", "send me details", one-word replies. A stop request always ends the call. The closing lines and the silence nudges use the primary language and the voice's verb gender. The two `is_..._dynamic: false` flags make the agent say the nudges as written.
   - `languages` takes display names exactly as the dashboard's language picker shows them, such as "English (India)" or "Hindi". An unknown name is dropped without an error (all unknown is an error), and the order is not kept, so check the saved list in step 7.
   - Inbound: `call_type` "Incoming", and no `voicemail`. `voicemail` is for outbound calls only, and is an access-gated feature: ask the user whether voicemail is enabled for their account. If they do not know, leave `voicemail` out.
   - `transfer` only if the design has a handoff, with the same condition the prompt uses.
   Capture the returned `id` as agent_id. (`status` is always "Completed"; it is not a build signal.)
6. Self-check, and fix silently before you go on: every design decision is in the prompt; every branch has an instruction; approved lines are word for word; every fact traces to the design or the material; no rule appears twice and no two rules contradict; every example follows the script rules; the end-call condition matches the prompt's ending list; the welcome, voicemail, nudges and closing lines match the identity and the verb gender; the AI disclosure rule and the identity do not contradict; the result variables cover every outcome. Fix anything you find with `updateAgent`, and send the full `context_breakdown` list: it replaces every section with the list you send.
7. Fetch it back with `getAgent` and check every setting saved, reading the names the `references/agent-config.md` resource maps (`getAgent` returns them flat: `voice_external_id`, `languages` labels, `asr_service`, `llm_temperature`, `is_end_call_enabled`, `end_call_message_type`, `voicemail_enabled`, `first_ideal_message`). Fix or report each mismatch.
8. Results after each call: `addPostCallAction` { agent_id, type: "omni_crm" (or where the design sends them; ask the user if unsure), include: ["summary", "sentiment", "fullConversation", "extracted_variables"], extracted_variables: [ { key, prompt } ] }. Variables: call_status (the design's outcomes), goal_confirmed (yes only on explicit agreement), callback_time, hot_lead, objection_raised (the design's objection list), not_interested_reason, do_not_call, language_used, asked_if_ai, and one per question the agent asks. Each prompt lists its allowed values and says to return "unknown" when the call did not make it clear.
9. Give it a number, as the design's number plan says. List numbers with
   `listPhoneNumbers` and show each one with the agent it serves now
   (`active_bot_id` is that agent's id, or false; name it from `listAgents`). Attach only the number the user names, with
   `attachPhoneNumber` { phone_number_id, agent_id }. If that number serves
   another agent, say that agent stops answering on it, and attach only after
   the user's yes. If no number exists, ask the user whether to buy one or
   import one they own. To buy: ask for the region and for landline or mobile,
   take the `carrier` from the `references/carriers.md` resource, run
   `searchPhoneNumbers` { region, carrier }, and confirm the exact number and
   price before `purchasePhoneNumber`, which charges the account. To import:
   importTwilioNumber / importExotelNumber / importSipTrunk.
   Optional knowledge base: `uploadKnowledgeBaseFile` then `attachKnowledgeBaseFiles` { file_ids, agent_id }.
10. If `getAgent` showed `can_publish: true`, save this as version "V1" with `createAgentVersion`. A 409 `no_changes` means a saved version already holds this state: rename that newest version to "V1" with `renameAgentVersion`.
11. (No test_number given.) Ask the user whether to call their phone now to test the agent. With their number, place the call with `dispatchCall`. Pass `call_context` with a value for every [variable] the welcome and prompt use, and the number from step 9 as `from_number_id` (omit it only if the account has no numbers).
12. Check `listCallLogs` { agentid, pagesize: 1 } at most 5 times, 30 seconds apart; if no new row appears, tell the user and stop. Then `getCallLog` on its id. The call is verified ONLY if `call_conversation` is non-empty (the agent actually spoke). A successful dispatch response alone is not proof. Tell the user what happened on the call and propose fixes with the update steps of the omnidim-build-agent skill.

Final reply, only this: the agent's name and id, the version saved, the settings applied, the conflicts and which source lost, the [MISSING] items and open risks, anything the user must set in the dashboard, and six lines to say on the first test call: the happy path, one-word replies, the toughest objection, a language switch, "are you AI?", and "stop calling". Do not paste the full prompt unless asked.

Throughout: phone numbers are E.164 with a leading `+`. See the `references/routing.md` resource for the full gotcha list.

## Change an agent safely: read what is saved, show the exact edits, back it up, apply on the user's word, and undo if asked.

Update an existing OmniDimension agent:

"<which agent, and what to change>"

1. Fetch the agent with `getAgent`, and the call with `getCallLog` if the user gave a call id. The dashboard may have changed the agent since the last edit, so work from what is saved now. If `published_state.is_published` is true, callers hear version `published_version_number`, not this draft: diagnose calls against that version (`diffAgentVersion` { version_number: <it>, against: "current" }). Tell the user this change edits the draft only, and callers hear it after they publish in the dashboard. If `has_unpublished_changes` is true, say that other draft edits will go live with it.
2. Check these every time and report any mismatch: the voice's gender against the prompt's verb forms; the transcriber and voice language against the primary language; the saved `languages`; the welcome, voicemail, silence nudges and closing line against the identity rule.
3. For each issue, name the cause in one line: a missing rule, a weak rule, two rules that conflict, a wrong fact, a bad example, a setting rather than the prompt, pronunciation, or new content.
4. Pick the size of the fix.
   - SMALL FIX (the default): the least new text. In order of preference: fix an existing example, tighten an existing rule, add one example, add one rule. Never rewrite a section, and never repeat a rule.
   - BIG CHANGE (new facts or stories, a new tone, identity or goal): update every section it touches, keep the eight sections of the `references/agent-prompt.md` resource, and say "BIG CHANGE" at the top. If the prompt does not use the eight sections, keep its structure, and offer to move it to the eight sections as a separate change.
   Either way, keep the prompt's style. When a fact changes, change every place it appears. Never add a fact the user did not give. Never apply anything that makes the agent deny being AI, threaten, or ignore a stop request: tell the user instead.
5. Show the user:
   - EDITS: # | section | Replace, Insert after or Delete | the exact current text | the exact new text | why. For a BIG CHANGE, show each changed section in full instead.
   - SETTINGS, only if needed: setting | now | change to.
   - WATCH OUT: any other line that could still cause the old behaviour.
   - TEST: two caller lines that would show the fix works.
6. Wait. When the user says "Apply":
   - Backup. If `getAgent` showed `can_publish: false`, version history is off for this agent: tell the user the change cannot be undone, and apply only after they agree. Otherwise save a backup with `createAgentVersion` and keep the `version_number` it returns. A 409 `no_changes` means the newest version already holds this state: take its `version_number` from `listAgentVersions`. A 409 `version_limit_reached` means there is no room for a backup: stop, and ask the user which old version to delete. A 403 `feature_disabled` or `feature_not_entitled` means no history: ask as above. Never apply without a backup `version_number` or the user's yes.
   - Apply exactly these edits with `updateAgent`, sending only the fields you change, in the shape the `references/agent-config.md` resource gives for reading an agent back. `getAgent` names differ: sections come back as `context_title` and `context_body`, but `updateAgent` takes `title`, `body` and `is_enabled`, and sending the `getAgent` names back does not save the sections. Copy `is_enabled` for every section, or a disabled section turns back on. Send the complete `context_breakdown` list, never only the changed section, because it replaces the whole list. In `end_call`, send `enabled` only to turn ending the call on or off. If the response has `warnings`, part of the edit was not saved: fix each named field and send it again.
   - Fetch the agent again and check that every section has its title and body. If any is blank, restore the backup `version_number` at once and tell the user.
   - Save the new version (V1.1, V1.2 and so on) with `createAgentVersion`, and confirm in one line what changed.
   If the user says "Undo", restore the backup `version_number` with `restoreAgentVersion`.
7. Offer a test call to check the fix, as in the build steps of the omnidim-build-agent skill's test steps. On a published agent, wait until the user has published: a call before that plays the old version.

## Safely roll an agent back to an earlier saved version: find the right snapshot, preview exactly what restoring changes, then restore.

Roll agent <agent_id> back to an earlier version.

Goal: "<the earlier state the user wants back>"

Follow these steps in order. Do NOT restore blindly, preview first.
1. `listAgentVersions` { agent_id: <agent_id> }. Versions come newest first; each has a `version_number`, `kind` (manual or system), who saved it, when, and a `change_summary` (what changed in that version). Use the names, timestamps, and change summaries to find the one that matches the goal above. If version history is off for this org these calls return 403 (feature_disabled); report that and stop. A 403 feature_not_entitled means the plan has no version history.
2. Preview the effect: `diffAgentVersion` { agent_id: <agent_id>, version_number: <chosen>, against: "current" }. This shows what restoring that version would change, grouped by area (Settings, Prompt, Transfer rules, etc.). In each change, `old` is this version's value and `new` is the agent's value now: restoring turns each `new` back into `old`. If it reports no meaningful change, tell the user there is nothing to restore and stop. Show the user this preview and restore only on their yes.
3. Restore: `restoreAgentVersion` { agent_id: <agent_id>, version_number: <chosen> }. This automatically keeps the current state as a backup first (a new "Backup before restore" version, or an existing version that already holds the same state), so the restore is itself undoable, then writes the chosen config back. On a published agent it writes the draft only: callers hear it after the user publishes in the dashboard. Report every item in the response's `skipped` list to the user: anything that no longer exists or is not allowed is dropped, not restored.
4. Confirm: tell the user which version holds the backup (`safety_version` in the restore response) and that they can undo by restoring it.

See the `references/agent-versioning.md` resource for the full tool list and the diff `against` modes.

## References

- [agent-config](references/agent-config.md)
- [agent-prompt](references/agent-prompt.md)
- [agent-versioning](references/agent-versioning.md)
- [call-tips](references/call-tips.md)
- [carriers](references/carriers.md)
- [recommended-stack](references/recommended-stack.md)
- [routing](references/routing.md)
- [voices](references/voices.md)
