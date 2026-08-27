---
name: repurpose-longform
description: Turn a transcript, blog, or long video into platform-native social drafts in Socialync. Use when the user wants to repurpose long-form content into Instagram, TikTok, YouTube, X, LinkedIn, Threads, or Bluesky captions and queue them.
---

# Repurpose long-form into Socialync drafts

Use Socialync MCP tools. Do not post directly to social platforms.

## Setup

1. `list_profiles` for `profileId`.
2. `list_connections` so you only target connected platforms.
3. `check_quota` before generating a batch. Free accounts have 5 posts/month (one cross-post = one post) and no AI generation. Paid plans allow 10 AI-created posts per day per brand.

## How to split the work

Read the source (transcript, article, notes). Extract 1 to 5 distinct posts, not 8 clones of the same line.

For each post, write a native caption:

- **X / Bluesky:** one idea, under the hard character cap.
- **Threads:** conversational, max 5 links, under 500.
- **LinkedIn:** the point in the first 210 characters, then the rest.
- **Instagram:** first 125 characters do the work; hashtags only if the user wants them.
- **TikTok / YouTube:** video only, so these need a `mediaId` from `list_media` or a public video URL. Hook in the first line. YouTube needs a title under 60 characters plus a description.
- **Facebook:** short; long captions underperform.

If `generate_content` is available on the plan, use it for a first pass, then edit to match the user's voice from the source. On the free plan it returns a plan error: write the captions yourself.

## Queue, do not publish

1. Upload or pick media (`create_media_upload` / `finalize_media_upload` / `list_media`) when the user provided files or a public video URL. You cannot receive a video file in chat; the user uploads it in Socialync.
2. `validate_post` per post.
3. `create_post_draft` (text or images, immediate) or `schedule_post_draft` (future time, may carry one video) per post, or per cross-post group if they asked to publish the same asset everywhere.
4. Show a table: platform, caption, proposed time, quota spend.
5. Stop. The user approves drafts in their Socialync draft inbox, or confirms in chat and you call `approve_draft`.

Only use `publish_now` or `publish_scheduled` when the user explicitly asked to go live now and confirmed the exact content. Default is the draft flow, and default timing is scheduled, not publish-now.

Stagger times if they asked for a content calendar. Stay inside `maxScheduleMonths` (currently 2).

## Quota honesty

Tell them how many of the monthly 5 (or paid unlimited) this batch spends before you create drafts. One Socialync publish to 8 platforms still spends one post.
