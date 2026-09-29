---
name: onboard
description: Set up an Open Voice Shield account end to end with the MCP tools - sign in, the destination calls go on to, routing, IP whitelist, webhook, alerts, then a test call the person confirms that proves a verdict comes back. Use when someone wants to connect their switch, carrier or PBX to Open Voice Shield, or asks how to get started with call fraud analysis on a SIP trunk.
---

# Onboard an account

Open Voice Shield is one extra SIP hop on a trunk the customer already runs. Every call through it
is recorded, transcribed and scored (probability, category, red flags, TCPA note, block / review /
allow), the route is checked for false answer supervision, and each leg's audio quality is graded.
The steps below are tools on the `open-voice-shield` MCP server. Three things always happen in the
portal and never in the chat: signing in, entering secrets, and confirming a test call.

## The path, in order

1. `get_started` - the flow and where the docs are. No account needed.
2. Sign-in: the first tool that needs an account answers 401, and the client opens the Open Voice
   Shield page where the person signs in, or signs up (new accounts get US$5 of credit). Never ask
   for their password, and never ask for an API key.
3. `add_destination` - where calls go on to after analysis: the host, port and transport of their
   switch or carrier. Set `is_default: true` on the first one. `check_destination` sends a SIP
   OPTIONS ping and reports whether it answers. If the destination needs a credential (a voice
   agent's digest password, a provider API key, a webhook signing secret), send the person to the
   portal's Network page to enter it. `list_destinations` shows `needs_credential`.
4. `add_routing_rule` (optional) - a dialled-number prefix to a destination, for more than one route.
5. `whitelist_ip` (optional) - the source address their switch sends INVITEs from. Not needed for
   test calls. A `dial_prefix` and label on an entry lets several sub-customers share one IP.
6. `set_webhook` (optional) - a signed POST of every analysed call to their system. The signing
   secret is set on the portal's Developers page first; then `set_webhook` takes the URL and
   `test_webhook` sends a sample event. `add_alert_rule` sends e-mail on verdicts above a
   threshold. To post them to Slack, the person connects a channel on the Connectors page, and
   `list_channels` gives its id for the rule.
7. `place_test_call` - requests a test call. It returns `status: awaiting_confirm` and a
   `confirm_url`. Give the person the link: the portal shows the number, caller ID, mode and
   duration, and nothing rings until they press Confirm (the request lapses after 10 minutes). Then
   poll `get_test_call` until `status` is `done`, and read `get_call`, `get_transcript` and
   `report_links` for the verdict, red flags, transcript, per-leg quality and the PDF. Use
   `route_test` for a first test. `scam_sample`, which confirms a block verdict, only rings a
   number the person has verified (see below).

## What to tell the person

- Their switch sends calls to the platform's SIP address with the dialled number as-is, and the
  platform relays the call to their destination. No B2BUA change, no media change on their side.
- A test call rings a real phone and is billed like any call. It opens with a recorded
  announcement that it is an automated test call placed on behalf of an Open Voice Shield account
  holder, and that it is recorded. It is recorded, transcribed and analysed like any call, so dial
  only numbers they are allowed to call.
- Caller ID: a test call presents Open Voice Shield's own number, or one of the account's verified
  numbers (`list_verified_numbers`). To verify a number, the person opens Verified numbers in the
  portal: the platform calls it and speaks a six-digit code they type in. You cannot do this for
  them.
- Verdicts arrive within minutes of hangup. The portal at https://ovs.telecomsxchange.com/app shows
  the same calls, and every write made here is in the account's audit log.
- Recording consent on their own traffic is theirs to settle: https://ovs.telecomsxchange.com/privacy

## When something fails

- `401` on a tool: the sign-in is missing or expired. Let the client run the sign-in page again.
- `check_destination` unreachable: their firewall does not admit the platform's SIP address, or the
  port or transport is wrong. Fix that before a test call.
- `caller_id_not_verified` or `number_not_verified`: the number is not one of the account's
  verified numbers. Leave `caller_id` empty, or ask the person to verify the number in the portal.
- A test call stuck at `awaiting_confirm`, or `expired`: the person has not confirmed it. Resend
  the link, or `cancel_test_call` and request again.
- A test call `failed`: read `get_test_call` for the SIP cause and the reason. The most common are
  the destination rejecting the call and the number being unreachable.
