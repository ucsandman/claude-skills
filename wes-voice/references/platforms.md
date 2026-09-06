# Platform fit: what each feed ranks, and how that shapes a draft

Read this after samples.md and stories.md whenever the piece is a public post. Voice comes from the samples. Shape comes from here. Each section carries its research date and sources so it can be checked. When the target platform's section is older than 60 days, or the platform has no section, refresh first with the query at the bottom and update this file in the same turn.

## X (researched 2026-09-06)

Sources: X open-sourced the Phoenix ranker with its weights on 2026-08-13 (Business Insider 2026-08-14; Postory 2026-09-05 code walkthrough; Joe Youngblood 2026-08-15).

What the ranker does. One Grok-architecture transformer predicts about 15 actions per post and sums weight times probability. Published weights, relative to a predicted like at 0.5: copy-link share about 40 likes, reply, quote and DM share about 10 each, follow-from-feed about 16, repost about 4. Negatives are far larger: predicted report about 468 likes the wrong way, mute 118, "not interested" 86, block 62. Grok reads the post text and the video by meaning, not just engagement. Reply quality is scored 0 to 3 and replies the author answers rank higher. Video earns its quality-view weight only above a minimum duration. There is no hard-coded link penalty in the open code; the 2023 "links kill reach" advice is unverified now.

How a draft changes:
- Write it to be sent to one specific person. The copy-link share is the single most valuable action, so the post has to be worth forwarding to "the guy who runs a shop out of his truck", not worth liking.
- The text describes exactly what the attached clip shows. Grok compares them.
- End on a real question the target reader can answer from their own life. Replies are 20x a like and Wes answering them multiplies it again. Answer every reply inside the first hour with a full sentence, never "thanks".
- The link can stay in the body. Put the build detail (stack, counts, how it works) in a first reply posted right after, which is also the reply the author has already engaged with.
- Nothing that could read as ragebait, hype or a category the reader would mark "not interested". One predicted "not interested" erases 170 likes.
- Native video or image always, never a link to a video. A clip under the duration threshold earns no video weight, so use the full social cut, not a 5 second loop.
- Hashtags only on launch and demo posts, one or two, matching Wes's own habit.

## LinkedIn (researched 2026-09-06)

Sources: SocialPilot 2026-08-07, Stackmatix 2026-08-20, Sales & Marketing Engineers 2026-08-13, Piyush Mantri 2026-08-09, Melanie Goodman 2026-08-28 (summarizing LinkedIn's own published ranking note: "Long Dwell" and "Contribution" are the two heaviest actions).

What the ranker does. Long dwell is the top signal, the target is 31 to 60 seconds of reading, and a "see more" click counts. Comment quality and conversation depth beat likes; one-word comments and "comment YES" bait are classified and halve reach. The first hour of real discussion decides second and third degree distribution. Personal profiles out-reach company pages by about 70 percent. External links in the body are deprioritized. Replying to comments inside 30 minutes lifts total comments. Keep 12 or more hours between posts.

How a draft changes:
- Two short lines before the fold that make the reader click "see more". The event and the result, nothing else.
- The body is long enough to hold 30 plus seconds: what happened, what broke, what the fix was, what Wes actually believes. Every paragraph earns its place with a concrete detail. No bullet walls, no arrows.
- No link in the body. Name the product in plain words and put the URL in the first comment, posted immediately.
- End on a question a specific reader can answer with a sentence, not a yes.
- Wes replies to every comment in the first hour with substance.

## Reddit, r/SideProject and similar builder subs (researched 2026-09-06)

Sources: GrowReddit 2026-07-04, RedditMaster r/SideProject rules page, GoGlobal 2026-05-14. Direct rules fetch is blocked to bots; re-check the sidebar in a browser before posting somewhere new.

What gets through. Commercial projects are fine when the post is the story and the building process, not the pitch. Removed: "check this out" posts, resold or affiliate products, reposts, upvote asks. Reciprocity is the contract there, comment on other people's projects the same day. Account age, karma and cross-sub activity gates exist on most builder subs; a pre-post modmail asking where the product fits often gets whitelisted.

How a draft changes:
- Title is the build story with a real timespan or number, never the product name alone.
- Body walks the decisions and what broke, with the product as the supporting detail. One link line at the end.
- Ask for what's missing, not for upvotes or signups.
- Wes comments on three other projects before posting and answers every comment.

## Hacker News (from the launch skill's virality notes, 2026-07; not refreshed)

"Show HN:" prefix, link straight to the product or repo, plain specific title without superlatives. Structure: who you are, one sentence what, the problem, origin, the technical part, what's different, ask for feedback. The comments are the launch, answer everything fast and technically. Never solicit votes. Timing matters less than response speed.

## Refresh procedure

Run these through web_search, read two current sources per platform, then rewrite that platform's section with today's date:
- "X algorithm ranking signals what works <month year>"
- "LinkedIn algorithm <month year> what's working dwell time comments"
- "r/<sub> rules self promotion <year>" plus the sub's sidebar in a browser
- "Show HN what works <year>" for HN
Keep the "How a draft changes" list tied to a named signal. A rule with no signal behind it gets deleted.
