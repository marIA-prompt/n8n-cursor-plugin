# n8n plugin for Cursor

Connect Cursor to your n8n instance. The Cursor agent can then build, test, and run workflows through the n8n instance-level MCP server.

The plugin adds one MCP server. It stores no credentials: you sign in to n8n with OAuth.

## Requirements

- An n8n instance on n8n Cloud or self-hosted.
- Instance-level MCP turned on. An instance owner or admin turns it on in **Settings > Instance-level MCP**. For details, see [Connect to the n8n MCP server](https://docs.n8n.io/build/ways-of-building-workflows/connect-to-n8n-mcp-server).

## Install

1. In Cursor, open **Cursor Settings > Plugins**, find **n8n**, and install it.
2. Cursor asks for the **n8n MCP server URL**. To get it:
   1. In n8n, open **Settings > Instance-level MCP** and click **Connect**.
   2. Select **Cursor** and copy the **Server URL**. It looks like `https://acme.app.n8n.cloud/mcp-server/http`.
3. Paste the URL and click **Save**.
4. Sign in to n8n in your browser and approve access.

To change the URL later, open **Cursor Settings > Plugins > n8n > Configure**.

On a Cursor team marketplace, an admin can set the URL one time for all members. Each member still signs in with their own n8n account.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| The n8n tools do not appear. | Restart Cursor after you set the URL. |
| The connection fails with a 404 error. | Use the full **Server URL** from n8n, including `/mcp-server/http`. |
| The n8n sign-in fails. | If your admin limits **Allowed callback URLs** in **Settings > Instance-level MCP**, add `http://localhost:8787/callback` (Cursor desktop) and `https://www.cursor.com/agents/mcp/oauth/callback` (Cursor web and cloud agents). |
| The n8n tools show two times. | You also added n8n with the one-click link or in `~/.cursor/mcp.json`. Remove one of them. |

## Develop

Cursor loads local plugins from `~/.cursor/plugins/local`. It ignores symlinks that point outside that folder, so copy the plugin into it.

1. Clone this repository.
2. From the repository root, copy the plugin into the Cursor local plugins folder:

   ```bash
   mkdir -p ~/.cursor/plugins/local
   rsync -a --delete --exclude .git ./ ~/.cursor/plugins/local/n8n/
   ```

3. In Cursor, run **Developer: Reload Window**. The plugin shows in **Cursor Settings > Plugins** with a **Local** label.
4. Open the plugin, click **Configure**, paste the Server URL, and click **Save**.
5. Quit Cursor and open it again. Cursor applies the URL when it loads the plugin.

After each change, run the `rsync` command again and reload Cursor.

To see the URL that Cursor uses, search the newest `mcpprocess.log` for `origin:` (macOS):

```bash
grep "origin:" "$(ls -td ~/Library/Application\ Support/Cursor/logs/*/ | head -1)mcpprocess.log" | tail -3
```

Cursor reads environment variables before saved values. To test without the form, start Cursor with the variable set (macOS):

```bash
N8N_MCP_URL="http://localhost:5678/mcp-server/http" /Applications/Cursor.app/Contents/MacOS/Cursor
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
