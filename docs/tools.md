# Tool reference

Twenty-two tools exposed by the Socialync MCP server at `https://mcp.socialync.io/mcp`, over streamable HTTP, authenticated with OAuth 2.0 through your Socialync account (or a per-brand API key as a bearer token for clients without OAuth).

All write operations take a `profileId`. Get it from `list_profiles` and never assume the default, because a user may manage several brands from one account.

## Discovery

### `list_profiles`
Returns every profile this connection can manage: id, name, whether it is the default, and connected platform count. Call this first.

### `list_connections`
Connected platforms for a profile, with connection health. Check before drafting against a platform, since a disconnected account fails at publish time rather than at schedule time.

### `check_quota`
Plan, remaining post allowance, per-platform daily caps, schedule horizon in months, and the list of supported platforms. Call before every batch.

### `list_audiences`
Saved audiences: named groups of connected accounts the user built in Socialync, reusable as a posting target.

## Preflight

### `validate_post`
Runs the same checks Socialync's own scheduler enforces, per platform, without submitting anything: caption length, link count, media type and count, image aspect ratio, video duration, and account health. Call it before every draft or publish and fix what it rejects. A rejection here is faster and clearer than a rejection at the platform.

## Drafting

### `create_post_draft`
Builds a text or image draft the user approves later in the Socialync app. Use it when the user asks to save a draft or review later; immediate publishing is text and images only.

### `schedule_post_draft`
Builds a draft with a publish time, for approval in the app. This is the path for video, including TikTok and YouTube (title required): attach a `mediaId` from `list_media` or a public video URL.

### `get_draft_status`
Polls a draft that is still processing.

### `approve_draft`
Approves a pending draft on the user's behalf. Use it only after they confirm in chat; it is the user-confirmation gate before anything goes live.

### `generate_content`
AI-assisted caption drafting. Paid plans only; on the free plan write the captions yourself.

## Publishing

### `publish_now`
Immediate publish of a text or image post. There is no approval modal, so show the exact caption, platforms, and media and get an explicit yes in chat before calling.

### `publish_scheduled`
Schedules a post (text, image, or video) directly, after explicit confirmation in chat. Respect `maxScheduleMonths` from `check_quota`.

### `create_x_article_draft`
Drafts a native X Article, the long-form rich-text format with a headline, for approval in the app. X Premium is required on the connected account.

### `publish_x_article_now`
Publishes an X Article immediately, after the user has seen the headline and full body and confirmed in chat.

### `get_scheduled_posts`
The calendar. Always read back after writing so the user can see what was created.

### `delete_scheduled_post`
Cancels a scheduled post, or a whole multi-platform group, before it fires.

## Media

### `create_media_upload`
Starts a media upload for files the agent produced. Two-step process.

### `finalize_media_upload`
Completes the upload into a single `mediaId`. Media must be finalized before it can attach to a post.

### `list_media`
Assets the user already uploaded in Socialync. The normal path for video.

## Analytics

### `get_analytics`
Reach and engagement per platform, with period-over-period change.

### `get_top_posts`
Best performers. Use this to pattern-match the next batch against real numbers instead of a guess.

### `get_post_history`
What has already shipped, with per-platform success and failure. Also the correct way to check for duplicates before retrying a publish.

## Platform limits worth knowing at draft time

`validate_post` is the authority; this table is for shaping the caption before you call it.

| Platform | Text limit | Notes |
| --- | --- | --- |
| X | 280 | Threads work when each post stands alone |
| LinkedIn | 3,000 | First 210 characters show before the More link. Lower daily cap than other platforms |
| Instagram | 2,200 | First 125 shown. Media aspect ratio and type are validated by the platform |
| TikTok | 2,200 | Caption is secondary to the video hook |
| Threads | 500 | Maximum 5 links per post |
| Bluesky | 300 | Hard limit, rejected above it |
| YouTube | 5,000 description | First 157 characters serve as the search snippet. Titles under 60 avoid truncation |
| Facebook | Effectively unlimited | Engagement falls off sharply past roughly 80 characters in feed |

## Plan and rate limits

MCP and API access are included on the free plan. A user can sign up for $0 with no
credit card and publish 5 posts per calendar month across all 8 platforms, with one
brand and two months of scheduling horizon. Paid plans start at $20/month for 5
connected accounts, lift the post cap, and extend scheduling to 12 months ahead.

One publish counts as one post no matter how many platforms it targets, so five posts
can mean up to forty platform posts a month. Plan batches with that in mind: on a free
account, a five-platform cross-post spends one of five, not five of five.

One API key per brand. Up to 10 agent-created posts per day per brand on paid plans, 5
on the free plan. `generate_content` is paid-plan only. Read live values from
`check_quota` rather than hardcoding these.

Two platform guarantees worth relying on: failed posts are retried automatically, and
duplicate protection means a retry can never publish twice.

## Errors

Errors return a human-readable message describing what the platform rejected and what to do about it. Common categories:

- **Connection expired.** The user must reconnect that platform in Socialync settings. Surface this plainly rather than retrying.
- **Content rejected by the platform.** Length, link count, media type, or aspect ratio. Run `validate_post`, fix, and resubmit.
- **Account restriction.** The platform has restricted the account. Not resolvable from the API side.
- **Quota exhausted.** Read `check_quota` and reschedule the overflow.

Do not retry a failed publish without first confirming through `get_post_history` that the post did not actually go out.
