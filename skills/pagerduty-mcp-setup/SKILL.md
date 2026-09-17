---
name: pagerduty-mcp-setup
description: Setup and recovery for the PagerDuty MCP server. IMPORTANT Use the PagerDuty MCP tools directly with no pre-flight check, because the API token can be supplied at runtime. Read these instructions only if a PagerDuty MCP call fails to authenticate.
---

IMPORTANT: Use the PagerDuty MCP tools directly for the task at hand. Follow these directions only once a PagerDuty MCP call has failed to authenticate.

### Context

The configuration for the MCP of the PagerDuty Plugin lives in the following directory relative to the path to this SKILL.md file: `../../mcp.json`

In a non-configured state, the Authorization header contains the placeholder `Token ${PAGERDUTY_API_TOKEN}`.

IMPORTANT: That placeholder is the normal shipped state of the file and does NOT mean the MCP is unusable. `PAGERDUTY_API_TOKEN` is a required plugin variable declared in `.cursor-plugin/plugin.json`, and Cursor substitutes its stored value into the header at runtime — from what the user entered in the plugin's settings on a normal install, or from team-managed configuration in a Cursor Automation, where the MCP is pre-configured centrally. In both cases the on-disk file keeps the placeholder while PagerDuty works fine.

The PagerDuty-hosted MCP service expects the header to look like this:

```json
{
  "Authorization": "Token your-pagerduty-api-token-here"
}
```

If a PagerDuty MCP call fails to authenticate, stop investigating PagerDuty, tell the user that the PagerDuty MCP is not configured yet, and help them finish setup.

Do not read `mcp.json` or make a throwaway probe call to decide whether PagerDuty is available. The call your task needs is the check. Never post or report that the MCP is unconfigured unless a real call has failed to authenticate.

### Steps

Only once a PagerDuty MCP call has failed to authenticate (401, 403, `invalid_auth`, missing or rejected token), take the following steps. Any other error is a normal tool error — handle it as you would any other and do not send the user into setup.

1. Tell the user the PagerDuty MCP rejected the request because the API token is missing or invalid, and include the error you got back.
2. If you are running in a Cursor Automation or another team-managed environment, the token comes from the plugin's centrally managed settings, not from this repo. Tell the user to update the `PagerDuty API Token` value in the PagerDuty plugin's settings for the team or workspace. You cannot fix this from inside the run and reloading the window is not available to you, so report the authentication failure plainly, say what you were unable to check, and continue with whatever parts of the task do not need PagerDuty. Do not edit `mcp.json`.
3. Otherwise, on a local install, tell the user to set the token in the PagerDuty plugin's settings in Cursor, and in that message do all of the following:
   - Point them at the plugin's `PagerDuty API Token` setting, which is the `PAGERDUTY_API_TOKEN` variable Cursor substitutes into the Authorization header. Only the token value is needed; the `Token ` prefix is already in the header, and `mcp.json` itself should not be edited by hand because a plugin update will overwrite it.
   - Tell them they can follow PagerDuty's API token instructions here: `https://support.pagerduty.com/main/docs/pagerduty-mcp-server-integration-guide#generate-your-pagerduty-api-token`
   - Do not ask them to send you the token.
   - Do not inspect PagerDuty MCP tools or continue the PagerDuty task yet.
4. Once the token has been set, tell the user to refresh Cursor by opening the Command Palette (`⌘⇧P` on Mac or `Ctrl+Shift+P` on Windows/Linux) and running `Reload Window`. Use the current operating system to decide what keybinding to show.
5. Retry the call and continue on to using the PagerDuty MCP.

### Reference

- PagerDuty API token setup instructions: `https://support.pagerduty.com/main/docs/pagerduty-mcp-server-integration-guide#generate-your-pagerduty-api-token`
- PagerDuty hosted MCP setup guide: `https://support.pagerduty.com/main/docs/pagerduty-mcp-server-integration-guide`
- PagerDuty developer docs: `https://developer.pagerduty.com/docs/mcp-tooling-remote-server`

### Example code block to show the user

If it helps to show where the token ends up, reference the header the plugin variable fills in. Show it for orientation only — do not ask the user to edit this file:

```3:5:mcp.json
    "headers": {
      "Authorization": "Token ${PAGERDUTY_API_TOKEN}"
    },
```
