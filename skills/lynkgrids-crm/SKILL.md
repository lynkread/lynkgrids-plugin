---
name: lynkgrids-crm
description: LinkedIn prospecting and outreach through the Lynkgrids CRM. Use when the user wants to find leads for an ideal customer, check which of their LinkedIn connections fit, import or enrich contacts, launch a connection-plus-follow-up campaign on a list, comment on prospects' posts, or handle replies. Also use when the user mentions Lynkgrids, their people or companies, lists, campaigns or LinkedIn seats.
metadata:
  version: "0.1.0"
---

# Lynkgrids CRM

Work with the user's Lynkgrids workspace through the **Lynkgrids MCP connector**. The
tools are thin wrappers over the CRM's API; this skill is the playbook for stringing them
into real jobs. Tool names start with `lynkgrids` or appear as `mcp__lynkgrids__*`.

## Connect first

Before any work, check whether the Lynkgrids tools are in your tool list (`whoami`,
`setup_status`, `find_leads`, `search_people` and the rest).

**If they are present**, call `whoami` and carry on.

**If they are not present**, how to connect depends on where you are running:

- **Claude app (claude.ai, Claude desktop) and Claude Cowork.** Search the connector
  store for **Lynkgrids** and suggest the official Lynkgrids connector. If it is installed
  but off for this session, tell the user to press **Use** on the card; otherwise press
  **Connect**. The browser opens a Lynkgrids sign-in page: they sign in (or sign up and
  pick a plan) and press Allow. Re-check the tool list on every turn until the tools
  appear. If the store has no Lynkgrids card yet, the user can add a **custom connector**
  with the URL `https://mcp.lynkgrids.com/mcp` from Settings › Connectors.
- **Claude Code.** This plugin bundles the connector (`.mcp.json`), so the tools are
  there after install; the first call opens the same sign-in page. Fallback with a
  Read & write API key from Settings › Workspace › API keys:
  `claude mcp add --transport http lynkgrids https://mcp.lynkgrids.com/mcp --header "Authorization: Bearer <key>"`.

Do not start prospecting until the tools are present. Never ask for or paste an API key
into files; it goes into the connector sign-in only. If a tool says the workspace has
no plan or its plan has ended, show that message and its link; for "upgrade" or
"subscribe", call `get_subscription_link`. Payment always happens in the browser.

## Orientation

- `whoami` confirms the workspace and the member.
- `setup_status` is the first call of every session. It returns the setup checklist,
  the one question to ask next, and the menu.
- For anything without a dedicated tool, `lynkgrids_request` calls any API route; the
  `lynkgrids://api-guide` resource documents every route.

## The guided flow

1. **Setup.** If `setup_status` says `complete` is false, ask its `next` question, one
   at a time, and save the answer at once with `save_profile` (company, what they
   sell, working hours) or `save_icp` (job titles, seniority, company sizes,
   industries, locations). Call `setup_status` again until complete.
