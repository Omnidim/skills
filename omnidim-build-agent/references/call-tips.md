# Tips from real calls: what the best agents do

Measured on answered calls in the 30 days to 20 November 2026: 165 outbound
agents in 79 accounts and 35 inbound agents in 17 accounts, each with at least
100 answered calls. "Early drop" is a call the person hangs up within 15
seconds. "Natural end" is a call the agent ends or transfers when it is done.
These patterns go with better calls. They are not proof of cause, so test a
change on a few calls before you roll it out.

## 1. Open with a name check of 8 words or fewer

| Outbound `welcome_message` length | Early drop | Natural end |
|---|---|---|
| 8 words or fewer | 31% | 27% |
| 9 to 15 words | 37% | 15% |
| 16 to 25 words | 49% | 15% |
| More than 25 words | 46% | 8% |

In 11 accounts that run both short and long greetings, the short one lost
fewer early calls in 10 of them. A good first line asks only for the person:
"Hi, am I speaking with [name]?" Say who you are and why on the next turn,
after they answer.

## 2. On the second turn, say who, why, and ask for a minute

One sentence: the agent's name, the company, and the thing the person did,
such as "You asked about our evening course" or "You applied for the sales
role". Then ask for permission with a time: "Is now a good time for two
minutes?" If they are busy, ask when to call back, repeat the time, and end
the call politely.

## 3. Get past call screening

Many phones now answer unknown numbers with a screening assistant ("If you
record your name and reason for calling..."). Tell the agent to answer with
one sentence that holds its name, the company and the reason, then wait. If
the person says "please hold", the agent waits and does not repeat itself.

## 4. Fill in the person's details, and check every placeholder

Put variables in square brackets, like `[name]` or `[course]`. In a campaign
they come from the contact list columns, so each name must match a column
header, and every row needs a value. A missing value makes the agent say the
placeholder out loud.

## 5. Keep the temperature low for scripted calls

Outbound agents with `model.temperature` 0.3 or lower had 37% early drop and
21% natural end, against 45% and 14% at 0.3 to 0.7.

## 6. One short question per turn

Ask one thing, then listen. Repeat back what you heard in a few words
("Got it, the evening slot") before the next question. Long turns give the
person more reasons to hang up.

## 7. Speak the caller's language

Tell the agent to answer in the language the caller uses, and to keep the
words people really say for products and forms (often English terms inside
Hindi or another language). Pick the transcriber and voice from the
`recommended-stack.md` guide for that language.

## 8. End the call on purpose

Set `end_call` with a plain condition, for example "End the call once the
appointment is confirmed or the person says they are not interested." Agents
that end calls themselves leave fewer silent calls that time out.

## What did not show a clear pattern

The language model, the prompt length and the speech speed showed no steady
difference once accounts with very different call types were compared. Pick
the model by language and cost (`recommended-stack.md`), and
keep `voice.speech_speed` near 1.0 unless the user asks for a change.

If something here is unclear, call `searchDocs`.
