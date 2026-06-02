# Lumen Loop

**The discovery layer for the Stellar ecosystem.**

One place to find every project, story, event, video, job, and governance vote across Stellar, kept current by a fleet of AI agents that watch the ecosystem so you don't have to.

Lumen Loop is part live ecosystem feed, part canonical project directory, part research desk, and part marketing co-pilot. Under the hood it is an agentic content platform: it discovers what is happening across the ecosystem, understands it, ties it back to the projects it belongs to, and publishes it in formats people and machines can both use.

---

## The problem

The Stellar ecosystem is large, fast moving, and spread across hundreds of websites, blogs, social accounts, calendars, repos, and chat servers. That scatter creates three real problems:

1. **Newcomers don't know where to start.** A developer, founder, or partner arriving in the ecosystem faces a wall of links. Which projects matter? What happened last week? Where is the conference? Who is hiring? The answers exist, but they are scattered across dozens of places and go stale fast.

2. **Builders struggle to advocate for themselves.** Good teams ship quietly. Their launch gets a few likes, their blog post reaches their existing followers, and then it disappears. There is no shared surface where a project's work, funding, and momentum are collected in one place and put in front of the people who should see it.

3. **The foundation works from stale data.** Ecosystem maps, grant directories, and partner lists are maintained by hand, so they rot the moment they are published. Decisions get made on a snapshot that was already out of date.

Lumen Loop exists to close all three gaps at once with a single, always-current, machine-readable view of the ecosystem.

---

## Architecture at a glance

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              EXTERNAL  SOURCES                                  │
│   project sites + blogs      social posts       community calendars     repos  │
│     (RSS · sitemaps)          (X · Reddit)        (Luma · Discord)      (GitHub)│
└──────────┬──────────────────────┬──────────────────────┬──────────────────┬────┘
           │                      │                      │                  │
           ▼                      ▼                      ▼                  ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│   DISCOVER  →  MONITOR                                                          │
│   crawl a project's site → learn its X / GitHub / blog / RSS → watch those      │
│   official channels for anything new; pick up links people share on X & Reddit  │
└────────────────────────────────────┬───────────────────────────────────────────┘
                                      ▼
                          ┌───────────────────────┐
                          │   TRIAGE  (one door)  │   classify each item:
                          │   fetch · clean ·     │   article · event · video ·
                          │   classify · route    │   job · noise → route or drop
                          └───────────┬───────────┘
                                      ▼
              every item flows:  discovered → enriched → published
                                      │
                                      ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│   AGENTIC  ENRICHMENT     (LLMs + autonomous agents)                           │
│   • summarize & write editorial copy      • tie content → the right project(s)  │
│   • tag + categorize                       • vector embeddings (semantic search) │
│   • extract entities (people/orgs/tokens)  • classify events (region/calendar)   │
│   • self-managing agents: enrich projects · resolve source conflicts · triage Q  │
└────────────────────────────────────┬───────────────────────────────────────────┘
                                      ▼
                       ┌──────────────────────────────┐
                       │   ENRICHED  ECOSYSTEM  STORE  │
                       │   PostgreSQL + vector index   │
                       │   projects ⇄ content ⇄ funding│
                       │              ⇄ repos          │
                       └───┬───────────┬───────────┬────┘
                           │           │           │
            ┌──────────────┘           │           └──────────────┐
            ▼                          ▼                          ▼
     ┌─────────────┐         ┌────────────────────┐      ┌──────────────────┐
     │ PUBLIC SITE │         │  OPEN ECOSYSTEM DB │      │   EXTERNAL  MCP   │
     │ news·events │         │  (GitHub, open     │      │  query the corpus │
     │ projects·…  │         │  YAML, git-tracked)│      │  with an AI agent │
     └──────┬──────┘         └────────────────────┘      └──────────────────┘
            │
            ▼   …and the loop closes:
     ┌──────────────────────────────────────────────────────────────────────┐
     │   AUTOMATED  DISTRIBUTION                                             │
     │   agents spot what's under-covered → draft → schedule → publish via   │
     │   official platform APIs to X · Discord · Reddit · Telegram · Bluesky │
     └──────────────────────────────────────────────────────────────────────┘
