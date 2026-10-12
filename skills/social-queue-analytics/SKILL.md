---
name: social-queue-analytics
description: Inspect and manage the Socialync calendar and post history. Use when the user asks what is scheduled, what already posted, why a post failed, or wants to cancel, move or publish early a scheduled Socialync post. For a weekly performance review or "what worked", use the weekly-what-worked skill.
license: MIT
metadata:
  author: Socialync
  version: "1.1.0"
  homepage: https://www.socialync.io/go/skills-sh
---

# Socialync queue and history

Reading needs no approval. Any change to the calendar follows the consent rule in the `schedule-social-post` skill: show exactly what will change and wait for a yes.

## Start here

1. `list_profiles` to pick the `profileId`.
2. `check_quota` if the question is about remaining posts or plan limits.

## Read

- Upcoming and recently failed: `get_scheduled_posts`. A failed post has `error.code`, a plain `message` to relay, and `action`.
- Already published, or a check before any retry: `get_post_history`.
- A draft still waiting: `get_draft_status`.
- Performance numbers: `get_analytics` and `get_top_posts` (paid plans). For a full review, switch to `weekly-what-worked`.

Summarize as a table: local time, platforms, status, first line of text. Leave out raw JSON.

## Change

- **Cancel:** show the post and ask. On a yes, `delete_scheduled_post` with `groupId` to cancel every platform of that post, or `postId` for one platform. A published post cannot be removed.
- **Move to another time:** there is no edit tool. Show the new time and ask. On a yes, `validate_post` with the new `scheduledFor`, then `delete_scheduled_post`, then `publish_scheduled` with the same content at the new time. For a video, confirm the clip still appears in `list_media` before you cancel, and reschedule straight away.
- **Publish early:** for text or images, the same steps with `publish_now` in place of `publish_scheduled`. A video stays on the scheduled path.
- Read back with `get_scheduled_posts` after every change.

## Failures

- `action: "reconnect"`: tell the user which account to reconnect in Socialync Settings. You cannot fix a connection from here. Do not reschedule until they have reconnected.
- `edit_post`: show what needs to change, then follow the move steps with the fixed content.
- `retry` or `wait_and_retry`: offer a new time.
- Before any retry, check `get_post_history` so you report what actually happened. Socialync blocks duplicate publishes.
