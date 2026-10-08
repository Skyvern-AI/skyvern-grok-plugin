# Skyvern plugin for Grok Build

Give Grok a real browser. This plugin connects Grok Build to [Skyvern](https://www.skyvern.com)'s
hosted MCP server, so Grok can open pages, click, type, fill forms, upload files, read page content,
take screenshots, and inspect console and network activity on live websites.

The browser runs in Skyvern Cloud. Nothing is installed on your machine and no code runs locally:
the plugin is one MCP server config and one skill.

## Install

1. Install Grok Build and sign in (see the [Grok Build docs](https://docs.x.ai/build/overview)).
2. Start `grok`, run `/marketplace`, find **skyvern**, and install it. Or from a terminal:

   ```bash
   grok plugin install skyvern --trust
   ```

3. Open `/mcps`, select **skyvern**, and sign in. Your browser opens the Skyvern sign-in page. You
   need a Skyvern account; [create one here](https://app.skyvern.com).
4. Ask Grok for something that needs a website.

## What is in the plugin

| Component | Path | Purpose |
|---|---|---|
| MCP server | `.mcp.json` | Skyvern's hosted server over HTTP, browser tool surface |
| Skill | `skills/skyvern/SKILL.md` | When to use each tool, session handling, login and safety rules |

There are no hooks, commands, agents, scripts, or local binaries.

## Tools

The plugin connects the **browser** surface of the Skyvern MCP server (54 tools):

| Group | Tools |
|---|---|
| Sessions and profiles | `skyvern_browser_session_create`, `_close`, `_list`, `_get`, `_connect`; `skyvern_browser_profile_create`, `_list`, `_get`, `_update`, `_delete` |
| Navigation and actions | `skyvern_navigate`, `skyvern_click`, `skyvern_type`, `skyvern_select_option`, `skyvern_press_key`, `skyvern_hover`, `skyvern_drag`, `skyvern_scroll`, `skyvern_file_upload`, `skyvern_wait`, `skyvern_handle_dialog` |
| AI actions | `skyvern_act` (describe the action in plain language), `skyvern_login` (sign in with a stored credential) |
| Batch | `skyvern_observe`, `skyvern_execute` |
| Reading | `skyvern_get_html`, `skyvern_get_value`, `skyvern_get_styles`, `skyvern_find`, `skyvern_screenshot`, `skyvern_evaluate` |
| Tabs and frames | `skyvern_tab_list`, `_new`, `_switch`, `_close`, `_wait_for_new`; `skyvern_frame_list`, `_switch`, `_main` |
| Debugging | `skyvern_console_messages`, `skyvern_network_requests`, `skyvern_network_request_detail`, `skyvern_network_route`, `skyvern_get_errors`, `skyvern_har_start`, `skyvern_har_stop` |

### Other tool surfaces

Skyvern serves narrower and broader surfaces at different URLs. To change, edit the `url` in your
installed copy of `.mcp.json`:

| URL | Surface |
|---|---|
| `https://api.skyvern.com/mcp/x/browser` | Direct browser control (this plugin's default) |
| `https://api.skyvern.com/mcp/x/lean` | The smallest browser surface |
| `https://api.skyvern.com/mcp/x/operate` | Run, monitor and schedule existing Skyvern workflows; no browser tools |
| `https://api.skyvern.com/mcp/x/build` | Author workflows |
| `https://api.skyvern.com/mcp/` | Everything, including the autonomous task runner and structured extraction |

## Authentication

The plugin uses OAuth. On first connection Grok opens the Skyvern sign-in page in your browser and
stores the resulting token. The manifest carries no API key.

Passwords for the websites you automate are never typed by the model. `skyvern_login` signs in with
a credential you have stored in Skyvern, Bitwarden, 1Password or Azure Key Vault, referenced by id.

## If you already use Skyvern in Claude Code

Grok Build also loads MCP servers from `~/.claude.json` and `.mcp.json`. If one of those files
already defines a server named `skyvern`, Grok sees two servers with that name, and a session may
connect to your existing one, which usually points at the full tool surface, instead of this
plugin's. Run `grok mcp doctor skyvern` to see which URL is in use. To use the plugin's server,
rename or remove the other `skyvern` entry.

## Network endpoints and credentials

| Endpoint | Why |
|---|---|
| `https://api.skyvern.com/mcp/x/browser` | The MCP server. Every tool call goes here. |
| `https://api.skyvern.com/oauth/*`, `https://clerk.skyvern.com/oauth/*` | OAuth sign-in, token exchange and client registration. |

The plugin itself contacts nothing else and sends no telemetry. The websites you ask Grok to visit
are loaded by the browser in Skyvern Cloud, not from your machine.

Credentials required: a Skyvern account, authorized through OAuth. The plugin reads no local files,
environment variables, or tokens.

Tool calls count against your Skyvern plan's usage.

## Links

- [Skyvern MCP documentation](https://www.skyvern.com/docs/developers/getting-started/mcp)
- [Skyvern on GitHub](https://github.com/Skyvern-AI/skyvern)

## License

MIT. See [LICENSE](LICENSE).
