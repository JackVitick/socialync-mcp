---
name: notes-to-carousel
description: Turn notes, a thread or an outline into a designed image carousel, render the slides locally, upload them to Socialync and schedule the carousel to Instagram, LinkedIn, Threads and Facebook with the user's approval. Use when the user asks for a carousel, slides, a swipe post or a slide deck for social, and the agent can run code to render images (Claude Code, Codex, OpenClaw and similar).
license: MIT
compatibility: Needs an agent that can run code and write image files, plus network access to upload them.
metadata:
  author: Socialync
  version: "1.0.0"
  homepage: https://www.socialync.io/go/skills-sh
---

# Notes to carousel

You write the slides, render them as images on the user's machine, upload them to Socialync, and schedule one carousel post once the user approves it.

Follow the consent rule from the `schedule-social-post` skill: show the post in full, publish only after a yes, and show it again after any change.

## Plan the slides

- 5 to 8 slides. Slide 1 is the hook and has to make someone swipe. The last slide sums up or asks for one action.
- One idea per slide, 25 words or fewer, large type.
- Show the slide text to the user as a numbered list and get their edits before rendering.

## Render

- Size 1080 x 1350 pixels (4:5), the same size for every slide. PNG or JPEG.
- Use whatever the environment has: an HTML page per slide captured with a headless browser, or an image library. Name the files `slide-01.png`, `slide-02.png` and so on.
- Check every file: text inside the frame, readable at phone size, nothing cut off. Open them or describe them to the user before uploading.

## Upload

1. `create_media_upload` with `files`: one entry per slide in order, each with `fileName`, `contentType` (for example `image/png`) and `sizeBytes`. Up to 10 images, 25 MB each.
2. For each returned `uploadUrl`, send the file's bytes with an HTTP PUT and the same `Content-Type`, for example:
   `curl -sS -X PUT -H "Content-Type: image/png" --data-binary @slide-01.png "<uploadUrl>"`
   The URLs expire after 1 hour.
3. `finalize_media_upload` with the `uploadId`. It returns one `mediaId` that carries every slide in order.

## Schedule

1. `list_profiles`, `list_connections`, `check_quota`.
2. Platforms: Instagram, LinkedIn, Threads and Facebook take the whole carousel (LinkedIn up to 9 images). X and Bluesky publish only the first 4 images, so include them only if the first 4 slides work alone. TikTok and YouTube take video only through Socialync's MCP server, so they cannot take this post.
3. Write a caption per platform. Put the shared text in `caption` and platform versions in `platformSpecificSettings`, for example `{ "linkedin": { "customCaption": "..." } }`. Hashtags or a link can go in `firstComment`.
4. Pick a time with the user and convert it to UTC for `scheduledFor`. The uploaded `mediaId` works with the scheduled tools, so to post soon, schedule it a few minutes ahead.
5. `validate_post` with the `mediaId`, platforms, captions, `firstComment` and `scheduledFor`. It checks image count and aspect ratio per platform. Fix errors and report warnings.
6. Show the post: slide list with file names, each platform's caption, first comment, local time.
7. On a yes, call `publish_scheduled` with the validated arguments, or `schedule_post_draft` if the user wants to review it in the Socialync app (drafts expire after 24 hours).
8. `get_scheduled_posts` to confirm.

## Things to avoid

- Mixing slide sizes in one carousel.
- Text smaller than about 40 pixels at 1080 wide.
- Stats on slides that the user did not give you or that you cannot trace to a source.

## Try it

> Turn these notes into a 6 slide carousel, render it, and schedule it to Instagram and LinkedIn for Thursday 11am my time. Show me the slides and captions first.
