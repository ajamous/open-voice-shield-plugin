# Open Voice Shield for Claude

Fraud verdicts, route checks and caller-ID verification for every call on a SIP trunk, operated by
Claude. The plugin connects Claude to the Open Voice Shield MCP server and adds four skills:

| Skill | What Claude does |
|---|---|
| `onboard` | Sign up, connect the customer's switch or carrier, routing, webhook, alerts, first test call |
| `route-test` | Test a vendor route: timing, per-leg audio quality, false answer supervision |
| `fraud-review` | List flagged calls, read verdicts, red flags, transcripts and PDF reports |
| `cli-verification` | Check whether a route delivers caller ID intact; host a number and earn per check |

## Install

Claude Code:

    claude plugin marketplace add ajamous/open-voice-shield-plugin
    claude plugin install open-voice-shield@open-voice-shield

Or add the MCP server alone: `claude mcp add --transport http open-voice-shield https://ovs.telecomsxchange.com/api/mcp`.

The server needs no key to start. The first tool that touches an account opens a sign-in (or
sign-up) page; accounts start with US$5 of credit. Test calls ring real numbers and are billed
like any call.

Docs: https://ovs.telecomsxchange.com/docs/agents · Privacy: https://ovs.telecomsxchange.com/privacy ·
Support: support@telecomsxchange.com

A TelecomsXChange product. MIT licence for the plugin files; the service has its own terms.
