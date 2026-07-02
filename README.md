# Socialync MCP Server

Official documentation and registry manifest for the **Socialync hosted MCP server**. Socialync is a cross-posting and scheduling tool for creators: upload once, publish to TikTok, Instagram, YouTube, Facebook, X (Twitter), LinkedIn, Threads, and Bluesky.

This MCP server lets Claude, ChatGPT, and any MCP-compatible AI agent draft, schedule, and publish social media posts through your own OAuth-authorized Socialync account, with human-in-the-loop approval built into the workflow.

> **Note:** Socialync's server is hosted (remote) and closed source. This repository contains the documentation and the MCP Registry manifest only. There is nothing to install or run from here.

## Endpoint

```
https://socialync-mcp-ntgtgrzanq-ue.a.run.app/mcp
```

Transport: Streamable HTTP. Authentication: OAuth 2.0 with dynamic client registration. You sign in with your Socialync account in the browser; no API keys are pasted into your client.

MCP access is included on the Socialync **Premium and Business plans**. [Plans and pricing](https://www.socialync.io/pricing).

## Connect

**Claude (claude.ai):** Settings, then Connectors, then "Add custom connector", and paste the endpoint URL above. Full walkthrough: [Socialync MCP connector guide](https://www.socialync.io/mcp-connector-guide).

**Claude Code:**

```bash
claude mcp add --transport http socialync https://socialync-mcp-ntgtgrzanq-ue.a.run.app/mcp
```

**Other MCP clients (generic config):**

```json
{
  "mcpServers": {
    "socialync": {
      "type": "streamable-http",
      "url": "https://socialync-mcp-ntgtgrzanq-ue.a.run.app/mcp"
    }
  }
}
```

## What your agent can do

| Tool | What it does |
|---|---|
| `list_profiles` | List your Socialync brand profiles |
| `list_connections` | Show which social accounts are connected |
| `check_quota` | Check your remaining post quota |
| `generate_content` | Generate platform-native captions with AI |
| `create_post_draft` | Draft a post (text, image, or video) for one or more platforms |
| `schedule_post_draft` | Attach a publish time to a draft |
| `approve_draft` | Human approval step before anything goes live |
| `get_draft_status` | Check where a draft is in the pipeline |
| `publish_now` | Publish an approved draft immediately |
| `publish_scheduled` | Queue an approved draft for its scheduled time |
| `get_scheduled_posts` | List upcoming scheduled posts |
| `delete_scheduled_post` | Cancel a scheduled post |
| `get_post_history` | Review published post history and status |
| `get_top_posts` | Surface your best-performing content |
| `get_analytics` | Pull follower and engagement analytics |
| `list_media` | Browse media you have uploaded to Socialync |
| `create_media_upload` / `finalize_media_upload` | Upload images for posting |

Text and image posts publish to Instagram, Facebook, X, LinkedIn, Threads, and Bluesky. Video posts, including TikTok and YouTube (title required), are supported through scheduled posts using media hosted in your Socialync library or a public video URL.

## How the human stays in the loop

The intended workflow is draft first, publish second. Agents create drafts with `create_post_draft`; nothing reaches a social network until the draft is approved and explicitly published or scheduled. Posts go out through OAuth connections you authorized in Socialync and can be revoked at any time from your account settings. AI assistants help you draft, schedule, and publish; they do not operate accounts you have not connected and approved.

## Links

- Website: [socialync.io](https://www.socialync.io)
- MCP feature overview: [socialync.io/features/mcp](https://www.socialync.io/features/mcp)
- Connector setup guide: [socialync.io/mcp-connector-guide](https://www.socialync.io/mcp-connector-guide)
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
