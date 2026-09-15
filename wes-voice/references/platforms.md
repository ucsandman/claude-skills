# Platform fit: what each feed ranks, and how that shapes a draft

Read this after samples.md and stories.md whenever the piece is a public post. Voice comes from the samples. Shape comes from here. Each section carries its research date and sources so it can be checked. When the target platform's section is older than 60 days, or the platform has no section, refresh first with the query at the bottom and update this file in the same turn.

## X (researched 2026-09-06)

Sources: X open-sourced the Phoenix ranker with its weights on 2026-08-13 (Business Insider 2026-08-14; Postory 2026-09-05 code walkthrough; Joe Youngblood 2026-08-15; TechCrunch 2026-08-13). Links: Nikita Bier and Elon Musk on X, 2026-07-28 (covered by AdTechRadar 2026-07-28, Free Press Journal 2026-07-29); Musk 2025-10 via Social Media Today 2025-10-19.

What the ranker does. One Grok-architecture transformer predicts about 15 actions per post and sums weight times probability. Published weights, relative to a predicted like at 0.5: copy-link share about 40 likes, reply, quote and DM share about 10 each, follow-from-feed about 16, repost about 4. Negatives are far larger: predicted report about 468 likes the wrong way, mute 118, "not interested" 86, block 62. Grok reads the post text and the video by meaning, not just engagement. Reply quality is scored 0 to 3 and replies the author answers rank higher. Video earns its quality-view weight only above a minimum duration. Links: there is no hard-coded link penalty in the open code, and X's head of product Nikita Bier said publicly on 2026-07-28 (replying to Zuckerberg, Musk confirming) that links "no longer need to go in replies", the penalty ended more than a year earlier. Musk's stated rule, 2025-10: a bare link with almost no description gets weak distribution, a link with a real description and an image or video gets distribution. The old drop in reach came from the in-app browser covering the post, which X changed in mid-2025. Trade blogs in Aug 2026 still repeat "links penalized"; that is 2023 lore, not the current code or the product head's statement. So: a link in the body is fine when the post carries native media and a full description. A link with one line of text is still a bad post.

How a draft changes:
- Write it to be sent to one specific person. The copy-link share is the single most valuable action, so the post has to be worth forwarding to "the guy who runs a shop out of his truck", not worth liking.
- The text describes exactly what the attached clip shows. Grok compares them.
- End on a real question the target reader can answer from their own life. Replies are 20x a like and Wes answering them multiplies it again. Answer every reply inside the first hour with a full sentence, never "thanks".
- The link can stay in the body. Put the build detail (stack, counts, how it works) in a first reply posted right after, which is also the reply the author has already engaged with.
- Nothing that could read as ragebait, hype or a category the reader would mark "not interested". One predicted "not interested" erases 170 likes.
- Native video or image always, never a link to a video. A clip under the duration threshold earns no video weight, so use the full social cut, not a 5 second loop.
- No hashtags. Post-Phoenix the ranker categorizes by reading the post text semantically; hashtags add zero reach and independent breakdowns rate them neutral-to-negative, with hashtag-heavy posts pattern-matching spam accounts (Postory 2026-09-01, Teract 2026-07, X-Autopilot 2026-09-02, refreshed 2026-09-10). Their only use is on-platform search, which Wes's audience doesn't arrive by. Write the topic words into the sentence instead.

## LinkedIn (researched 2026-09-06)