```

Read it left to right, top to bottom: public sources flow in on the left, get understood and connected in the middle, and flow out on the right into a public website, an open database, an agent-queryable API, and a distribution engine that pushes the best of it back out to the community.

---

## The platform: what you see

Everything below is a public surface. Each one is a different lens on the same enriched corpus.

### Home: the live wire
A continuously updated feed of the ecosystem: the latest articles, freshly published research, upcoming events, new governance proposals, and notable social posts, all in one ticker. It is the "what's happening right now" view, the antidote to "I don't know where to start."

### News: curated stories, grouped by day
Every meaningful article, announcement, and analysis piece from across the ecosystem, deduplicated, summarized, and grouped by day. Filter by topic (DeFi, payments, governance, infrastructure, and so on). Source material comes from official project blogs and ecosystem publications (watched via their RSS feeds and sitemaps) and from links surfaced on social platforms. Each article links back to its original source and to the projects it mentions.

### Projects: the canonical directory
A browsable, searchable directory of the ecosystem's projects. Each card shows category, tags, funding and audit badges, and links. Open a project and you get a full profile that ties together, in one place:

- **Funding**: its Stellar Community Fund submissions, awards, rounds, and budgets.
- **Dev activity**: GitHub stats (stars, forks, recent commits, contributors), so you can see whether a project is actually shipping.
- **Audits**: security audit reports, plus an at-a-glance audited badge.
- **Token and on-chain data**: issued assets, deployed contracts, DeFi metrics where applicable, and (for live tokens) a price chart from an on-chain oracle.
- **Geography**: where the team is based and the regions it operates in.
- **Content and coverage**: every article, video, research piece, and social post about it, collected automatically.
- **Related projects**: neighbors found by category, tags, and semantic similarity.

Anyone can suggest a missing link or correct a field straight from the project page. Those suggestions land in a review queue rather than overwriting data directly.

### Events: a subscribable ecosystem calendar
Upcoming conferences, meetups, online sessions, and live audio rooms across the ecosystem, deduplicated across sources and shown in your local timezone. Source data comes from community event calendars and scheduled events on community servers. The calendar is **subscribable**: one click exports an iCal / `webcal://` feed you can add to Google Calendar, Apple Calendar, or Outlook, and it stays live as new events are discovered. A searchable archive holds everything that already happened.

### Media: searchable video, audio, and Spaces
An index of publicly available ecosystem talks, podcasts, and recorded live audio rooms. Content stays hosted at its source and plays through embedded players, and Lumen Loop adds **searchable transcripts** for indexing, so you can find the exact moment a topic was discussed, not just the right video.

### Research: the in-house desk
Long-form, original analysis and briefings written about the ecosystem: weekly round-ups, deep dives, and topic explainers. Each piece cites its sources and links to the projects it covers. Research can be filtered down to a single project, so a project page can show "everything we've written that mentions you."

### Jobs: the ecosystem job board
Open roles aggregated from ecosystem organizations, filterable by company and location. A single front door for "who is hiring in Stellar right now."

### Governance: live DAO votes
A dashboard of active and recent on-chain governance proposals from major Stellar protocols (such as Aquarius and YieldBlox), with live vote status, so the community can follow ecosystem decision-making without hopping between governance UIs.

### Search: across everything
One search box over news, projects, events, and media at once, with full-text ranking plus semantic matching on top, so a query finds the right thing even when the wording does not match exactly.

### Machine-readable surfaces
The same data is exposed for tools and agents: an RSS feed of the latest articles, the iCal events feed, and an `llms.txt` so AI assistants can find their way around.

---

## How it works

