# Use Cases: What People Are Actually Building

Note-taking assistants and calendar summaries get all the press. Here's what's actually interesting.

> 💬 **Community:** [@Bencera](https://x.com/Bencera/status/2031061573473808600) — "0 employees. $3.2M run rate. Here's exactly how I operate. TALK TO AI ALL DAY." The use cases below are how people like this actually leverage agents.

---

## 1. Due Diligence Research Agent ⭐⭐⭐ Advanced

**What it does:** Researches companies for M&A decisions, investment opportunities, or partnership evaluation — automatically.

**In practice:** The agent scrapes public information (LinkedIn, Crunchbase, news, filings), analyzes financials, maps competitors, identifies red flags, and generates a structured due diligence report. What used to take a junior analyst 2-3 days now takes 30 minutes.

**Why it matters:** Professional due diligence is gated behind expensive advisors and analyst hours. This democratizes it for individual investors, solo founders, and small firms evaluating partners.

**Real example:** Erik built exactly this — MA Advisor, running at port 3002, generates full structured reports on demand. The same tool investment banks charge thousands for.

**How to build:**
1. Set up Claude Code with [Perplexity](https://www.perplexity.ai/computer) or Tavily for web search
2. Create a report template in CLAUDE.md (financials, risks, competitors, news section)
3. Give it a company name — it handles the rest

---

## 2. Competitive Intelligence Monitor ⭐⭐ Intermediate

**What it does:** Watches your competitors' websites, LinkedIn, job boards, and news — daily, automatically.

**In practice:** Agent detects price changes, new feature announcements, new hires, funding rounds, and blog posts. You get an alert before your sales team even hears about it.

**Why it matters:** Competitive intelligence is usually manual, expensive, or just... not done. An agent makes it continuous and free.

**Example alert you might get:**
> "Competitor X just posted 3 Senior ML Engineer positions in the past 48h → they're likely building a new AI feature. 2 are in their recommendations team."

**How to build:**
1. Cron job that runs your agent daily against a list of competitor URLs
2. Agent scrapes + diffs against previous snapshot
3. Telegram/Discord alert if meaningful change detected

---

## 3. Personal Finance Analyzer ⭐⭐ Intermediate

**What it does:** Analyzes your actual bank exports and gives real insights — not just pie charts and totals.

**In practice:** Agent categorizes spending with context, identifies patterns humans miss, flags unusual charges, and benchmarks this month against your last 6 months. Weekly Telegram report.

**Why it matters:** Most people genuinely have no idea where their money goes. The numbers exist in their bank app — but no one reads them. An agent that can *reason* about your data is different from an app that just visualizes it.

**The honest take:** This is what Mint tried to do. An agent does it better because it can say "you spent 40% more on food delivery in weeks when you worked past 8pm" — that's a pattern, not a statistic.

**How to build:**
1. Export CSV from your bank (most banks support this)
2. Python script feeds it to Claude with a structured analysis prompt
3. Weekly automated report to your phone

---

## 4. Legal Document Reviewer ⭐⭐⭐ Advanced

**What it does:** Reviews contracts, terms of service, rental agreements, freelance contracts — before you sign them.

**In practice:** Agent flags risky clauses, explains legal jargon in plain language, compares to standard terms, and suggests specific things to negotiate. Especially useful for NDAs, freelance contracts, and rental agreements.

**Why it matters:** Most people sign contracts without reading them. They're written to be unreadable. An agent changes that without a €500/hr lawyer.

**Real value:** One user almost signed a freelance contract with a €5000 liability clause hidden in section 14. Agent caught it in 90 seconds.

**How to build:**
1. PDF → text extraction (pdfplumber or similar)
2. Structured Claude analysis with a legal review prompt template
3. Output: flagged clauses, risk rating, suggested negotiation points

**Important caveat:** Not a lawyer, not legal advice. But 80% of the value at 0% of the cost.

---

## 5. Scientific Literature Agent ⭐⭐⭐ Advanced

**What it does:** Monitors arXiv and research databases, summarizes new papers, and for technical users — runs preliminary analysis on data.

**In practice:** Daily digest of relevant papers in your domain, auto-downloaded and summarized. Key findings extracted. Interesting anomalies flagged for human review.

**Why it matters:** Researchers spend 30%+ of their time reading papers just to stay current. This cuts that significantly.

**Real example:** Erik's Gooper-1 project uses lightkurve + ML to analyze Kepler space telescope data automatically — the agent monitors the pipeline, summarizes findings, and flags anomalies. It found 12 candidate exoplanet signals; 3 turned out to be real data anomalies worth investigating.

**How to build:**
1. arXiv API + paper scraper (semantic scholar also has a good API)
2. Claude summarization with domain-specific prompt (you define what "interesting" means)
3. Daily digest to Telegram/Discord with links to full papers

---

## 6. Job Application Agent ⭐⭐ Intermediate

**What it does:** Monitors job boards matching your profile, customizes your application materials, and tracks status.

**In practice:** Agent finds relevant postings based on your criteria, tailors your CV and cover letter for each role, and maintains a tracking spreadsheet of where you've applied.

**Why it matters:** Job searching is exhausting, repetitive, and demoralizing. The mechanical parts — finding, applying, tracking — can be automated. The judgment about which roles actually fit? That stays human.

**How to build:**
1. LinkedIn/Indeed scraper with your criteria
2. Your CV as a base template in the agent's context
3. Claude customizes cover letter per role + updates tracking sheet

**The uncomfortable truth:** Your competition is already using something like this. The people getting responses aren't necessarily better candidates — they're applying to more relevant roles, faster.

---

## 7. Real Estate Research Agent ⭐⭐ Intermediate

**What it does:** Monitors property listings, analyzes neighborhoods, and estimates true value vs. asking price.

**In practice:** Agent tracks price history on specific listings, compares to similar properties in the micro-location, flags suspicious listings (too good to be true), and alerts you when something matching your criteria appears.

**Why it matters:** Buying property is the largest financial decision most people make — with almost no systematic analysis. The data exists. Nobody looks at it.

**Example insight you'd get:**
> "This apartment is priced 23% above comparable properties based on 47 recent sales within 400m. Similar units sold for €285k-€310k in the last 6 months. Asking price: €368k."

**How to build:**
1. Immoscout24/Zillow API or scraper with your search criteria
2. Claude analysis against historical comparable data
3. Telegram alerts for new matches + weekly market summary

---

## 8. Customer Support Intelligence ⭐⭐ Intermediate

**What it does:** Reads all your support tickets, identifies patterns, and surfaces actionable product insights.

**In practice:** Agent clusters similar issues, tracks sentiment trends over time, identifies root causes, and writes a weekly insights report. It tells you what's actually breaking and why.

**Why it matters:** Most companies have hundreds of customer insights rotting in their support system. Someone closes the ticket, problem "solved" — but the root cause never gets fixed. An agent systematically surfaces this.

**Real finding from a startup:** Agent identified that 34% of support tickets were caused by one confusing UI element. One engineer, one afternoon, one fix — thousands fewer tickets per month.

**How to build:**
1. Zendesk or Intercom API connection
2. Claude analysis with clustering and sentiment instructions
3. Weekly Slack/Telegram report with ranked issues + recommended fixes

---

## 9. Content Research & Brief Generator ⭐ Basic

**What it does:** Researches topics thoroughly and generates detailed content briefs — not the content itself.

**In practice:** Agent finds top-ranking articles, identifies coverage gaps, surfaces keyword opportunities and audience questions, then creates a structured brief your writer can actually use.

**Why it matters:** Content strategy work is valuable but tedious. The research that good briefs require takes 3-4 hours manually. An agent does it in 10 minutes.

**How to build:**
1. Tavily or [Perplexity](https://www.perplexity.ai/computer) for web research
2. Claude analysis with a brief template
3. Output: target audience, key questions to answer, competitor gaps, structure suggestions

**The honest position:** Don't use AI to generate the content — readers can tell. Use it to be 5x better at briefing the writers who create it.

> 💬 **Community:** [@Mike_Scully_](https://x.com/Mike_Scully_/status/2034253609865732233) — "I condensed everything I know about selling AI services into one cheat sheet." — if you're building agent use cases for clients, this is required reading.

> 💬 **Community:** [@seratch](https://x.com/seratch/status/2031041799494791415) — "New post on the OpenAI Developer Blog: how we use skills for open-source maintenance" — skills as use cases, not just utilities.

---

## 10. Travel Planning Agent ⭐⭐ Intermediate

**What it does:** Builds complete, realistic trip itineraries based on your preferences, budget, and constraints.

**In practice:** Agent researches destinations with current information, finds actual hotels and restaurants (with context, not just lists), checks realistic prices, and builds a day-by-day plan with logistics. Unlike ChatGPT travel suggestions, this can be tuned to your actual style.

**Why it matters:** Good travel planning takes 10+ hours. Most people skip it and wing it — then spend a week in the wrong neighborhood or miss things they would have loved.

**How to build:**
1. [Perplexity](https://www.perplexity.ai/computer)/Tavily for real-time search (current prices, reviews, availability)
2. Claude reasoning with your preferences in the system prompt
3. Structured output → PDF or Notion export

**The twist:** The agent can continuously update the plan. Flight delayed 4 hours? It rebuilds the first day. Weather looks bad Tuesday? It reshuffles the outdoor activities.

---

## 11. Code Review Agent ⭐⭐ Intermediate

**What it does:** Reviews pull requests for security vulnerabilities, performance issues, and code quality — automatically.

**In practice:** GitHub webhook triggers the agent when a PR is opened. It checks for injection vulnerabilities, exposed credentials, insecure dependencies, performance anti-patterns, and style guide violations. Then it posts a structured review as a PR comment.

**Why it matters:** Human code reviewers are excellent at architecture and business logic. They're terrible at catching the mechanical security issues — not because they're bad engineers, but because there are too many patterns to remember.

**Real stat:** Agent systems catch ~85% of common security issues that human reviewers miss in practice (SQL injection, hardcoded credentials, insecure direct object references).

**How to build:**
1. GitHub webhook → your server → Claude analysis
2. Structured review prompt with security checklist
3. Bot posts review comments back to the PR

**Reference:** Erik's auto-review system costs ~$0.35 per PR and has caught 3 critical security issues that would have shipped to production.

---

## 12. Personal Health & Wellness Tracker ⭐⭐ Intermediate

**What it does:** Analyzes health data from wearables, identifies patterns, and surfaces evidence-based insights about what actually affects your metrics.

**In practice:** Agent reads Apple Health or Garmin exports, correlates sleep quality with activity, alcohol, stress signals, and HRV data. It finds the patterns that aren't obvious when you're just looking at individual nights.

**Why it matters:** Wearables generate enormous amounts of data. Almost nobody analyzes it systematically. The insights are sitting there — the agent finds them.

**How to build:**
1. Health data export (Apple Health XML → CSV, or Garmin Connect API)
2. Python analysis script for data prep
3. Claude pattern recognition + weekly insights report

**Important caveat:** Not medical advice. But "your HRV drops 15% when you sleep less than 6.5 hours AND have more than 2 drinks" is a data insight, not a diagnosis. The distinction matters.

**The finding that changed one user's habits:** Agent found that afternoon coffee (not morning coffee) was the primary predictor of sleep quality. Morning coffee: no significant correlation. 3pm coffee: HRV down 12%, sleep efficiency down 18% the following night.

---


---

## 13. Website Portfolio Manager & Debug Agent
**Difficulty: ⭐⭐⭐ Advanced** | **Perfect for: anyone running multiple content sites**

If you build and operate multiple websites — niche directories, affiliate sites, content hubs — managing them manually doesn't scale. An agent changes this.

**Scenario:** You've built a directory of all swimming schools in Germany, another for dog training schools, another for cooking classes. Each gets traffic, earns affiliate revenue or lead fees. You have 10 sites. Then 20. Then 50. You can't manually check them all.

**What the agent does:**

*Monitoring:*
- Checks all sites daily for broken links, 404 errors, slow load times
- Monitors Google Search Console for ranking drops, crawl errors, manual penalties
- Alerts you on Telegram when something needs attention
- Auto-fixes common issues (broken internal links, missing meta tags) via PR

*Content optimization:*
- Analyzes which pages get traffic but don't convert → suggests improvements
- Identifies missing content opportunities (keywords competitors rank for, you don't)
- Monitors competitor sites for new content in your niches
- Drafts content updates for your review

*SEO maintenance:*
- Monitors backlink profile for toxic links
- Tracks ranking changes for target keywords
- Suggests internal linking improvements

**How to build it:**
1. Google Search Console API → export site health data daily
2. Screaming Frog or similar for crawl errors (or build a lightweight Python crawler)
3. Claude analysis: "Flag anything that needs human attention"
4. Telegram alert system for urgent issues, weekly summary report for the rest

**The scale advantage:** One agent monitoring 50 sites costs the same as monitoring 1 site. Your operational overhead stays flat as you grow.

---

## 14. Inbound Request Handler (Email, Messenger, Forms)
**Difficulty: ⭐⭐ Intermediate** | **Perfect for: marketplace/directory operators**

You've built a directory of dog training schools in Germany. It gets traffic. Dog trainers start finding it and want to be listed, submit their content, update their profiles, ask questions.

At 10 requests a week: you handle it manually.
At 100 requests a week: you're drowning.
At 1,000 requests a week: you need a system.

**What the agent does:**
- Reads incoming emails/form submissions/Messenger messages
- Classifies: "new listing request" / "content update" / "billing question" / "spam"
- For listing requests: validates the business is real (Google Maps lookup, website check), sends confirmation, adds to review queue
- For content updates: verifies the sender is the business owner, formats content, creates a draft update for your approval
- For common questions: responds automatically with the right answer
- Escalates genuinely complex cases to you with context

**Example flow for a dog training school submission:**
1. School emails: "Hi, I'd like to be listed on your site. We offer..."
2. Agent checks: Does this business exist? Website? Google Maps? Legitimate?
3. Agent responds: "Thanks! We've received your submission. We'll review within 48 hours. In the meantime, could you provide..."
4. Agent creates a structured draft in your CMS review queue
5. You review and approve in 30 seconds instead of spending 10 minutes processing it yourself

**How to build it:**
1. Email API (Gmail API or similar) or form webhook → Claude classification
2. Google Maps / Places API for business validation
3. Structured output → your CMS or database
4. Telegram notification for items requiring your attention

**The business model impact:** This is the difference between a directory that caps at "what you can personally manage" and one that can scale to thousands of listings with the same operational overhead.

---

## 15. Discovery Pipeline Agent (Rapid Idea Testing)
**Difficulty: ⭐⭐ Intermediate** | **Impact: ship 5x more ideas**

You have ideas faster than you can evaluate them. A new niche directory, a new affiliate angle, a new content format. Most won't work. The ones that do need to be identified fast.

**The problem:** Evaluating each idea properly takes hours of research. So most ideas die in your head.

**What the agent does:**
Runs each idea through a structured 5-phase evaluation in 20-30 minutes:

1. **Problem check** — Is there real search demand? Who's looking for this?
2. **Market research** — Who already exists in this space? How strong are they?
3. **Opportunity assessment** — Where are the gaps? What's underserved?
4. **Quick business model** — How would this make money? What's realistic?
5. **Go/No-Go** — Structured recommendation with reasoning

**Example:** You think "directory of all dog groomers in Germany, filterable by specialty"
- Agent researches: search volume for related terms, existing directories (strength?), monetization options (lead gen? listings? affiliate?)
- 25 minutes later: "Viable. Search demand exists (8,400/mo for core terms). Main competitor is weak (DA 22, thin content). Revenue model: €29/month listing fee, 500 listing target = €14,500/month potential. Recommend building MVP in 2 weeks."

**How to build it:**
1. Tavily/Perplexity for market research
2. Claude as the analyst/synthesizer
3. Structured output template (the 5-phase report)
4. Store results in a simple database so you can compare across ideas

**Why this matters:** The bottleneck for most small operators isn't having good ideas — it's evaluating them fast enough to know which ones to pursue. This agent turns a 3-hour research session into a 25-minute automated analysis, letting you test 5x more ideas in the same time.

> 💬 **Related:** The [Discovery Pipeline skill](04b-skills.md) is the framework that powers this — you can use it interactively or let the agent run it autonomously.

## Building Your First Use Case

Three paths depending on where you're starting:

### If you're not a coder: Start with Content Research (Use Case #9)
- Requires only Claude Code + Tavily API key
- No coding, no infrastructure, no server
- Immediate value: 10 minute research task becomes 10 minutes of review instead
- Build time: 1 afternoon

### If you're intermediate: Start with Competitive Intelligence (#2) or Personal Finance (#3)
- Requires basic scripting (copy-paste friendly)
- Clear, measurable value you'll feel immediately
- You'll learn web scraping, cron jobs, and Telegram bots along the way
- Build time: 1-2 weekends

### If you're a developer: Start with Code Review (#11) or Due Diligence (#1)
- Immediately applicable to real work
- GitHub webhook integration is a great gateway to agent infrastructure
- You'll build patterns that scale to more complex systems
- Build time: A few hours to MVP, a week to production-quality
