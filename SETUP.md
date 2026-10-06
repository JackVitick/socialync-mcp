# Socialync plugin setup (Claude Code)

The plugin adds one hosted MCP server, `socialync` at `https://mcp.socialync.io/mcp`, plus three skills that tell Claude when to draft, schedule, repurpose, or inspect your social queue. Nothing runs locally and no key is stored in the plugin.

## Sign in

1. Install the plugin, or add the server by hand: `claude mcp add --transport http socialync https://mcp.socialync.io/mcp`.
2. The first time a Socialync tool is called, Claude opens your browser. Sign in with your Socialync account (OAuth 2.1 with dynamic client registration; no client secret, nothing to paste).
3. The sign-in screen asks which of your brands Claude may manage. Revoke the grant any time from Socialync settings.

No Cursor callback URL is involved here; Claude Code handles the OAuth redirect itself.

Clients that cannot open a browser can use a per-brand API key from https://www.socialync.io/api-keys as `Authorization: Bearer <key>` instead. That is a fallback, not the normal path.

## Plan facts

- The free plan includes MCP: $0, no credit card, 5 posts per calendar month across 7 platforms (X requires a paid plan). One publish counts as one post however many platforms it targets.
- Paid plans start at $20/month for 5 connected accounts with unlimited posts. Read live numbers from `check_quota` rather than assuming.
- AI caption generation (`generate_content`) is a paid-plan feature.

## Rules the skills enforce

- Never call `publish_now`, `publish_scheduled`, or `approve_draft` without the user's explicit confirmation in chat. Show the exact caption, platforms, media, and time first.
- Run `validate_post` before every draft or publish; fix what it rejects.
- Video (including TikTok and YouTube) goes out as a scheduled post using media already in the Socialync library or a public video URL. A video file cannot be pasted into a chat.
- Before retrying a failed publish, read `get_post_history`; duplicate protection exists, but history is the source of truth.

Full tool reference: [docs/tools.md](docs/tools.md). Terms: https://www.socialync.io/developer-terms
