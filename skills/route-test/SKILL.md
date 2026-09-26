---
name: route-test
description: Test a vendor route or a destination through Open Voice Shield - place a test call, read post-dial delay, answer delay, estimated MOS, jitter and packet loss per leg, and any false answer supervision (FAS) alert. Use when someone asks whether a route is good, whether a carrier is cheating with fake answer, or wants to check call quality on a SIP destination.
---

# Test a route

A test call is placed by the platform through one of the account's destinations
(`place_test_call`: the destination, the number to dial, the speech sample and the length in
seconds). The recording, transcript, verdict, timing and quality are all on the call record.

## Steps

1. `list_destinations` - pick the destination (route) to test; `check_destination` first if in doubt.
2. `place_test_call` with `speech: "route_test"` (neutral announcement) and 20 to 40 seconds, to a
   number the person is allowed to call. Confirm the number and the cost with them before dialling.
3. Poll `get_test_call` every 10 to 15 seconds until `status` is `done` (or `failed`, with a reason).
4. `get_call` for the timing and quality: `pdd_ms` (post-dial delay), `answer_delay_ms`,
   `early_media`, `hangup_by`, `q850_cause`, and per leg the estimated MOS, jitter and packet loss.
   `route_alert` on the analysis, when present, is the false answer supervision finding with its
   evidence (a call answered with no live party, delayed playback, ringback after answer).
5. `report_links` - the PDF report and the SIP ladder for the person's records or a vendor dispute.

## How to read it

- MOS above 4.0 on both legs is good; 3.5 to 4.0 is acceptable; under 3.5 the person will hear it.
- Post-dial delay under 3 s is normal for a wholesale route; over 6 s is worth raising with the vendor.
- A route alert with `delayed_playback` or `no_live_party` evidence means the vendor billed
  minutes nobody spoke. Say so plainly and point at the evidence lines and the recording.
- Compare against the same route's earlier test calls (`list_calls` filtered by the destination)
  before calling a change a trend.

## Scheduling

Route tests are placed on demand here. For a daily check, the person sets a schedule on the CLI
verification page of the portal or asks their own scheduler to call this skill each morning.
