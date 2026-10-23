# Socialync MCP Server

![Socialync](assets/logo.png)

Official documentation, registry manifest, and Cursor plugin for the **Socialync hosted MCP server**. Socialync is a cross-posting and scheduling tool for creators: upload once, publish to TikTok, Instagram, YouTube, Facebook, X, LinkedIn, Threads, and Bluesky.

This MCP server lets Claude, ChatGPT, Cursor, Google Antigravity, Gemini CLI, OpenAI Codex, Grok Bot, and any MCP-compatible AI agent draft, schedule, and publish social media posts through your own OAuth-authorized Socialync account, with human-in-the-loop approval built into the workflow. It also reads back your analytics (views, likes, comments, shares, saves, followers, and your best and worst posts), so an agent can plan next week's content from what actually performed.

Every platform integration runs on the official platform API. No browser automation, no scraping, no unofficial endpoints. Your agent cannot get your account restricted on our behalf.

Duplicate protection means a retry can never publish twice, and failed posts are retried automatically. Both matter when an agent is doing the posting.

> **Note:** Socialync's server is hosted (remote) and closed source. This repository contains the documentation, the MCP Registry manifest, and the Cursor plugin files only. There is nothing to install or run from here.

## Endpoint

```
https://mcp.socialync.io/mcp
```

Transport: Streamable HTTP. Discovery document (tools, platforms, auth, limits, as JSON): https://mcp.socialync.io/.well-known/mcp

**MCP and API access are on the free plan.** $0, no credit card, 5 posts a month on 7 platforms (X is on paid plans). Your agent can connect and publish without anyone reaching for a card. Paid plans start at $20/month for unlimited posts and 5 connected accounts (Starter $20, Creator $30 for 10 accounts, Agency $99 for 100, extra accounts from $2 a month). [Plans and pricing](https://www.socialync.io/go/github-pricing).

Limits worth knowing: 1 API key per brand, and 10 AI-created posts per day per brand on paid plans (5 on free).

## Authentication, two ways

**A) OAuth 2.1 (preferred).** Add the URL with no headers. Your client registers itself (dynamic client registration, PKCE, no client secret), opens a browser, and you sign in with your Socialync account and choose which brands the agent may manage. This is what Claude.ai, Claude Code, Cursor, ChatGPT, Google Antigravity, Gemini CLI, and Codex do on their own.

**B) Bearer API key.** For clients that cannot complete a browser OAuth flow (Grok Bot, headless agents, hand-edited config files). Generate a key at https://www.socialync.io/go/github-api-keys and send `Authorization: Bearer <key>`. One key per brand, included on every plan. Never commit the key.