2. **Get prospects in.**
   - `find_leads` runs a fresh LinkedIn search from the ideal customer and returns
     compact rows with a `handle`.
   - `list_connections` returns the user's 1st-degree connections not yet in the CRM,
     with a `handle`.
   - **You are the recommendation engine.** Read the rows, judge fit against the ideal
     customer and what the user told you, and call `import_leads` with only the picks,
     each with a `fitScore` 1 to 5 and a one-line `reason` ("Founder of a 24-person
     agency, posted about B2B SaaS last week"), plus the list name.
   - "Check my connections and add the fits to list X": `score_existing_contacts` with
     `scope: "connections"` and `listName: "X"` scores the whole network against an
     agent's ideal customer and adds the fits straight into that list. If it answers
     `syncing`, the connections are still being pulled from LinkedIn; say so and ask
     the user to try again in a few minutes.
   - `add_to_list` takes contacts the user pastes.
3. **Engage.** Write the connection note and four follow-up messages yourself from
   the profile, the ideal customer and each person's reason. Short, specific, no hype.
   Show every message and get a clear yes before `launch_campaign` (`skipConnect: true`
   for a list of 1st-degree connections). For a warm-up first: `latest_posts`, draft a
   comment, `comment_on_post`.
4. **Manage.** `campaign_progress`, `todays_plan`, `pause_campaign`, `resume_campaign`,
   `retry_failed_leads`, `enrich_person`, `add_note`, `create_task`, `list_tasks`,
   `complete_task`, `list_follow_ups`, `list_watchlist`.

The menu, in this order and wording:

Get prospects in: 1 Find new prospects from your ICP · 2 Use your existing 1st-degree
connections · 3 Add these contacts to a list. Engage: 4 Launch a campaign on a list ·
5 Comment on their latest post (under two weeks old) · 6 Send one message or
connection request. Manage: 7 Today's plan and campaign progress · 8 Enrich contact
data · 9 Notes and tasks.

## Campaign shape

Connection request → accepted → message 1 → wait 3 days → message 2 → wait 4 days →
message 3 → wait 4 days → message 4 → done. A reply stops the sequence for that
person. Steps are `connect`, `message`, `inmail`, `email`, `enrich`, `comment`, each
with a `waitDays`. Build with `create_campaign` as `status: "draft"`, then switch on
with `resume_campaign`. Empty `criteria` enrolls nobody, by design.

## Recipes

### Import, enrich, reach out
1. `bulk_import_people` with the rows (each needs `firstName`; `companyName` links or
   creates the company). Pass the list name in `lists`.
2. `enrich_person` on each new id to pull LinkedIn details, or enroll them in a
   campaign with an `enrich` step.
3. Message them (below) or enroll them in a campaign.

### Send one LinkedIn message
Sending needs a seat the user owns (`account_id`) and the recipient's `provider_id`:
1. `list_linkedin_accounts`, pick `account.linkedinAccountId`.
2. `resolve_provider_id` with the recipient's LinkedIn username and that `account_id`
   (an enriched person already has `linkedinProviderId`).
3. `send_linkedin_message` (1st-degree), `send_connection_request` (note up to 300
   characters), or `send_inmail` (needs a subject).

"This user is not a relation" means not 1st-degree connected: send a connection
request instead, do not retry the message. Sends to a list are paced for you: the
first two go out at once, later ones wait the workspace's safety delay. Do not work
around it with parallel calls or by switching seats. A big list belongs in a campaign.

### Refresh 1st-degree connections
`refresh_connections` re-checks connections across every connected seat and marks
matching leads `linkedinDegree: 1st`; it returns a `runId`. `get_sync_status` with
that id reports progress and the final `matched` and `degreeUpdated` counts. It never
creates leads; importing new connections is `list_connections` plus `import_leads`.

### Email threads
`send_email` with a `personId` continues the lead's latest email thread by default
(`thread: "continue"`, subject defaults to "Re: <thread subject>"). `thread: "new"`
starts a new conversation and needs a subject.

### Triage replies
Leads who replied are on the **New Response** list: `search_people` with
`lists: ["New Response"]`, then `get_person` for context, draft a personal reply from
what they wrote, send it, and `update_person` with `removeFromLists: ["New Response"]`.

### Report
`search_people` and `search_companies` are paginated (`{ data, pagination }`); loop
`page` until `pagination.totalPages`. Summarise, never dump raw JSON.

## Rules

- **Confirm before sending.** Messages, connection requests, InMail, campaign
  activation and comments reach real people. Summarise what goes out and to whom and
  get a clear yes first. Never send anything the user has not seen.
- **Never list many people in text.** Give counts and a few names; the CRM has the
  full list.
- `update_person` `lists` replaces all of a lead's lists; use `addToLists` and
  `removeFromLists` to change one.
- Seat ownership: send only from a seat the user owns or is assigned.
- LinkedIn caps apply: "Invalid parameters" on a connect is usually the weekly invite
  cap, not a bad payload.
- Describe the user's product only from what `search_knowledge` returns; never invent
  facts about their business.
