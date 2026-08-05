# Tool reference

Nineteen tools exposed by the Socialync MCP server at `https://mcp.socialync.io/mcp`, over streamable HTTP, authenticated with OAuth 2.0 through your Socialync account.


All write operations take a `profileId`. Get it from `list_profiles` and never assume the default, because a user may manage several brands from one account.

## Discovery

### `list_profiles`
Returns every profile this connection can manage: id, name, whether it is the default, connected platform count, and whether the profile is eligible for AI management. Call this first.

### `list_connections`
Connected platforms for a profile, with connection health. Check before drafting against a platform, since a disconnected account fails at publish time rather than at schedule time.

### `check_quota`
Plan, remaining post allowance, per-platform daily caps, schedule horizon in months, and the list of supported platforms. Call before every batch.

### `list_audiences`
Audience segments where configured.

## Drafting

### `create_post_draft`
Builds a draft without sending it. Preferred entry point for anything an agent produces.

### `get_draft_status`
Polls a draft that is still processing.

### `approve_draft`
Marks a draft approved. Use this as the user-confirmation gate before anything goes live.

### `generate_content`
AI-assisted drafting where the plan is available.

## Publishing

### `schedule_post_draft`
Places an approved draft on the calendar. Respect `maxScheduleMonths` from `check_quota`.

### `publish_now`
Immediate publish. Use only with explicit user confirmation.

### `publish_scheduled`
Fires an already-scheduled post early.

### `get_scheduled_posts`
The calendar. Always read back after writing so the user can see what was created.

### `delete_scheduled_post`
Reverses a scheduled post before it fires.

## Media

### `create_media_upload`
Starts a media upload. Two-step process.

### `finalize_media_upload`
Completes the upload. Media must be finalized before it can attach to a post.

### `list_media`
Assets already uploaded.

## Analytics

### `get_analytics`
Reach and engagement per platform.

### `get_top_posts`
Best performers. Use this to pattern-match the next batch against real numbers instead of a guess.

### `get_post_history`
What has already shipped. Also the correct way to check for duplicates before retrying a publish.

## Platform limits worth knowing at draft time

Validate against these before submitting, because a rejection at the platform is slower and less clear than a check in your own code.

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

## Errors

Errors return a human-readable message describing what the platform rejected and what to do about it. Common categories:

- **Connection expired.** The user must reconnect that platform in Socialync settings. Surface this plainly rather than retrying.
- **Content rejected by the platform.** Length, link count, media type, or aspect ratio. Fix and resubmit.
- **Account restriction.** The platform has restricted the account. Not resolvable from the API side.
- **Quota exhausted.** Read `check_quota` and reschedule the overflow.

Do not retry a failed publish without first confirming through `get_post_history` that the post did not actually go out.
