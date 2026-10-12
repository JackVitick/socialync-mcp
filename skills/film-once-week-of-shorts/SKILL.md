---
name: film-once-week-of-shorts
description: Turn one filming session into a week of short-form video on TikTok, Instagram Reels and YouTube Shorts, scheduled through Socialync with the user's approval on every post. Use when the user has batch-filmed several clips and wants them spread across the week, or says "schedule my videos for the week", "post one clip a day", "TikTok, Reels and Shorts", or "film once, post all week".
license: MIT
metadata:
  author: Socialync
  version: "1.0.0"
  homepage: https://www.socialync.io/features/mcp
---

# Film once, schedule a week of TikTok, Reels and Shorts

The user films 5 to 7 clips in one sitting. You write a caption for each platform, give each clip a day and a time, and schedule them through Socialync once the user approves each post. One clip goes to TikTok, Instagram and YouTube in a single post, so it costs one post of the user's allowance and one of the day's slots.

Follow the consent rule from the `schedule-social-post` skill on every post. In short: show each post in full, publish only what the user approved, and show it again after any change.

## What you need from the user

- The clips, uploaded to Socialync. You cannot receive video files in chat. Ask them to open socialync.io (or the Socialync app), start a post, add the videos, and leave the page. Uploads not attached to a post are deleted about 12 hours later, so do this in one session.
- Which days and what time of day. If they have no preference, suggest one clip a day at the same local time and ask.
- Their timezone, if you do not know it.
- One line about each clip, or let them tell you the topic of the batch. You cannot watch the video, so never describe what is in it beyond what the user told you or what the file name says.

## Steps

1. `list_profiles`, then `list_connections`. You need TikTok, Instagram and YouTube connected and healthy on the chosen profile. Leave out any that are missing and say which.
2. `check_quota`.
   - Free plan: `postsRemaining` is the most clips you can schedule this month. If it is less than the batch, say so and schedule that many. Free plans schedule up to 2 months ahead.
   - Each day takes at most 10 posts per profile on paid plans and 5 on free. One clip a day is far below that.
3. `list_media`. Match the clips by file name and upload time. Show the list and confirm which clip goes on which day before writing anything.
4. Write for each clip:
   - **TikTok:** first line is the hook. 1 to 3 short sentences. 3 to 5 hashtags at the end if the user uses hashtags.
   - **Instagram:** the first 125 characters carry the hook. Hashtags at the end or in `firstComment`.
   - **YouTube:** a `title` under 60 characters that says what the viewer gets, plus a short description. YouTube requires the title.
   - Put the shared text in `caption`, and each platform's version in `platformSpecificSettings`, for example `{ "tiktok": { "customCaption": "..." }, "instagram": { "customCaption": "..." }, "youtube": { "customCaption": "..." } }`.
5. For each clip, call `validate_post` with `platforms: ["tiktok", "instagram", "youtube"]`, the `mediaId`, `title`, `caption`, `platformSpecificSettings` and `scheduledFor` (UTC). Fix every error. Report every warning.
6. Show the week as numbered posts in the format from `schedule-social-post`, with the clip name, day and local time on each. Add one line at the top asking who can view the TikToks (Everyone, Friends, Followers or Only me; one answer covers the week, passed as `tiktokPrivacyLevel`), and say that TikTok sounds cannot be added through the API, so the audio in each clip is what plays.
7. Ask: "Reply with the numbers to schedule, or 'all'." Wait for the answer.
8. For each approved post, call `publish_scheduled` with the exact validated arguments. If the user asked to review in the Socialync app instead, call `schedule_post_draft` and tell them the drafts expire in 24 hours.
9. Call `get_scheduled_posts` and show the week as a short table: day, time, clip, platforms, status.

## Options the user can ask for

- **Instagram Trial Reels:** add `instagramTrialReel: "MANUAL"` or `"AUTO"` to show a clip to non-followers first. MANUAL stays a trial until the user shares it in the Instagram app, which cannot be done through the API. AUTO lets Instagram share it with followers if it performs well. Only offer this when the user asks about trial reels.
- **More platforms:** Facebook, Threads, X and Bluesky also take video. `validate_post` checks the video's duration against each platform. Drop any platform it flags and tell the user why.
- **A video hosted elsewhere:** a direct public https link to the video file works as `videoUrl`. Socialync copies it when you call the tool. A share page link (for example a Drive preview page) is not a file link and will fail.

## Things to avoid

- Two clips on the same day for the same profile unless the user asks for it.
- Captions that describe footage you have not been told about.
- Promising a trending sound or a TikTok draft. Neither is available through MCP.

## Try it

> I just uploaded 5 clips to Socialync. Schedule one a day Monday to Friday at 6pm my time to TikTok, Reels and Shorts. They are all about editing short videos on a phone. Show me everything before you schedule.
