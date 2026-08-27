---
name: schedule-social-post
description: Draft, approve, schedule, or publish a social post through Socialync. Use when the user wants to post, cross-post, schedule content, or publish now to Instagram, TikTok, YouTube, Facebook, X, LinkedIn, Threads, or Bluesky.
---

# Schedule or publish a Socialync post

Use the Socialync MCP server. Do not scrape social sites or open platform web apps to post.

## Auth and first calls

The server is hosted at `https://mcp.socialync.io/mcp`. Auth is OAuth in the browser (Cursor, Claude, ChatGPT) or a Bearer API key from https://www.socialync.io/api-keys for clients that cannot open a browser (Grok Bot). If tools fail with auth errors, tell the user to reconnect Socialync in their client's MCP settings.

Before drafting:

1. `list_profiles`: every write takes `profileId`. Never assume the default.
2. `list_connections` on that profile: skip disconnected platforms and tell the user to reconnect in Socialync settings.
3. `check_quota`: plan, remaining posts, per-platform daily caps, `maxScheduleMonths`. Call this before every batch.
4. `validate_post` with the exact arguments you are about to submit. It cannot post anything; it tells you what the platform would reject.

## Limits

- Free plan: 5 posts per calendar month, no credit card, MCP included. Paid plans start at $20/mo for unlimited posts and 5 connected accounts.
- One publish counts as one post no matter how many platforms it targets.
- 10 AI-created posts per day per brand on paid plans, 5 on free.
- Read live numbers from `check_quota`. Do not hardcode.

Platform text limits (reject before submit):

| Platform | Limit | Notes |
|---|---|---|
| X | 280 | Each post must stand alone |
| Bluesky | 300 | Hard reject above this |
| Threads | 500 | Max 5 links, enforced |
| LinkedIn | 3,000 | First 210 characters show |
| Instagram | 2,200 | First 125 shown |
| TikTok | 2,200 | Video only. Caption is secondary to the hook |
| YouTube | 5,000 description | Video only. Title required; keep titles under 60 |
| Facebook | effectively unlimited | Engagement drops after ~80 characters in feed |

Text and image posts publish immediately to Instagram, Facebook, X, LinkedIn, Threads, and Bluesky. Video (including TikTok and YouTube) goes out as a scheduled post using media already in the Socialync library (`mediaId` from `list_media`) or a public video URL. YouTube needs a title. You cannot receive a video file in chat: tell the user to upload it in Socialync, then attach by `mediaId`.

## Two ways to go live, both need the user's consent

**Draft flow (default).** `create_post_draft` (immediate, text or images) or `schedule_post_draft` (future time, may carry one video). The draft lands in the user's Socialync draft inbox and they approve it there. Nothing is live until they do. If they confirm in chat instead, `approve_draft` publishes the pending draft for them.

**Direct flow.** `publish_now` (text or images, immediately) or `publish_scheduled` (any media, future time). Only after you have shown the exact caption, platforms, media, and time and the user said yes in chat.

Workflow:

1. If there is local image media, `create_media_upload` then `finalize_media_upload` before attaching. For existing assets, `list_media`.
2. `validate_post`, fix anything it flags.
3. Show the user the post: platform-native captions, not one caption copied everywhere.
4. Default to the draft flow. Use the direct flow only when the user explicitly asked to post or schedule right now and confirmed the exact content.
5. Read back with `get_scheduled_posts` or `get_post_history` so they can see what was created.

Never call `publish_now`, `publish_scheduled`, or `approve_draft` unless the user clearly asked to go live. If they said "draft" or "queue", stop after the draft tool.

## Errors

- Connection expired: tell them to reconnect that platform in Socialync. Do not retry.
- Content rejected: fix length, links, or media and resubmit.
- Quota exhausted: read `check_quota` and reschedule overflow.
- Before retrying a failed publish, `get_post_history` so you do not double-post. Duplicate protection exists, but check anyway.

If the user is on the free plan and this publish would exceed 5 posts, say so and point them at https://www.socialync.io/pricing. Do not nag. One sentence.