### 1. Discovery: find the sources
It starts with a project's website. Lumen Loop crawls it and reads off the things that matter: the project's social accounts (X, GitHub, Discord), its blog, and any RSS feeds. From one homepage, the system learns where that project actually publishes.

### 2. Monitoring: watch the official channels
Those discovered sources then get watched continuously. New blog posts (via RSS and sitemaps), new videos, and new scheduled events are picked up as they appear, straight from each project's own official channels. This is the high-trust spine of the system: a project's own outputs, monitored at the source.

### 3. Social discovery: catch what the official channels miss
Not everything announces itself through an RSS feed. So Lumen Loop also follows public posts on social platforms (X and Reddit) and the community's public event calendars, through official platform APIs and public feeds. When something relevant surfaces there (a shared link, a thread, a scheduled live session, an event), it gets pulled into the same pipeline. Only public, non-authenticated content is used. Social is where discovery happens; the official channels are where it gets confirmed and enriched.

### 4. Triage: one door, one decision
Every discovered item, no matter where it came from, flows through a single triage layer. That layer fetches the page, cleans it, and uses a lightweight model to decide what it is (an article, an event, a video, a job, or just noise) and where it should go. Irrelevant and duplicate items are filtered out here, before any expensive downstream processing.

### 5. Agentic enrichment: understand it and connect it
This is the heart of the system. Once an item is triaged, a sequence of AI steps turns raw content into structured, connected knowledge:

- **Editorial metadata**: a clean title, a summary, a short and long write-up, topic tags, and a category, all drawn from a controlled vocabulary so the data stays consistent.
- **Project linking**: the single most important step. Each piece of content is tied back to the specific ecosystem project(s) it is about, using a mix of deterministic matching (domain, social handle, repo, channel) and named-entity recognition. This is what makes "everything about project X, in one place" possible.
- **Categorization and event classification**: events get a region, country, format, and calendar; content gets placed in the ecosystem's topic taxonomy.
- **Embeddings**: every item is turned into a vector embedding and stored in a vector index, which powers semantic search, "related content," duplicate detection, and similarity across the whole corpus. Long transcripts are chunked and embedded passage by passage, so search can land on a specific moment.
- **Entity extraction**: people, organizations, and tokens are pulled out and normalized against the directory, building a graph of who and what is mentioned where.

Every item moves through a simple, visible status pipeline (discovered, then enriched, then published), so its state is always known and stuck items can be recovered automatically.

### 6. It manages itself
A fleet of autonomous agents keeps the data healthy without a human in the loop for routine work:

- One agent fills gaps on thin project records (missing category, tags, or region) by researching across the corpus and the live web, proposing values with a confidence score.
- When sources disagree about a project (its description, category, links, or which social account is really theirs), an agent investigates instead of just picking one. It pulls the field's full change history from the database and checks the competing values against external ground truth (the project's own live website and links), reasons about which is correct, then locks in the resolution and records the rejected value so it never gets re-proposed. The same conflict does not come back.
- A third rewrites weak project descriptions and flags projects that have gone quiet.
- A fourth reviews the incoming pipeline each day, handles the clear cases on its own, and proposes durable rules when it spots a bad pattern (for example, a domain that keeps producing off-topic or spam content).

These agents are **self-improving**: anything a human reviews and confirms becomes a verified example that teaches the agent to do better next time. High-confidence suggestions apply automatically; lower-confidence ones queue for a quick human check.

---

## The project directory and the open ecosystem database

The directory is the canonical record, and it is built to stay trustworthy when many sources disagree.

