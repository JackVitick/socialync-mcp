---
name: social-queue-analytics
description: Inspect and manage the Socialync calendar, post history, and analytics. Use when the user asks what is scheduled, what already posted, what performed, or wants to cancel or reschedule a Socialync post.
---

# Socialync queue, history, and analytics

Read-only first. Mutations only when the user asks to change the calendar.

## Always start here

1. `list_profiles`: pick `profileId`.
2. `check_quota` if the question is about remaining posts or plan limits.

## Calendar and history

- Upcoming: `get_scheduled_posts`
- Already shipped / duplicate check: `get_post_history`
- Draft still processing: `get_draft_status`

Summarize in a table (time, platforms, status, title/first line). Do not dump raw JSON.

## Analytics

- `get_analytics` for follower and engagement by platform, with period-over-period change.
- `get_top_posts` before recommending what to make next. Pattern-match the next batch off real winners, not guesses.

Analytics and top posts are paid-plan features; on the free plan tell the user that plainly.

## Changes

- Cancel a scheduled post: `delete_scheduled_post` only with explicit confirmation.
- Publish a scheduled post early: not a separate tool. Confirm with the user, then `delete_scheduled_post` and `publish_now` with the same content (text and images only; video must stay scheduled).
- Reschedule: confirm first, then `delete_scheduled_post` and `schedule_post_draft` (or `publish_scheduled` if they confirmed the exact content in chat) at the new time. Read back the calendar after.

Expired connections show up on `list_connections`. Tell the user to reconnect in Socialync settings. You cannot fix OAuth from here.

If a publish may have failed, `get_post_history` before any retry. Duplicate protection means a retry should not post twice, but history is the source of truth.
