# Use Lynkgrids in Claude Code

Add the marketplace, then install the plugin:

```text
/plugin marketplace add lynkread/lynkgrids-plugin
/plugin install lynkgrids@lynkgrids
```

The plugin bundles the Lynkgrids MCP server, so the tools are available straight away.
The first call opens a Lynkgrids sign-in page in your browser; sign in and press Allow.

## Without the browser sign-in

Create a Read & write API key in the Lynkgrids app under Settings › Workspace › API keys,
then:

```bash
claude mcp add --transport http lynkgrids https://mcp.lynkgrids.com/mcp --header "Authorization: Bearer <your key>"
```

Keep the key out of files and chat; it belongs in that command only.

## Slash commands

- `/lynkgrids:find-leads <who, and which list>`
- `/lynkgrids:check-connections <which list>`

Restart Claude Code if the plugin does not appear immediately after install.
