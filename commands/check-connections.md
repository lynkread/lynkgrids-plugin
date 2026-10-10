---
description: Score the user's LinkedIn connections against an ideal customer and add the fits to a list
---

# Check my connections

The user's request is:

$ARGUMENTS

Load the `lynkgrids-crm` skill and follow its Connect first section until the Lynkgrids
tools are present. Then:

1. Call `agent_list`. One agent: use it. Several: ask which ideal customer to score
   against. None: propose one first with `propose_agent`, since scoring needs an ideal
   customer.
2. Call `score_existing_contacts` with `scope: "connections"`, and `listName` set to
   the list the user named so the fits go straight into it.
3. If the result is `syncing`, say the connections are still being pulled from
   LinkedIn and to ask again in a few minutes. If it is `no_seat`, point to
   connecting a LinkedIn account.
4. Report how many connections were looked at, how many matched, how many were added
   and to which list.

If `$ARGUMENTS` is empty, ask which list the fits should go into.