Each project record tracks, per field, what every source said (the project's own site, GitHub, the grant program, community submissions, internal edits). A resolver computes the winning value using clear policies (prefer this source, lock this value, merge these lists), and any field where sources genuinely conflict is flagged rather than guessed. When policy alone cannot settle a disagreement, an agent resolves it from evidence: it reads the field's history and verifies the competing values against the project's own live site and links before committing an answer that sticks, so the directory effectively corrects itself over time. The canonical record never auto-deletes a project just because an upstream source went quiet: projects can be hidden or, rarely, removed by hand, but a source dropping a project never silently erases it.

Each project is more than a profile, it is a set of structured, comparable data verticals: funding, dev activity, audits, token and on-chain data, geography, social reach, and content coverage. Because these all hang off the same canonical record, you can line them up against each other and ask cross-cutting questions: which regions attract the most funding relative to their development activity, which funded projects still lack an audit, how on-chain traction tracks against grant size. The directory is not just a list to browse, it is a queryable dataset of the ecosystem that an analyst or an AI agent can interrogate directly.

That canonical directory then **syncs out to an open, public database on GitHub** as plain YAML, one file per project. The export mirrors the current visible directory, and git history preserves every change over time. Fields that are still in conflict are held back from the public export until they are resolved, so the open data stays clean. The result is exactly what the foundation and the wider community need: a continuously updated, transparent, forkable map of the ecosystem that nobody has to maintain by hand. (Lumen Loop seeded itself from that same open database, and now keeps it current.)

---

## Automated marketing: the ecosystem's co-pilot

Lumen Loop does not just collect what the ecosystem is saying. It notices what the ecosystem is **not** saying, and helps fill the gap.

A signal layer reads the stream of public posts from trusted ecosystem accounts and groups them into topics (clustering similar posts together). It then checks each cluster against what has already been written and published. Clusters that are clearly being discussed but are under-covered in the content library are surfaced as **gaps**, scored by how important and how fresh they are. This step is deterministic, so it is cheap and repeatable.

Those gaps feed a set of research and drafting agents that compose original content (a tweet, a thread, a round-up, a deep dive) grounded in the enriched corpus. Drafts run through editorial style guides and fact-checking before a human reviews them.

Once approved, a rules engine decides **where** each piece should go, formats it per channel, and publishes it through **official platform APIs** (X, Discord, Reddit, Telegram, Bluesky, Farcaster), each with its own template. Distribution can be scheduled on a recurring cadence or a one-off time, and every attempt is logged and retryable.

Put together, the loop is: **surface what's being talked about → find the holes → draft to fill them → schedule → publish where it belongs.** It is a marketing function that runs continuously and gives quiet, deserving work the reach it would not get on its own.

---

## Open access: query the ecosystem with an AI agent

Everything Lumen Loop knows is also reachable by AI agents through a scoped MCP (Model Context Protocol) server. Connect an assistant with an access key and it can work directly against the live ecosystem corpus: search content semantically, pull together everything known about a given project, browse the directory, and run research queries over the index. Every key is scoped, so what it can see and do is controlled.

For outside parties, two external access tiers are on the roadmap:

- **Guest** access: browse the project directory and read published content with summaries and metadata, the public surface made agent-readable.
- **Partner** access: metered research, ask a natural-language question and have a research agent run over the corpus on your behalf, returning a synthesized, cited report, without handling the raw material yourself.

For a foundation analyst, a partner, or a developer, this turns the whole enriched ecosystem (the directory, the content graph, the semantic index, the live aggregates) into something an AI agent can simply ask. The internal scoped server runs today; the external guest and partner tiers are being rolled out.

---

## Why it matters

| The problem | What Lumen Loop does about it |
|---|---|
| Newcomers don't know where to start | One always-current front door: live feed, directory, calendar, media, jobs, and search across all of it. |
| Builders can't get discovered | Every project gets a profile that auto-collects its funding, content, and activity, and a marketing loop that surfaces under-covered work and publishes it. |
| The foundation runs on stale data | A canonical, conflict-aware directory that syncs continuously to an open, public database, plus agent access to query it live. |

One ingestion brain, many surfaces. Lumen Loop watches the Stellar ecosystem, understands it, connects it, and gives it back, to people through a website and a calendar, to the community through an open database, to machines through feeds and an agent API, and to the ecosystem's own reach through automated distribution.
