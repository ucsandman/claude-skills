---
name: xplainer
description: Turn any problem — a bug, a root cause, an architecture decision, a new subsystem — into a short narrated explainer video with captions, rendered to a single MP4. Use when asked to "explain this as a video", "make an explainer", "turn this into a video", or when a fix is subtle enough that the team will understand it faster from ninety seconds of animation than from a diff.
---

# xplainer

Build an explainer video: animated scenes, a synthesised voiceover, and
word-level captions, rendered together into one MP4.

The bar is comprehension. Someone who watches the output should be able to say
what broke, why it broke, and what the fix does — without opening the diff.
That bar is met by **showing the mechanism**, not by narrating a slide deck.

## One tool set, and it runs on this machine

The eight tools are served by a runtime on the user's own machine. Nothing is
uploaded and nothing renders anywhere else: the workspace, the speech service
and every frame stay where this session already is.

**Prefer the local tools when they are present.** If this session has an
xplainer server registered, call it: it renders on the machine the user is
already sitting at, it has no quota, and the finished MP4 lands somewhere they
can open. If none is registered, say so and stop — there is no second route to
reach for.

Rendering uses Remotion. Depending on the size of the user's company and how
they use it, they may need their own Remotion licence — see
<https://remotion.pro/license>.

## Nothing to install

Remotion, React and Tailwind are already installed in the workspace the runtime
owns. You never run `npm install`, never scaffold a project, and never touch
`node_modules`. Everything goes through the tools:

| Tool | Does |
|---|---|
| `explainer_create(slug)` | Makes the video and scaffolds its files |
| `explainer_put_source(slug, files)` | Writes your Remotion source files into the video |
| `explainer_put_media(slug, name, base64)` | Uploads one image, audio or video asset |
| `explainer_narrate(slug, narration)` | Voiceover + captions + timings |
| `explainer_still(slug, frame)` | One frame, for a layout check |
| `explainer_render(slug)` | Renders the MP4 |
| `explainer_job(job_id)` | Progress of any of the queued three |
| `explainer_list()` | What videos exist and their state |

Narration, stills and renders are queued, so those three tools return a `job_id`
immediately — poll `explainer_job` rather than waiting.

`explainer_create` scaffolds six files. **Five belong to the engine and you
cannot write them:** `index.ts`, `types.ts`, `Root.tsx`, `Captions.tsx` and
`Video.tsx`. `Video.tsx` is the composition shell — it mounts the voiceover,
mounts the caption track, and lays out one `<Sequence>` per narration segment at
the measured position and length.

This is enforced, not requested. A write naming any of those five is **refused**,
the whole call fails, and nothing at all is written — not even the files in the
same call that were fine. A render restores an engine file that was altered by
any other route. The reason is blunt: two of the lines in that shell are the
soundtrack and the captions, and a video that lost them still renders, still
exits clean, and ships silent.

**The sixth file, `Scenes.tsx`, is yours**, together with every component it
imports — by convention under `scenes/` — and any helper, hook or data file you
want beside them. Nothing about what a scene may contain is constrained: a scene
is ordinary React and you can draw anything. `Scenes.tsx` is scaffolded once, as
an empty map, and never overwritten, so `explainer_create` stays safe to re-run.

Your files reach the runtime through `explainer_put_source`, one call carrying
every file you changed. Paths are relative to the video, so a scene component
goes to `scenes/Symptom.tsx` and the map naming it goes to `Scenes.tsx`. Only
those five exact top-level names are reserved — `scenes/Root.tsx` is yours.
`explainer_create` also returns a `write_source_to` path, and you may write the
files there directly instead; the same five names are off limits there too.

## The one mechanic that isn't negotiable

`explainer_narrate` synthesises the voiceover and gets back **the timestamp of
every spoken word**. Every scene length, and every caption, comes from those
measurements, written to `timings.json`.

Guess durations instead and the picture drifts from the voice a few scenes in.
So: **write the narration, narrate it, then build the picture against
`timings.json`.** Never hand-write a scene duration.

You never write a scene's position or its length, because you never write the
`<Sequence>`. `Scenes.tsx` maps each narration segment id to the component that
draws it, and the engine reads `from` and `durationInFrames` straight off the
measured segment:

