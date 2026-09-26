---
name: onboard
description: Set up an Open Voice Shield account end to end with the MCP tools - sign up, API key, the destination calls go on to, routing, IP whitelist, webhook, alerts, then a test call that proves a verdict comes back. Use when someone wants to connect their switch, carrier or PBX to Open Voice Shield, or asks how to get started with call fraud analysis on a SIP trunk.
---

# Onboard an account

Open Voice Shield is one extra SIP hop on a trunk the customer already runs. Every call through it
is recorded, transcribed and scored (probability, category, red flags, TCPA note, block / review /
allow), the route is checked for false answer supervision, and each leg's audio quality is graded.
Everything below is a tool on the `open-voice-shield` MCP server; nothing needs the portal.

## The path, in order

1. `get_started` - the flow, the current prices and the SIP address the customer will send calls to.
2. `signup` (e-mail, password, company) - creates the account with US$5 of credit and returns an
   access token. If the person already has an account, `login` instead. In Claude, the first tool
   that needs an account opens the consent page, where the person signs in or up themselves.
3. `create_api_key` - a long-lived key for their own scripts. Show it once; it is not retrievable.
4. `add_destination` - where calls go on to after analysis: the host, port and transport of their
   switch or carrier. Set `is_default: true` on the first one. `check_destination` sends a SIP
   OPTIONS ping and reports whether it answers.
5. `add_routing_rule` (optional) - a dialled-number prefix to a destination, for more than one route.
6. `whitelist_ip` (optional) - the source address their switch sends INVITEs from. Not needed for
   test calls. A `dial_prefix` and label on an entry lets several sub-customers share one IP.
7. `set_webhook` (optional) - a signed POST of every analysed call to their system; `test_webhook`
   sends a sample event. `add_alert_rule` sends e-mail on verdicts above a threshold;
   `connect_slack_webhook` posts them to a Slack channel.
8. `place_test_call` - the platform dials a number through the destination and plays a speech
   sample: `route_test` (a neutral announcement) or `scam_sample` (a known card-services scam, to
   confirm a block verdict). Poll `get_test_call` until `status` is `done`, then `get_call`,
   `get_transcript` and `report_links` for the verdict, red flags, transcript, per-leg quality and
   the PDF.

## What to tell the person

- Their switch sends calls to the SIP address from `get_started`, with the dialled number as-is;
  the platform relays the call to their destination. No B2BUA change, no media change on their side.
- A test call rings a real phone and is billed like any call: per second after the first minute
  plus the per-call analysis fee of the account's AI tier. Ask for a number they are allowed to call.
- Verdicts arrive within minutes of hangup. The customer portal at https://ovs.telecomsxchange.com/app
  shows the same calls, and every write made here is in the account's audit log.
- Suspicious callers can be blocked or diverted automatically by an automation rule in the portal.

## When something fails

- `401` on a tool: the credential is missing or expired; in Claude, let the consent page run again.
- `check_destination` unreachable: their firewall does not admit the platform's SIP address, or the
  port or transport is wrong. Fix that before a test call.
- A test call `failed`: read `get_test_call` for the SIP cause and the reason; the most common are
  the destination rejecting the call and the number being unreachable.
