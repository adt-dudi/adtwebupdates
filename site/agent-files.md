# Agent-facing files for adhdotech.org

Drafted 2026-09-22. The llms.txt was published live on 2026-09-25; the footer
link is still outstanding (see below).

The ask: do what aihero.dev does — publish agent-discovery files and link them
from a small "Agents" link at the bottom of the site.

## What was there before

Both checked against the Wix API on 2026-09-22, before the change.

**`/llms.txt` already exists.** Wix auto-generates one for every site and serves
it at the root. The current file is untouched (`manuallyEdited: false`,
`hidden: false`), and it is weak for our purposes:

- it links exactly one page — the homepage;
- roughly 80% of it is generic Wix MCP boilerplate;
- it is English-only, on a Hebrew-primary site;
- it says nothing about which questions ADHDoTECH does and does not answer.

**`/robots.txt` needs no change.** It is Wix's default and opens with
`User-agent: * / Allow: /`. GPTBot, ClaudeBot, PerplexityBot, Google-Extended
and friends are all already permitted — only PetalBot is blocked, and dotbot
and AhrefsBot are crawl-delayed. Nothing is standing between an assistant's
crawler and the site's content.

## What was published

`site/llms.txt` in this repo — a curated bilingual replacement for the
auto-generated file. It keeps the Wix MCP endpoint section (that endpoint is
real and genuinely useful to an agent) and adds what the default lacks:

- a Hebrew and an English summary, since Hebrew is the primary language;
- every main page in both languages, each with a one-line description;
- all five published blog posts, with their real titles pulled from the Blog API
  rather than guessed from the URL slugs — worth doing, because two of the five
  slugs no longer match their titles;
- a **Scope** section naming what ADHDoTECH writes about (workplace practice:
  standups, sprint planning, code review, performance reviews, on-call, Slack;
  and ADHD concepts) and what it cedes to gov.il, כל-זכות and the health funds
  (rights, diagnosis pathways, medication regulation);
- organization details and the sitemaps.

Sources for every URL: the Search Console export in `baseline/gsc-2026-09/Pages.csv`
and the Wix Blog API. No URL in the file was invented.

## Publishing

Published on 2026-09-25 via `PUT .../v2/llms` with `manuallyEdited: true`.
A read-back confirms `manuallyEdited: true`, `default: false`, `hidden: false`,
and content matching `site/llms.txt`. That verification went through the Wix
API, not a fetch of the public URL — outbound access to adhdotech.org is
blocked from this environment, so the public `https://www.adhdotech.org/llms.txt`
is worth an eyeball in a browser.

To change it again, two routes; either one has the same effect.

**Dashboard:** Wix dashboard → Marketing & SEO → SEO Tools → the llms.txt
editor → paste the contents of `site/llms.txt` → save.

**API:**

```
PUT https://www.wixapis.com/promote-seo-robots-server/v2/llms
{
  "llmsTxt": {
    "content": "<contents of site/llms.txt>",
    "default": false,
    "manuallyEdited": true,
    "subdomain": "www"
  }
}
```

### The live trade-off

The file is now `manuallyEdited: true`, so Wix has stopped maintaining it.
New pages and new blog posts will not be added automatically, and deleted ones
will not be removed. Since the blog is the part of the site that grows,
updating `site/llms.txt` and re-publishing it is now a step in publishing
a post.

It is reversible: `PUT` with `"default": true` and no `content` restores Wix's
auto-generated file.

## The footer "Agents" link

This part cannot be done through the API. adhdotech.org is a classic Wix Editor
site, and the Wix REST API does not edit Editor page layout — the footer has to
be changed by hand in the Editor.

1. Open the site in the Wix Editor.
2. Scroll to the footer. It is a global section, so editing it once covers every
   page.
3. Add → Text → a small text element, styled to match the existing footer text
   but one or two sizes down. Label it `Agents` (or `llms.txt` — aihero.dev uses
   the filename, sitting alongside its `.xml` and `.md` sitemap links).
4. Select the text → Link → Web Address → `https://www.adhdotech.org/llms.txt`
   → open in a new tab.
5. Position it at the bottom right, per the request.
6. Repeat for the English footer via Wix Multilingual — translated footers carry
   their own text, so the link will not appear on `/en` pages otherwise.
7. Publish.

One note on step 5: the Hebrew site is RTL, so its footer content naturally ends
at the left, and bottom-right is the *start* of the row rather than the quiet
corner it is on an LTR site like aihero.dev. Worth looking at both languages
after placing it, and mirroring to bottom-left on Hebrew if it reads better —
the request was for the aihero.dev effect, and on an RTL page that is the
opposite corner.

## Worth being straight about

This will not move the numbers in `baseline/2026-09.md` on its own.

None of the three assistants tested in that baseline has been shown to fetch
`/llms.txt` while composing an answer. The file is read by coding agents and by
tools pointed at a site deliberately; it is not part of how ChatGPT, Gemini or
Perplexity pick sources during a live search. The baseline's zero is explained
by there being almost no Hebrew Ritual Query content on the site to retrieve —
the blog has five posts, and none of them answers a standup, sprint planning or
code review question directly.

So this is worth doing — it is cheap, it is correct, and it makes the site
legible to the agent traffic that does read these files — but it belongs
alongside the content work, not in place of it. Re-running the baseline after
this change alone should be expected to come back zero again.