OAuth callbacks known to work end to end (any https redirect URI plus http://localhost and http://127.0.0.1 on any port is accepted):

- https://www.cursor.com/agents/mcp/oauth/callback
- http://localhost:8787/callback
- https://claude.ai/api/mcp/auth_callback
- http://localhost/callback
- http://127.0.0.1/callback

## Connect

**Claude (claude.ai):** Socialync is listed in Claude's connector directory. Open Settings, then Connectors, then Browse connectors, search Socialync and sign in. To add it by URL instead, choose "Add custom connector" and paste the endpoint above. Full walkthrough: [Socialync MCP connector guide](https://www.socialync.io/go/github-setup).

**Claude Code:**

```bash
claude mcp add --transport http socialync https://mcp.socialync.io/mcp
```

**Cursor / Grok Bot (OAuth, url only):**

```json
{
  "mcpServers": {
    "socialync": {
      "url": "https://mcp.socialync.io/mcp"
    }
  }
}
```

**Grok Bot or any client that cannot complete OAuth (Bearer key):**

```json
{
  "mcpServers": {
    "socialync": {
      "url": "https://mcp.socialync.io/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_SOCIALYNC_API_KEY"
      }
    }
  }
}
```

> Grok Bot runs OAuth from Cursor's cloud, not your machine. The same URL can work in the Cursor IDE and fail in Grok Bot. Test in Grok Bot under Settings, then Plugins, not only in the IDE. If OAuth never starts or you see "fetch failed", use the Bearer key form.

**Google Antigravity:** add this to `~/.gemini/config/mcp_config.json` (or `.agents/mcp_config.json` in a workspace). Antigravity uses `serverUrl` and handles the OAuth sign in itself through dynamic client registration.

```json
{
  "mcpServers": {
    "socialync": {
      "serverUrl": "https://mcp.socialync.io/mcp"
    }
  }
}
```

**Gemini CLI:** add this to `~/.gemini/settings.json`, then run `/mcp auth socialync`.

```json
{
  "mcpServers": {
    "socialync": {
      "httpUrl": "https://mcp.socialync.io/mcp"
    }
  }
}
```

**OpenAI Codex CLI:**

```bash
codex mcp add socialync --url https://mcp.socialync.io/mcp
codex mcp login socialync
```

**ChatGPT:** turn on developer mode and add a custom connector with the endpoint URL. Which ChatGPT plans can add custom connectors has changed over time, so check OpenAI's developer mode help page for yours.

**OpenClaw:**

```bash
openclaw mcp add socialync --url https://mcp.socialync.io/mcp --transport streamable-http
openclaw mcp login socialync
```

**Other MCP clients (generic config):**

```json
{
  "mcpServers": {
    "socialync": {
      "type": "streamable-http",
      "url": "https://mcp.socialync.io/mcp"
    }
  }
}
```

## Claude Code plugin

This repository is a Claude Code plugin and its own plugin marketplace. It adds the hosted server (`.mcp.json`, no secrets) and the skills below.

    /plugin install socialync --marketplace JackVitick/socialync-mcp

On Claude Code versions before 2.1.275, use two steps:

    /plugin marketplace add JackVitick/socialync-mcp
    /plugin install socialync@socialync

Sign in opens in your browser the first time a Socialync tool runs. Setup notes: [SETUP.md](SETUP.md). To skip the plugin, add the server directly (see Claude Code under Connect).

## Skills

Six skills teach an agent to use the server well. Every one of them asks you to approve each post before it goes out.

| Skill | What it does |
|---|---|
| [schedule-social-post](skills/schedule-social-post/SKILL.md) | Draft, schedule or publish to 8 platforms, with your yes on every post |
| [film-once-week-of-shorts](skills/film-once-week-of-shorts/SKILL.md) | One filming session becomes a week of TikTok, Reels and Shorts |
| [blog-to-a-week-of-posts](skills/blog-to-a-week-of-posts/SKILL.md) | One article becomes a week of LinkedIn and X posts, link in the first comment |
| [weekly-what-worked](skills/weekly-what-worked/SKILL.md) | Weekly review from your analytics, plus next week's ideas |
| [notes-to-carousel](skills/notes-to-carousel/SKILL.md) | Notes become a rendered image carousel for Instagram, LinkedIn and Threads |
| [social-queue-analytics](skills/social-queue-analytics/SKILL.md) | Check, move or cancel what is scheduled, and see why a post failed |

Install them:

- **Claude Code:** the plugin above.
- **Cursor, Codex, Windsurf, Gemini CLI and other agents:** `npx skills add JackVitick/socialync-mcp`
- **OpenClaw:** `openclaw skills install @jackvitick/social-media-scheduler`
- **Claude (web and desktop):** download a skill folder, zip it, then go to Customize, Skills, the + button, Create skill, Upload a skill. Code execution has to be on (Settings, Capabilities).

The skills are optional guidance. The server works without them.

## Cursor and Grok Bot plugin

This repository is also the Cursor plugin (`.cursor-plugin/plugin.json`, `mcp.json`, `skills/`, `assets/logo.png`). Install from the Cursor Marketplace (search "Socialync") once listed, or add the MCP URL directly as above. Skills in `skills/` tell the agent how to draft, schedule, plan a week of posts, or inspect the queue. Submission checklist: [MARKETPLACE-SUBMIT.md](MARKETPLACE-SUBMIT.md).

## Tools

Twenty-eight tools. The same list is published at https://www.socialync.io/.well-known/mcp.

| Tool | What it does |
|---|---|
| `list_profiles` | List your Socialync brand profiles (call first; every write takes a `profileId`) |
| `list_connections` | Show which social accounts are connected and healthy |
| `list_audiences` | List saved audiences a post can target |
| `list_media` | Browse media already in Socialync, with the `mediaId` used to attach video |
| `create_media_upload` / `finalize_media_upload` | Upload images or a generated video |
| `import-claude-motion-from-url` | Bring a Claude Motion video into your library, ready to post |
| `check_quota` | Plan, remaining posts, per-platform daily caps, scheduling window |
| `validate_post` | Read-only preflight: caption length, links, media, aspect ratio, video duration, connection health |
| `create_post_draft` | Draft a text or image post (you approve in the app) |
| `schedule_post_draft` | Draft a post for a future time, text, image, or video (you approve in the app) |
| `get_draft_status` | Check where a draft is in the pipeline |
| `approve_draft` | Publish a pending draft after you confirm in chat |
| `publish_now` | Publish a text or image post immediately, after you confirm in chat |
| `publish_scheduled` | Schedule a post directly, including video to TikTok and YouTube, after you confirm in chat |
| `create_x_article_draft` / `publish_x_article_now` | Long-form X Articles (X Premium account required) |
| `get_scheduled_posts` | List upcoming scheduled posts |
| `update_scheduled_post` | Change a scheduled post's time, caption, title or first comment, or drop a platform |
| `delete_scheduled_post` | Cancel a scheduled post |
| `get_post_history` | Review published post history and status |
| `get_top_posts` | Surface your best-performing content, with watch time and 3 second hold on videos |
| `get_post_insights` | One post in depth: retention curve, biggest drop, every platform side by side (interactive chart in apps-capable clients) |
| `get_comments` | Read a post's comments and replies |
| `reply_to_comment` | Reply to one comment after you confirm in chat (Instagram, Threads, LinkedIn Pages, Bluesky) |
| `get_best_times` | Your best posting windows per platform, from your own history |
| `get_analytics` | Pull follower and engagement analytics |
| `generate_content` | Generate platform-native captions with AI (paid plans) |

Text and image posts publish immediately to Instagram, Facebook, X, LinkedIn, Threads, and Bluesky. Video posts, including TikTok and YouTube (title required), go out as scheduled posts using media hosted in your Socialync library or a public video URL. A raw video file cannot be pasted into a chat; upload it in Socialync first.

Full reference, including recommended call order, per-platform limits, and error handling: [docs/tools.md](docs/tools.md).

## Analytics through MCP

Socialync collects post and account analytics for TikTok, YouTube, Instagram, Facebook, Threads, and Bluesky, and the MCP server returns the same numbers the Socialync analytics page shows (paid plans).

- `get_analytics` returns totals for views, likes, comments, shares, saves, and followers over the last week or month, the change against the previous period, an optional per-platform breakdown, and an optional daily timeseries for charts.
- `get_top_posts` returns your posts with views, likes, comments, shares, engagement rate, and a performance status (viral, good, average, flop), sorted by engagement or by date, filterable by platform and by minimum status.
- `get_post_history` returns what was posted where, with a plain reason and a retry hint for anything that failed.

Bluesky reports engagement but not views, so its posts are rated on engagement alone.

## Example workflows

Paste these into any connected agent. Naming the tool is optional; it just makes the run easier to follow. Each skill above ends with a "Try it" prompt you can paste.

**Plan the week from what worked**

> Use get_top_posts to pull my 5 best posts from the last 30 days and tell me what they have in common. Then give me 5 post ideas for this week in that style.

**Write and schedule a week of posts**

> Write each idea as a post for TikTok, Instagram, LinkedIn, and Threads, with a caption that fits each platform. Run validate_post on every one, then schedule them as drafts Monday to Friday at 6pm my time and list them for me to approve.

**Send one video everywhere**

> List my newest videos with list_media. Schedule the latest one to TikTok, Instagram Reels, and YouTube Shorts for tomorrow at 5pm. YouTube title: "How I edit a Short in 10 minutes". Show me everything before you schedule it.

**Weekly report**

> Use get_analytics for the last week with breakdown by_platform. Tell me which platform grew, which slipped, and which post did best on each.

**Check the queue**

> Show me next week's posts with get_scheduled_posts, and anything that failed this week with get_post_history.

## How the human stays in the loop

Socialync tells every connected agent to show you the exact post (platforms, text for each one, media, time) and wait for your yes in chat before it calls `publish_now`, `publish_scheduled` or `approve_draft`. Those tools are marked as destructive, so clients like Claude ask your permission before running them. The skills in this repo do the same for batches: every post shown in full and numbered, and only the numbers you approve get published. For TikTok, the agent asks who can view each video (Everyone, Friends, Followers or Only me) and never picks for you. To review in the app instead, ask for a draft: `create_post_draft` and `schedule_post_draft` save it for you to approve in Socialync, and nothing goes out until you do.

For a second check run by Socialync itself, switch on the confirmation dialog in Socialync settings. Clients that support MCP elicitation then show you the post before it publishes, and a decline stops it.

Socialync publishes the exact content you approved and logs who approved each post and when. Posts go out through accounts you connected and can disconnect at any time. There is no unattended posting mode.

## Links

- Website: [socialync.io](https://www.socialync.io)
- MCP feature overview: [socialync.io/features/mcp](https://www.socialync.io/go/github)
- Connector setup guide: [socialync.io/mcp-connector-guide](https://www.socialync.io/go/github-setup)
- Discovery document: [socialync.io/.well-known/mcp](https://www.socialync.io/.well-known/mcp)
- Official MCP Registry: `io.github.JackVitick/socialync`
- Terms of Service: [socialync.io/terms-of-service](https://www.socialync.io/terms-of-service)
- Developer Agreement (API and MCP): [socialync.io/developer-terms](https://www.socialync.io/developer-terms)
- Privacy Policy: [socialync.io/privacy-policy](https://www.socialync.io/privacy-policy)

## Support

Questions or issues: open an issue here or email [support@socialync.io](mailto:support@socialync.io).

## Follow Socialync

- X: https://x.com/Socialync_io
- Instagram: https://www.instagram.com/socialync.io/
- TikTok: https://www.tiktok.com/@socialync.io
- YouTube: https://www.youtube.com/@Socialync-io
- LinkedIn: https://www.linkedin.com/company/socialync
- Threads: https://www.threads.com/@socialync.io
- Bluesky: https://bsky.app/profile/socialync.bsky.social
