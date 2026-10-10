# Lynkgrids plugin

**Lynkgrids is your LinkedIn CRM, the outreach tool that finally remembers your customers.**

Other tools send the message and leave you to track the relationship. Lynkgrids does both.
It runs the LinkedIn outreach, a connection request and then follow-ups paced to keep your
account safe, and keeps every conversation as a tracked lead. This plugin brings that into
Claude. Describe your ideal customer and get leads, score your own connections against it,
enrich contacts, launch a campaign on a list, and handle replies, without leaving the
conversation.

Works in the Claude app (claude.ai and Claude desktop), Claude Cowork and Claude Code.

## Getting started

| Where | How |
| --- | --- |
| Claude app and Claude Cowork | Connect the **Lynkgrids** connector from the connector store, or add a custom connector with the URL `https://mcp.lynkgrids.com/mcp`. See [docs/install-claude.md](docs/install-claude.md). |
| Claude Code | `/plugin marketplace add lynkread/lynkgrids-plugin` then `/plugin install lynkgrids@lynkgrids`. See [docs/install-claude-code.md](docs/install-claude-code.md). |

Connecting opens a Lynkgrids sign-in page. Sign in, or sign up and pick a plan, and press
Allow. You need a Lynkgrids workspace with a connected LinkedIn account to search and send.

### Try it

> Find 50 Heads of Sales at B2B SaaS companies in India and add them to the list Q4 SaaS.

> Check my LinkedIn connections and put everyone who fits my ideal customer into the list
> Q4 founders.

> Who replied to my campaigns this week? Draft replies for them.

## What it does

| Capability | What happens | Tools used |
| --- | --- | --- |
| Find leads | Searches LinkedIn from your ideal customer, Claude ranks the rows, the picks join a list with a fit score and reason | `find_leads`, `import_leads` |
| Check connections | Scores your 1st-degree connections against an ideal customer and adds the fits to a list | `score_existing_contacts`, `list_connections` |
| Enrich | Fills headline, experience and company from LinkedIn | `enrich_person` |
| Outreach | Drafts a connection note and four follow-ups, launches a paced campaign on a list after you confirm | `launch_campaign`, `create_campaign` |
| Engage | Comments on a prospect's latest post, sends one message or connection request | `latest_posts`, `comment_on_post`, `send_linkedin_message` |
| Replies and reporting | Triages the New Response list, today's plan, campaign progress | `search_people`, `todays_plan`, `campaign_progress` |

Nothing is sent to a real person until you have seen the message and said yes.

## What is in this plugin

- `skills/lynkgrids-crm/SKILL.md`: the playbook Claude follows, including how to connect
  on each surface.
- `commands/find-leads.md` and `commands/check-connections.md`: two slash commands for
  the most common jobs (`/lynkgrids:find-leads`, `/lynkgrids:check-connections`).
- `.mcp.json`: the remote Lynkgrids MCP server, `https://mcp.lynkgrids.com/mcp`, so a
  Claude Code install connects the CRM too.
- `docs/`: install notes per surface.

The plugin runs no code on your machine. Everything it does goes through the Lynkgrids
MCP server, which calls the Lynkgrids API as you, with your own permissions.

## Privacy Policy

This plugin sends your requests to the Lynkgrids MCP server at `mcp.lynkgrids.com`,
which acts on your Lynkgrids workspace with the account you signed in with. Data about
your contacts, campaigns and LinkedIn activity is stored in your Lynkgrids workspace
under the [Lynkgrids privacy policy](https://www.lynkgrids.com/legal/privacy-policy.html)
and [terms of service](https://www.lynkgrids.com/legal/terms-of-service.html). The
plugin itself stores nothing and sends nothing anywhere else. Questions:
support@lynkgrids.com.

## Support

Email support@lynkgrids.com or use the [contact page](https://www.lynkgrids.com/contact-us.html).

## License

MIT. See [LICENSE](LICENSE).
