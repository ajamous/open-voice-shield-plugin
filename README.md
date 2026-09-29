# Open Voice Shield for Claude

Fraud verdicts, route checks and caller-ID verification for every call on a SIP trunk, operated by
Claude. The plugin connects Claude to the Open Voice Shield MCP server and adds four skills:

| Skill | What Claude does |
|---|---|
| `onboard` | Sign in, connect the customer's switch or carrier, routing, webhook, alerts, first test call |
| `route-test` | Test a vendor route: timing, per-leg audio quality, false answer supervision |
| `fraud-review` | List flagged calls, read verdicts, red flags, transcripts and PDF reports |
| `cli-verification` | Check whether a route delivers caller ID intact; host a number and earn per check |

## Install

Claude Code:

    claude plugin marketplace add ajamous/open-voice-shield-plugin
    claude plugin install open-voice-shield@open-voice-shield

Or add the MCP server alone: `claude mcp add --transport http open-voice-shield https://ovs.telecomsxchange.com/api/mcp`.

The server needs no key to start. The first tool that touches an account opens the Open Voice
Shield sign-in (or sign-up) page (OAuth); accounts start with US$5 of credit. No password, API key
or provider credential ever goes through the chat: secrets are entered in the portal.

Test calls ring real phones, so Claude can only request one. You confirm each call (number, caller
ID, mode, duration) in the portal before anything rings. Every test call opens with a recorded
announcement that it is an automated test call placed on behalf of an account holder, and that it
is recorded. A test call presents Open Voice Shield's own number, or a caller ID you have verified
in the portal. The scam sample only goes to a number you have verified. Calls are recorded and
transcribed, and transcripts are returned to Claude when asked for; see the privacy policy for
retention, subprocessors and deletion.

Docs: https://ovs.telecomsxchange.com/docs/agents · Privacy: https://ovs.telecomsxchange.com/privacy ·
Support: support@telecomsxchange.com

A TelecomsXChange product. MIT licence for the plugin files; the service has its own terms.
