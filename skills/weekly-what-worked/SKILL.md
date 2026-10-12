---
name: weekly-what-worked
description: Run a weekly review of the user's social media from their Socialync analytics, showing which platform grew, which posts did best and worst and what they have in common, which posts failed, and where next week's calendar has gaps, then propose next week's posts. Use when the user asks what worked, how last week went, for a weekly report or recap, what to post next based on results, or "more like my best posts".
license: MIT
metadata:
  author: Socialync
  version: "1.0.0"
  homepage: https://www.socialync.io/go/skills-sh
---

# Weekly review: what worked, what to post next

The review only reads data, so it needs no approval. Anything it leads to that publishes goes through the consent rule in the `schedule-social-post` skill.

## Before you start

- `list_profiles`. If the user has several profiles, ask which one, or review each separately.
- Analytics are on paid Socialync plans. If `get_analytics` returns a plan error or no data, say so in one line and offer the parts that still work: failed posts and next week's calendar.
- Socialync collects analytics for TikTok, YouTube, Instagram, Facebook, Threads and Bluesky. X and LinkedIn have no numbers here, and Bluesky reports engagement without views. Say this when those platforms matter to the user.

## Pull the data

1. `get_analytics` with `period: "week"` and `breakdown: "by_platform"`. Use `period: "month"` if the user asks for a monthly view. The response compares against the previous period of the same length.
2. `get_top_posts` with `sort: "engagement"` and `limit: 10`. Use each post's date to keep to the period you are reviewing.
3. `get_top_posts` with `sort: "recent"` and `limit: 20` to see every recent post, including the weak ones.
4. `get_post_history` for anything that failed this week, with its reason.
5. `get_scheduled_posts` for what is already queued for the next 7 days.

## Write the review

Keep it short enough to read in a minute.

1. **The week in three lines:** total views, engagement and followers with the change against last week, and the platform that moved most in each direction.
2. **Top 3 posts:** platform, first line, views, engagement rate, status. Then what they share. Look at format (video, image, text), the type of hook, length, topic, day and time posted. Name the pattern only if at least two of the three show it.
3. **Bottom 3 posts** with the same fields. A post with status `no_data` has no numbers yet. Leave it out of this list.
4. **Failures:** each failed post with its plain reason and the fix. For `reconnect`, name the account to reconnect in Socialync Settings.
5. **Next week:** days with nothing scheduled.
6. **Three to five ideas for next week**, each tied to a top post: "Your Tuesday how-to clip got 4 times your usual views. Make another how-to on [topic]."

Every number you write comes from the tool results. If a figure is missing, write "no data".

## Turn ideas into posts

If the user picks ideas to make:

- For video, use the `film-once-week-of-shorts` skill once the clips are uploaded.
- For text, use `blog-to-a-week-of-posts` or write the posts directly.
- Either way: `validate_post`, show each post in full, get a yes, then `publish_scheduled`.

## Running it every week

The user can ask their assistant to run this review on a schedule, for example every Monday morning, if their client supports scheduled tasks. The scheduled run reads and reports only. Any posting it suggests waits until the user approves each post in a conversation.

## Try it

> Do my weekly review from Socialync: what grew, my top and bottom 3 posts and what they have in common, anything that failed, and 3 ideas for next week based on what worked.
