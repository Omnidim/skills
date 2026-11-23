---
name: omnidim-client-onboarding
description: "Take a reseller's client on OmniDimension from nothing to a working phone number: create the account, fund it, verify it where the region requires it (India eKYC, US business checks), buy a number and attach it to an agent. Use when a reseller or agency onboards a new client, a client cannot buy a number because of KYC, or the user asks how to verify an account or set up a reseller client."
---

# Onboard a client

A reseller runs client accounts from its own product. This skill takes one client from nothing to a number it can call from, over the Reseller API. A runnable version of the same flow is at https://github.com/Omnidim/examples/tree/main/reseller-onboarding.

The MCP tools act on your own account. For a client, call the Reseller API at `https://backend.omnidim.io/api/v1` with the reseller's key from https://omnidim.io/api-management, sent as `Authorization: Bearer <key>`. The key must belong to the account owner or an admin. Read it from an environment variable, never from the chat.

For your own account, not a client's, verification runs in the dashboard number shop: https://omnidim.io. The steps below are for resellers.

## Steps

1. **Create the client.** `POST /reseller/users/add` with `name`, `email`, `phone` and `password`. Optional: `cost_per_min` (at least what you pay) and `concurrent_call_limit`. It creates the user and its account together.
2. **Fund it.** A new client has no balance, so its calls cannot run. Preview with `POST /reseller/credits/calculate`, then `POST /reseller/credits/transfer` with `to_organization_id`, `minutes` and `cost_per_min`. This moves the reseller's money, so confirm the amount with the user first.
3. **Check whether the region needs verification.** `GET /reseller/kyc/status?user_id=<id>&region=<IN|US>&carrier=<carrier>`. Each entry in `regions` has the carrier's `status`, its `next_step` and `can_purchase`.
4. **Run the verification loop.** Post each step to `POST /reseller/kyc/steps/<next_step>` with `user_id`, `region`, `carrier` and that step's fields, and follow `next_step` from each response until there is none. `GET /reseller/kyc/requirements?region=<r>&carrier=<c>` lists every step's fields and formats. Branch on each step's `method`, never on its name:
   - `submit`: post the fields.
   - `otp`: post the code the client just received. Ask the client for it, and never store it.
   - `redirect`: send a `return_url`, then give the client the single-use link to open in their own browser, and poll the step named in `poll_step`. The link expires within minutes, so never store or reuse it.
   - `upload`: send the document as `multipart/form-data` in the field `document`.
5. **Buy a number** on the carrier you verified: `GET /phone_number/search?region=<r>&carrier=<c>&user_id=<id>`, then `POST /phone_number/purchase` with `region`, `carrier`, `phone_number` and `user_id`. The rental comes out of the client's balance and renews monthly, so confirm the number with the user first.
6. **Attach the number** to one of the client's agents: `POST /phone_number/attach`.
7. **Optional:** issue the client its own API key, if its own system calls the API. The secret shows once.

## The carriers

| Carrier | Region | Stocks | Verification |
|---|---|---|---|
| `carrier-1` | IN | Landline, city codes 11, 12 and 80 | Codes by email and voice call, PAN, Aadhaar by OTP, preview, accept |
| `carrier-2-new` | IN | Mobile, 94 and 79 series | PAN, Aadhaar through a link, a document after each check, confirm |
| `carrier-us` | US | US local, by area code | Business details, address, authorized representative, confirm |

Don't rely on this table: a call without `carrier` returns `409 carrier_required` with the region's current carriers and what each stocks.

## Rules

- **`carrier` is required on every verification and number call.** Verification is per carrier. A client verified on one carrier must verify again on the other before it can buy there.
- **`account_type` is permanent.** It is `individual` (the default) or `business`, set at `register`. A business sends `business_name` exactly as on its GST certificate. With no GST number, run `skip-gst` in place of `verify-gst`.
- **No `next_step` is not always done.** The US carrier ends in its own review: read the status again and report `review_status`.
- **You are relaying the client's personal data.** Never store or log the PAN, Aadhaar number, codes, GST number, the link or the documents.

## Errors

| Status | Slug | What to do |
|---|---|---|
| 400 | `invalid_request` | A field is missing or in the wrong shape. Check `/reseller/kyc/requirements` |
| 409 | `step_order` | Run the step that `next_step` names first |
| 409 | `carrier_required` | Pick one of the carriers the response lists |
| 422 | `verification_failed` | The details or file were refused. Fix them with the client |
| 429 | `too_fast` | Wait about 30 seconds before retrying an Aadhaar step |
| 503 | `service_unavailable` | The verification service is down. Retry in a few minutes |

Field details: call `readDoc` on https://docs.omnidim.io/docs/reseller/your-clients/verification, or `searchDocs` for anything else.
