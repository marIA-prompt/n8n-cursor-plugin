# n8n plugin for Cursor

Connect Cursor to the Senff n8n instance. The Cursor agent can then build, test, and run workflows through the n8n instance-level MCP server.

The plugin adds one MCP server pointed at `http://n8n-operacoes.senff.com.br:5678/mcp-server/http`. It stores no credentials: you sign in to n8n with OAuth.

## Requirements

- Network access to `n8n-operacoes.senff.com.br` on port `5678`.
- Instance-level MCP turned on. An instance owner or admin turns it on in **Settings > Instance-level MCP**. For details, see [Connect to the n8n MCP server](https://docs.n8n.io/build/ways-of-building-workflows/connect-to-n8n-mcp-server).

## Install

1. In Cursor, open **Cursor Settings > Plugins**, find **n8n**, and install it.
2. Sign in to n8n in your browser and approve access.

The plugin uses Streamable HTTP (`type: streamable-http`), matching the Cursor snippet from n8n's **Connect a client** dialog.

To point at a different instance later, edit `mcp.json` and replace the `url` value with the **Server URL** from n8n (**Settings > Instance-level MCP > Connect**, select **Cursor**). It looks like `https://acme.app.n8n.cloud/mcp-server/http`. Then reload Cursor.

On a Cursor team marketplace, an admin can install this plugin once for all members. Each member still signs in with their own n8n account.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| The n8n tools do not appear. | Restart Cursor after installing the plugin. Confirm you can reach `http://n8n-operacoes.senff.com.br:5678`. |
| The connection fails with a 404 error. | Confirm the URL in `mcp.json` is the full **Server URL** from n8n, including `/mcp-server/http`. |
| The n8n sign-in fails. | If your admin limits **Allowed callback URLs** in **Settings > Instance-level MCP**, add `http://localhost:8787/callback` (Cursor desktop) and `https://www.cursor.com/agents/mcp/oauth/callback` (Cursor web and cloud agents). |
| The n8n tools show two times. | You also added n8n with the one-click link or in `~/.cursor/mcp.json`. Remove one of them. |
| Cloud Agents or Cursor CLI ignore the server. | Some Cursor CLI builds only accept `"type": "http"` (not `"streamable-http"`). Change `type` in `mcp.json` to `http` if the IDE works but the agent does not. |

## Develop

Cursor loads local plugins from `~/.cursor/plugins/local`. It ignores symlinks that point outside that folder, so copy the plugin into it.

1. Clone this repository.
2. From the repository root, copy the plugin into the Cursor local plugins folder:

   ```bash
   mkdir -p ~/.cursor/plugins/local
   rsync -a --delete --exclude .git ./ ~/.cursor/plugins/local/n8n/
   ```

3. In Cursor, run **Developer: Reload Window**. The plugin shows in **Cursor Settings > Plugins** with a **Local** label.
4. To test against a local n8n instance instead of Senff, edit `mcp.json` and set `url` to `http://localhost:5678/mcp-server/http`.
5. Quit Cursor and open it again. Cursor applies the URL when it loads the plugin.

After each change, run the `rsync` command again and reload Cursor.

To see the URL that Cursor uses, search the newest `mcpprocess.log` for `origin:` (macOS):

```bash
grep "origin:" "$(ls -td ~/Library/Application\ Support/Cursor/logs/*/ | head -1)mcpprocess.log" | tail -3
```

Cursor shows local plugins with a capitalized name, such as **N8n**. Marketplace installs use the `displayName` from `plugin.json`.

### Validate

Run the Cursor plugin template validator from the repository root:

```bash
git clone --depth 1 https://github.com/cursor/plugin-template /tmp/cursor-plugin-template
node /tmp/cursor-plugin-template/scripts/validate-template.mjs
```

## License

[Apache-2.0](LICENSE)
