---
name: fraud-review
description: Review analysed calls on Open Voice Shield - list calls scored block or review, read a call's verdict, red flags, transcript and PDF report, and act on the caller. Use when someone asks what was flagged overnight, why a call was blocked, what a caller said, or wants evidence for a complaint or a TCPA case.
---

# Review flagged calls

Every analysed call carries a verdict: `probability` 0 to 100, `category` (credit card scam, IRS or
government impersonation, tech support scam, robocall, telemarketing, legitimate...), the
impersonated `entity`, numbered `red_flags` quoting what was said, a `tcpa_comment` and a
`recommendation` of block, review or allow.

## Steps

1. `list_calls` with a time window (`from`, `to`) and, when asked, `min_probability`. Sort by
   probability; say how many were block, review and allow (the recommendation is on each call).
2. For a call worth a look: `get_call` (verdict, entity, red flags, summary, timing, quality) and
   `get_transcript` (speaker-labelled, timed). Quote the red flags as the model wrote them; they
   cite the transcript.
3. `report_links` - the PDF report, the recording and the SIP capture, for a case file.
4. To act: the portal blocks or diverts a caller ID by an automation rule, and a case can be opened
   on the call; say where. Alert rules (`add_alert_rule`) and Slack (`connect_slack_webhook`) make
   the next one arrive without asking.

## How to talk about a verdict

- The probability is the model's confidence that the call is fraudulent, not a legal finding.
  "Review" means a person should listen. Never call a caller a criminal on the strength of one
  verdict; describe what the transcript shows.
- The TCPA comment is a compliance note for North American numbers only; it says "not applicable"
  otherwise.
- A call can be re-analysed with another model from the admin side; if the person disputes a
  verdict, a support ticket from the portal does that.
