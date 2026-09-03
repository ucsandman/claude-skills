# Search registration and analytics for a new public surface

Do this the day a public web surface goes live, in the same session that deploys it.
Every step below was run for declick.dev on 2026-09-03 through Wes's logged-in Chrome
(claude-in-chrome). None of it needs a password typed by the agent; when a sign-in wall
appears, hand Wes the one-line step and continue with the rest.

Checklist, in order:

1. Google Search Console: property verified, sitemap submitted, home URL inspected.
2. Bing Webmaster Tools: site imported from Google Search Console.
3. Analytics enabled on the hosting platform and its tag on every page.
4. Recorded: the verification method in the repo's design or ops doc, and the state in memory.

## 1. Google Search Console

- Open https://search.google.com/search-console/welcome in the browser tools.
- Add a property. Pick **URL prefix** (https://example.com/), not Domain. URL prefix
  verifies with an HTML meta tag and needs no DNS change, which keeps it out of the DNS
  hard stop. Domain properties need a TXT record.
- Expand **HTML tag** under other verification methods and read the token from the
  textbox. Add it to the head of the home page:
  `<meta name="google-site-verification" content="<token>">`
  Do not use the HTML file method on a site with clean URLs; the .html path redirects
  and verification fails.
- Deploy, confirm with curl that the live home page carries the tag, then click Verify.
- Sitemaps page: submit `sitemap.xml`. Confirm the row reads Success with the expected
  page count.
- URL inspection: paste the home URL in the top search box (click the box by coordinate,
  the ref click may not focus it), wait for the report, click **Request indexing**, wait
  for "Indexing requested". Do the same for any second page that matters.
- Performance and Indexing reports fill in after a day or so.

## 2. Bing Webmaster Tools

- Open https://www.bing.com/webmasters. It always starts at a sign-in wall; the agent
  cannot sign in. Hand Wes: "bing.com/webmasters, sign in, Import from Google Search
  Console, pick the property." He did this for declick.dev in about two minutes.
- Bing feeds Copilot, DuckDuckGo and ChatGPT search, so this is not optional for a
  product aimed at engineers.
- Once signed in, Bing has a Submit Sitemaps page and a URL submission quota; the import
  brings the sitemap across automatically.

## 3. Analytics

- Vercel site: project page, Analytics tab, **Enable**, keep the tier included in the
  plan (Web Analytics, not Plus, unless Wes says otherwise). Then the tag:
  - static HTML: `<script defer src="/_vercel/insights/script.js"></script>` in the
    head of every page. Same origin, no cookies, works with the page's CSP.
  - Next.js: `npm i @vercel/analytics` and `<Analytics />` from
    `@vercel/analytics/next` in the root layout.
  - Confirm the script URL returns 200 on the live domain.
- A site that promises zero JavaScript records the exception in its design doc: the
  analytics script is the only script and nothing on the page depends on it.
- Other hosts: use the platform's first-party analytics before adding PostHog or GA;
  a third-party script is a render-blocking and privacy cost that needs a reason.

## 4. Record it

- Repo: the verification method and the analytics tag go in docs/DESIGN.md or the ops
  runbook, with the date. Removing the meta tag revokes the Search Console verification.
- Memory: the project state file notes which consoles are registered and what is still
  pending (usually Bing until Wes signs in).

## Reporting

In the preflight report the line is:

```
[PASS|FAIL] search   gsc=<verified|MISSING> sitemap=<submitted|MISSING> bing=<imported|PENDING WES> analytics=<vercel|posthog|MISSING>
```

A PENDING WES on Bing is not a FAIL; it is a one-line handoff in the report.