```tsx
import type { SceneMap } from "./Video";
import { SymptomScene } from "./scenes/Symptom";
import { CauseScene } from "./scenes/Cause";

export const scenes: SceneMap = {
  symptom: SymptomScene,
  cause: CauseScene,
};
```

Keep `import type` on that first line. As a value import it creates a real module
cycle at runtime and your scenes can come back `undefined`.

Each scene is handed `{ segment, timings, captions }`. Mismatches are loud in
both directions: a key naming no narration segment fails the render and lists the
valid ids, and a segment with no key draws a visible "no scene" placeholder — so
building one scene at a time works, and reordering the narration reshuffles the
picture correctly on its own.

Two optional exports draw across the whole video if you want them: `Backdrop`,
behind every scene, and `Overlay`, above the scenes and below the captions.

Inside a scene, `useCurrentFrame()` restarts at zero, so scene animations are
written relative to the scene. To land a beat on a specific spoken word, read
`captions.json` — every word carries real `startMs`/`endMs`.

## Workflow

1. **Get the explanation right.** Read the code, the diff, the logs. You need
   the real root cause; an explainer built on a guess is worse than none,
   because it is confidently wrong and now memorable. Land on: the symptom
   someone noticed, what actually caused it, why it was non-obvious, what the
   fix changes. If there were three causes, that *is* the story.
2. **Decide the visual direction** before writing components (below).
3. **Storyboard as motion** — what moves, and what that movement means.
4. `explainer_create`, then write the narration and `explainer_narrate`.
5. **Design your scenes** against `timings.json` — one entry in `Scenes.tsx`
   per narration segment id, plus the components it imports — and send them all
   with `explainer_put_source`.
6. `explainer_still` and look at it, then `explainer_render`.

If the caller handed you screenshots, recordings, or diagrams, use them — real
footage of the bug beats any abstraction you can draw. Lead with it. Send each
file with `explainer_put_media` and reference it as `staticFile("media/name.png")`;
`explainer_create` returns the matching `put_media_in` path, so you may also
drop the files there yourself.

## Show the mechanism, don't list it

The default failure mode is a deck: heading, three bullets, cut to next heading.
That transfers words, not understanding. People understand a bug when they watch
the wrong thing happen.

Before writing any scene, ask: *what would this look like if it were moving?*

| When the point is… | Show |
|---|---|
| a sequence of events | a timeline that plays — events landing one by one, the late one arriving after the boundary that was supposed to close things out |
| state that got stuck | the state machine itself, a token moving through it, and stalling where it stalled |
| a race or an ordering bug | two lanes against one clock, drifting out of order |
| data taking the wrong path | the payload travelling between boxes and visibly branching wrong |
| a value that should have changed | the value on screen, changing — or conspicuously not |
| specific code | the excerpt held still, with emphasis moving to the line under discussion |
| before and after | one diagram transforming in place, not two static cards side by side |
| scale, cost, or volume | quantity as quantity: bars growing, a counter running, a queue filling |

Working rules:

- **One mechanism per scene.** If a scene needs "and also", it is two scenes.
- **Hold after motion settles.** Give each state about a second of stillness to
  be read. Animation that never rests is harder to follow than a static card.
- **Movement must mean something.** If an element moves, the movement is the
  point. Ambient drift, floating particles, and things sliding in for no reason
  read as generated filler.
- **Reserve a slot for every element.** Lay scenes out with flex or grid and
  fade elements in where they already sit. Never let a late arrival shove an
  earlier one sideways.
- **Voice and picture do different jobs.** The animation shows *what happens*;
  the narration explains *why it happens*. If the voice is reading the words on
  screen, delete one of them.
- **Text is the fallback, not the default.** A text card is right for a hard
  claim you want to land — a takeaway, a name, a number. It is wrong as the
  representation of a process.

## Visual direction

Design it for this subject, the way a studio would. Do not reach for a generic
"tech explainer" look.

Fix the direction before building. Write down, in a few lines:

- **Palette** — 4–6 named hex values. Derive them from the subject's own world
  where there is one: the product's real UI, the terminal it runs in, the
  colours the logs already use for pass and fail.
- **Type** — a display face with personality used with restraint, plus a body
  face that stays readable in motion, plus a mono face if code appears. Set a
  real scale; do not let one weight do every job.
- **Motion signature** — the one movement idea repeated throughout. Everything
  easing on the same curve, a single accent that travels between scenes, a
  consistent way new information arrives.