Sources: SocialPilot 2026-08-07, Stackmatix 2026-08-20, Sales & Marketing Engineers 2026-08-13, Piyush Mantri 2026-08-09, Melanie Goodman 2026-08-28 (summarizing LinkedIn's own published ranking note: "Long Dwell" and "Contribution" are the two heaviest actions).

What the ranker does. Long dwell is the top signal, the target is 31 to 60 seconds of reading, and a "see more" click counts. Comment quality and conversation depth beat likes; one-word comments and "comment YES" bait are classified and halve reach. The first hour of real discussion decides second and third degree distribution. Personal profiles out-reach company pages by about 70 percent. External links in the body are deprioritized. Replying to comments inside 30 minutes lifts total comments. Keep 12 or more hours between posts.

How a draft changes:
- Two short lines before the fold that make the reader click "see more". The event and the result, nothing else.
- The body is long enough to hold 30 plus seconds: what happened, what broke, what the fix was, what Wes actually believes. Every paragraph earns its place with a concrete detail. No bullet walls, no arrows.
- No link in the body. Name the product in plain words and put the URL in the first comment, posted immediately.
- End on a question a specific reader can answer with a sentence, not a yes.
- Wes replies to every comment in the first hour with substance.
- For a complex idea, lead with one clean infographic that turns it into a single visual. In the source playbook this was the single biggest reach lever (a 4K to 300K impressions jump in 6 months, reposted by execs and AI consultants). Use the epic-infographics / marketing-studio assets, not a text wall, when the point is a concept. (added 2026-09-07, source: r/AI_Agents laid-off-growth-marketer playbook, 1w9rja4.)
- Discovery is comment-led, not just reply-led. Commenting 4 to 5 substantive times a day on the ICP's own posts pulls them to Wes's profile faster than posting alone does. Do this the week before and during a launch, not only on replies to Wes's posts. (added 2026-09-07, same source.)

## Reddit, r/SideProject and similar builder subs (researched 2026-09-06)

Sources: GrowReddit 2026-07-04, RedditMaster r/SideProject rules page, GoGlobal 2026-05-14. Direct rules fetch is blocked to bots; re-check the sidebar in a browser before posting somewhere new.

What gets through. Commercial projects are fine when the post is the story and the building process, not the pitch. Removed: "check this out" posts, resold or affiliate products, reposts, upvote asks. Reciprocity is the contract there, comment on other people's projects the same day. Account age, karma and cross-sub activity gates exist on most builder subs; a pre-post modmail asking where the product fits often gets whitelisted.

How a draft changes:
- Title is the build story with a real timespan or number, never the product name alone.
- Body walks the decisions and what broke, with the product as the supporting detail. One link line at the end.
- Ask for what's missing, not for upvotes or signups.
- Wes comments on three other projects before posting and answers every comment.

## Reaching a non-technical buyer (trades, local service, "normies"; researched 2026-09-06)

Use this when the product's real buyer is not on X/LinkedIn/HN/builder-Reddit: a trades owner, a local service business, anyone who has the problem and the money but never reads a launch thread. Sources: r/b2bmarketing 2026-05-26, r/DigitalMarketing, mrtask.com 2026-06-21, OneUp self-promo DB 2026-07-13. Basis: @thekitze 2026-09-06, "normies are clueless and would gladly pay; indiehackers are hard to sell to."

Where they are, ranked: local trade Facebook groups and regional associations (highest trust); Google Business Profile and Nextdoor for local search; r/smallbusiness software/systems threads (the one general sub where an owner evaluating tools is on-topic); direct local outreach. Not the trade pro subs, which ban outside posts (r/HVAC routes outsiders to r/hvacadvice, homeowner Q&A only), and not builder subs for selling.

How a draft changes:
- Sell relief, not tech. "The call got answered and the quote went out while you were on the job" beats "AI back office". Say it the way the owner would say it on the phone.
- On r/smallbusiness or a trades group, lead with the shop's problem and ask for their reality. A feedback post ("is the missed call your biggest leak, or is it X?") is on-topic; a launch pitch gets removed. No link in the post, demo link in a comment only if asked.
- 58% of subs ban self-promo or gate it 9:1 (OneUp DB). Check the exact sub's sidebar before posting and comment in the community first.
- The highest-conversion move is usually not a broadcast post at all: a helpful comment in an existing "what software do you use" thread, or direct outreach with a demo link, beats any launch.

## Hacker News (researched 2026-09-14)

Sources: HN's own Show HN guidelines (news.ycombinator.com/showhn.html) as quoted by Favors.dev 2026-08-03; daily.dev Ads 2026-06-03; Flowjam 2026-06-22.

What gets through. A Show HN is only for "things people can run on their computers or hold in their hands". A blog post, a waitlist, a landing page with no product behind it, a newsletter or a point release gets flagged. Two conditions catch people out: the project has to be yours, and you have to be present to discuss it. The guidelines also ask you to lower the barrier as far as it goes, which is why the email wall is the most complained-about thing in Show HN threads. The non-trivial rule is the one that aged hardest in the AI era: readers apply it before they finish the title, so the post has to say how and why it was built, personally. Front page needs roughly 30 to 50 upvotes in the first hour (daily.dev 2026-06), and there is no way to buy position, no scheduling and no hunter.

How a draft changes:
- Title is boring and specific, with a digit, a version or a time box. "Show HN: I cut my AWS bill 82% with a 200-line Lambda" beats "How I saved money on AWS" (Flowjam 2026-06). No superlatives, no adjectives, nothing that reads like an ad.
- The link goes straight to the thing a reader can run, not to a marketing page.
- The first comment is the real launch post, written before posting: who Wes is, one sentence of what it does, the problem, why he built it, the technical part, what is different, then the ask for feedback.
- Name the ugly thing yourself in the first comment. An unsigned binary, a missing platform, a known false positive: HN finds it in ten minutes, and stating it first reads as honesty while omitting it reads as hiding.
- No email wall, no sign-up gate between the link and the working product.
- Answer every comment fast and technically, in the first hour. Response speed matters more than post time. Never ask for votes, anywhere, including other channels.

## Refresh procedure

Run these through web_search, read two current sources per platform, then rewrite that platform's section with today's date:
- "X algorithm ranking signals what works <month year>"
- "LinkedIn algorithm <month year> what's working dwell time comments"
- "r/<sub> rules self promotion <year>" plus the sub's sidebar in a browser
- "Show HN what works <year>" for HN
Keep the "How a draft changes" list tied to a named signal. A rule with no signal behind it gets deleted.
