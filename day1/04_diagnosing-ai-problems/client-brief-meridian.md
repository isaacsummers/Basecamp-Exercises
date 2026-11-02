# Client Brief: Meridian Support Pilot

*One page. This is what Priya takes to her leadership.*

---

## Client & pilot

Meridian runs an AI agent that triages and resolves customer support tickets. A coordinator hands each ticket to a billing, technical, or account specialist. It has been live for three weeks and has been closing tickets that were not actually resolved.

## What's actually breaking it

The model is not the problem. The coordinator's instructions told it to pick one specialist per ticket, and to pick only the most urgent category. Ticket T-4471 contained two separate problems: an SSO outage that locked out about 40 people, and a $1,200 overcharge on a plan downgrade. The SSO problem is the urgent one, so the coordinator always sent the ticket to the account specialist and never routed anything to billing. The overcharge was never looked at. The coordinator then wrote a reply that called the ticket handled. That is the false resolution Priya's customer found.

## The fix

One file changed: `system-prompt-coordinator.txt`, step 5 and the routing guidance, plus the step 6 wording on closing tickets. The coordinator now lists every separate issue in a ticket and spawns one specialist per category that has an issue. Urgency sets the order of work, not which issues get handled. Step 6 now requires the reply to address every issue. It can only say "resolved" when each issue was actioned. Anything that needs a human approval, a security escalation, or an engineering fix gets stated plainly and marked "escalated". The model, the tools, the specialist prompts, and the scoring were not touched.

## Proof

- T-4471, five trials, before: 0 of 5 resolved, $0.127 per trial.
- T-4471, five trials, after: 4 of 5 resolved, $0.191 per trial.
- Holdout (T-4471 plus two different tickets, three trials each): 8 of 9 resolved, $0.184 per trial. The two new tickets resolved 3 of 3 each. The one miss was a T-4471 trial where the refund was not completed. One T-4503 trial also overclaimed in its reply, so the fix is strong but not perfect.

## What it would take

A change to one prompt file, with a re-run of the scoreboard and holdout after each edit. The cost per ticket rises about 50 percent, because two specialists now run where one used to. Cost still sits well under a dollar per ticket. To hold cost flat, the next step would be to have the coordinator skip a specialist when an issue is clearly out of scope, and to tighten the overclaim check that still misses on T-4503.

## The objection we'll get

"Why not just use a better model?" We tested that. On the original instructions, Opus 4.8 resolved 0 of 5 trials at $0.262 per trial, the same result as Sonnet at 0 of 5 and $0.127. Opus also routed all five trials to the account specialist only, so it dropped the refund the same way. On the fixed instructions, Opus resolved 4 of 5 at $0.337 per trial, while Sonnet resolved 4 of 5 at $0.191. The bigger model never beat the smaller one on resolution and cost about 1.8 times as much with the fix in place. The lever is the instructions, not the model.