- **Signature element** — the one thing this video is remembered by. Spend your
  boldness here and keep everything around it quiet.

Then check the plan against the brief: if any part of it is what you would have
produced for *any* engineering explainer, change that part.

Currently over-used and worth avoiding unless the brief actually asks: cream
background with a high-contrast serif and a terracotta accent; near-black with
one acid-green accent; glassmorphic cards floating on a gradient mesh; and
`01 / 02 / 03` numbering on content that is not genuinely a sequence.

Sizing for a 1920×1080 frame: headline 90–120px, body 48–56px, labels 32px
minimum. Anything smaller is decoration — if it must be read, make it bigger.
Keep one focal point per frame, hold a generous margin, and keep scene content
out of the band reserved for captions.

## Styling: Tailwind is allowed

Tailwind v4 is installed and enabled workspace-wide. Use utility classes or
inline styles — whichever suits the video. Both are fine; don't mix them
arbitrarily within one video.

The engine's `index.ts` imports the stylesheet already, so the utilities are live
whether or not you reach for them — there is nothing to wire up, and nothing to
remove if you style purely inline.

**One hard limit:** Tailwind's animation and transition utilities — `animate-*`,
`transition-*`, `duration-*` — **do not render**. Remotion draws each frame
independently, so CSS-driven motion silently produces a static frame. Tailwind
is for layout, colour, spacing and type; motion is always `interpolate()`. This
is the single most common way a Remotion video comes out looking broken.

Note also that arbitrary Tailwind text sizes are usually easier to read at video
scale than the default scale — `text-[96px]` beats `text-6xl` when you want a
specific size.

## Motion craft

- Animate with `useCurrentFrame()` and `interpolate()`. **CSS transitions and
  CSS/Tailwind animations do not render.**
- Reach for `interpolate()` with `Easing.bezier()` first; `Easing.spring()` when
  the motion should overshoot.
- Prefer the `scale`, `translate` and `rotate` CSS properties over `transform`
  strings — they stay readable and editable.
- Stagger related elements by a few frames rather than moving them as a block.
- Add video and audio with `@remotion/media`, images with Remotion's `<Img>`.

## Narration

- **Short sentences.** Each becomes its own caption window; long clauses smear
  the timing.
- **Spell out symbols.** "the agent underscore id field", not `agent_id`.
  "line twenty-eight sixty", not `:2860`. The model reads literally.
- **Open on the symptom**, in the words someone would actually use to complain
  about it. Architecture comes after the viewer cares.
- **Don't narrate the visuals.** No "as you can see here".
- **60–150 seconds**, roughly 8–14 segments. Longer than that, ship two videos.
- Use `holdSeconds` on a segment when the animation needs to finish after the
  voice stops.

The narration spec passed to `explainer_narrate`:

```json
{
  "voice": "af_heart",
  "speed": 1.0,
  "fps": 30,
  "segments": [
    { "id": "symptom", "text": "The session sat there saying it was working." },
    { "id": "cause", "text": "A subagent outlived the stop event.", "holdSeconds": 6 }
  ]
}
```

Voices are `<language><gender>_<name>` — `af_heart` (default) is American
female, `am_` American male, `bf_`/`bm_` British. Voices blend by weight:
`"af_bella(2)+af_sky(1)"`. `speed` runs 0.25–4.0.

Pass `dry_run: true` to skip synthesis and get silence of estimated length — the
fast way to check pacing and layout before committing to real audio.

## Before you render

`explainer_still(slug, frame)` and actually look at the image:

- Is the main message readable in about a second?
- Is there one obvious focal point, or is the frame competing with itself?
- Does anything overlap, or collide with the captions?
- Would this frame still make sense if it were on screen for half a second?

Remember a still can catch a scene mid-reveal — if it looks sparse, check
whether later elements simply haven't arrived yet.

Then render, and report the output path or URL and the duration.

## Notes

- **Narration needs a speech service.** It runs on the user's machine, and a
  failed `explainer_narrate` usually means it is not running yet — the runtime's
  own setup command installs and starts it. Read the failure out of
  `explainer_job` before retrying.
- **Re-running is cheap.** Change the narration and re-narrate, or change the
  components and re-render. There is no state to clean up.
- **The workspace is a build area, not a repository.** Treat the MP4 as the
  deliverable and hand it over directly, rather than expecting the sources to
  still be sitting there later.
