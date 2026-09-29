---
name: cli-verification
description: Verify caller ID delivery on a route with Open Voice Shield - check which countries are covered, run a CLI check that dials a number in the target country with a chosen caller ID and reports what arrived (match, modified, withheld, missing), and host a number to earn per check. Use when someone asks whether their caller ID is delivered intact, whether a vendor strips or rewrites CLI, or wants to enrol a number.
---

# Verify caller ID

A CLI check places a short call from one of the account's destinations, with the caller ID the
person wants to prove, to a number the platform holds in the requested country. The call only ever
reaches the platform's own verification numbers, never a third party. The answer is what
that number saw: `match`, `modified` (format, prefix or digits changed), `withheld`, `missing` or
`not_received`, with the call's timing and estimated audio quality.

## Steps

1. `cli_coverage` - the countries with a number available now. Uncovered countries can be requested
   from the portal; requests decide what is sourced next.
2. `verify_cli` with the destination, the country and the caller ID to send. Each check costs the
   published per-check fee plus the short call it places; say so before running a batch.
3. Poll `get_cli_check` until it is finished, then report the result and, for `modified`, exactly
   what changed (the sent and the received number side by side).
4. For a recurring check with an alert on change, the person sets a schedule on the portal's CLI
   verification page.

## Verified numbers and hosted numbers

A CLI check is not a test call. A test call's caller ID must be one of the account's verified
numbers (`list_verified_numbers`), which the person verifies in the portal: the platform calls the
number and speaks a code they type in. A hosted number that passed its pointing test counts as
verified too.

## Hosting a number

Anyone who owns a working number in a wanted country can point it at the platform and earn per
check it answers: `host_number` enrols it, `test_hosted_number` confirms it is live after the
person calls it once from any phone, `list_hosted_numbers` shows status and earnings. Numbers in
Africa, the Middle East and Asia are the most wanted.
