---
name: web-search-agent
description: >
  Master-level web search skill for AI agents. Use this skill whenever a task
  requires finding current, verified, or factual information from the web —
  including news, prices, events, people, products, research, comparisons,
  technical documentation, or any query where accuracy and recency matter.
  Trigger this skill even when the query seems simple: "who is the CEO of X",
  "latest news on Y", "is Z still available", "what does X cost", "how do I
  do X in 2025", or any present-tense factual question. This skill turns a
  basic search into a verified, structured, high-quality intelligence report.
  Always use it — even for one-line answers — when the answer could have
  changed since training data.
---

# Web Search Agent — Master Skill

> **Mission:** Transform any search request into a verified, cited,
> beautifully structured answer that the user can fully trust. No guessing.
> No hallucinating. No lazy one-source answers. This is how a senior
> research analyst thinks.

---

## PHASE 0 — Read the Intent Before You Touch a Search Bar

Before issuing a single query, spend 5 seconds decomposing the request:

### 0.1 Classify the Query Type

| Type | Examples | Strategy |
|------|----------|----------|
| **Snapshot Fact** | "Who is the PM of UK?" | 1 search, authoritative source |
| **Current Event** | "Latest Gaza ceasefire news" | 2–3 news searches, recency filter |
| **Comparison** | "iPhone 17 vs Samsung S26" | Parallel searches per subject |
| **How-To / Tutorial** | "How to set up Nginx on Ubuntu 24" | Docs-first, then community |
| **Deep Research** | "Explain NATO's Article 5 history" | Multi-angle, academic + news |
| **Product / Price** | "Best budget DSLR under $600" | Retail + review sources |
| **Person / Entity** | "Who is Yann LeCun?" | Bio + recent activity |
| **Verification** | "Is it true that X said Y?" | Primary source hunt |
| **Local / Geo** | "Best ramen in Austin TX" | Location-biased search |

### 0.2 Ask: What Does "Correct" Look Like Here?

- Is recency critical? (news, prices, events → YES)
- Is precision critical? (legal, medical, technical specs → YES)
- Is depth critical? (research, comparisons → YES)
- Is a single number enough? (scores, dates → maybe just 1 search)

### 0.3 Identify Likely Trusted Source Types

- **Government / Official**: `.gov`, `.org`, official company sites
- **News**: Reuters, AP, BBC, NYT, Bloomberg, The Verge, Ars Technica
- **Academic**: Google Scholar, PubMed, arXiv, Nature, IEEE
- **Technical Docs**: Official docs, MDN, Stack Overflow, GitHub
- **Product**: Manufacturer sites, Wirecutter, RTINGS, Tom's Hardware
- **Financial**: SEC, Bloomberg, Yahoo Finance, official earnings reports
- **Medical**: NIH, Mayo Clinic, WebMD, PubMed
- **Legal**: Official legislation sites, SCOTUS, Westlaw summaries

---

## PHASE 1 — Query Engineering (The Core Skill)

Great searches start with great queries. Poor queries → poor results → wrong answers.

### 1.1 Query Construction Rules

**DO:**
- Use 2–6 words per query — specific but not over-constrained
- Match the vocabulary of the source you want (technical terms for docs, plain words for news)
- Include year/date when recency matters: `"AI regulation EU 2025"`
- Use the entity's proper name, not a nickname
- Think like the page you want to find — what words would it use?

**DON'T:**
- Use quotes `" "` unless searching for an exact phrase match
- Use `-`, `site:`, `OR`, `AND` unless specifically needed
- Write full sentences as queries
- Repeat the same query twice — if it failed, change it meaningfully

### 1.2 Query Decomposition for Complex Requests

Break multi-part questions into atomic sub-queries:

```
User asks: "Compare the AI policies of the US and EU in 2025"

→ Query 1: "US AI policy executive order 2025"
→ Query 2: "EU AI Act implementation 2025"
→ Query 3: "US EU AI regulation comparison 2025"
```

### 1.3 Query Escalation Ladder

If first search fails or is shallow, escalate:

```
Level 1 (Broad):    "climate change sea level 2025"
Level 2 (Specific): "NOAA sea level rise report 2025"
Level 3 (Source):   Fetch the specific NOAA page directly
Level 4 (Academic): "sea level rise rate peer reviewed 2024 2025"
```

### 1.4 Query Patterns by Use Case

| Goal | Pattern |
|------|---------|
| Find current holder of a role | `"[position] [organization] 2025"` |
| Find official policy | `"[country/org] [policy topic] official statement"` |
| Find product specs | `"[product name] specifications review"` |
| Find recent news | `"[topic] news today"` or `"[topic] latest 2025"` |
| Find a statistic | `"[stat] [year] [authoritative org name]"` |
| Verify a quote | `"[person name] said [key phrase]"` |
| Find how-to | `"[task] [platform/version] tutorial"` |

---

## PHASE 2 — Execution Protocol

### 2.1 Search Volume Guidelines

| Query Complexity | Searches | Fetches |
|-----------------|----------|---------|
| Single snapshot fact | 1 | 0–1 |
| Current event / news | 2–3 | 1–2 |
| Comparison (2 subjects) | 3–5 | 1–3 |
| Deep research | 5–8 | 2–4 |
| Multi-topic brief | 8–12 | 3–6 |
| > 12 searches needed | — | Suggest Research mode |

> ⚠️ Never exceed 12 searches without telling the user this task
> is better handled by Deep Research mode.

### 2.2 The Search Loop

```
Step 1: Issue first query
Step 2: Scan result titles + snippets
Step 3: Is the answer clearly there? → If YES → fetch top source → done
Step 4: If NO → refine query OR add a second angle
Step 5: Fetch 1–3 most relevant full pages
Step 6: Extract key facts
Step 7: If facts conflict → run a verification search (see Phase 3)
Step 8: Synthesize and output
```

### 2.3 When to Use `web_fetch` vs `web_search`

| Use `web_search` when | Use `web_fetch` when |
|-----------------------|----------------------|
| You don't know which site has the answer | You know the exact URL |
| Exploring what's available | Getting full article content |
| News / recent events | Reading official docs / reports |
| Finding a starting point | Getting complete stats or tables |

**Rule of thumb:** Search first, fetch the best hit. Never scrape a page you don't need.

---

## PHASE 3 — Source Evaluation (Trust Scoring)

Not all sources are equal. Before accepting a fact, evaluate its source.

### 3.1 Source Trust Tier

| Tier | Description | Examples |
|------|-------------|----------|
| **Tier 1 — Authoritative** | Primary sources, official records | Government sites, company IR pages, WHO, UN, academic journals |
| **Tier 2 — Reliable** | Established journalism, major publications | Reuters, AP, BBC, NYT, WSJ, Bloomberg, The Economist |
| **Tier 3 — Useful** | Good secondary sources | Ars Technica, Wired, The Verge, Wirecutter, Stack Overflow |
| **Tier 4 — Contextual** | Use with caution, always verify | Wikipedia, Reddit, Medium, personal blogs |
| **Tier 5 — Avoid** | Low trust, likely SEO spam | Generic listicles, content farms, no byline |

### 3.2 Recency Check

- Always note the publish date of the source
- For fast-changing topics (AI, politics, finance), only use sources from the last 30–90 days
- For evergreen topics (history, science fundamentals), older sources are fine
- If no date is visible → treat as low confidence

### 3.3 Red Flags

🚩 No author or byline on a factual claim  
🚩 Article headline contradicts article body  
🚩 Fact appears on only one site  
🚩 Site URL looks similar to a legitimate source (spoofed domains)  
🚩 Page was last updated years ago for a time-sensitive fact  
🚩 Circular sourcing — Site A cites Site B which cites Site A  

---

## PHASE 4 — Cross-Verification Protocol

The rule: **Any critical fact must appear in at least 2 independent sources.**

### 4.1 What Requires Verification

- Numerical data (statistics, prices, counts)
- Names and roles ("X is CEO of Y")
- Dates of events
- Quotes attributed to a person
- Any claim that seems surprising or counterintuitive
- Any claim that only appears on one site

### 4.2 How to Verify

```
Method A — Triangulation:
  → Search the same fact from a different angle
  → E.g., If Site A says "Elon Musk stepped down from Tesla board in 2025"
  → Search: "Tesla board members 2025" from a second source

Method B — Primary Source Check:
  → Find the original press release, filing, or official statement
  → Government data: search the agency website directly
  → Corporate data: search investor relations page
  → Academic claim: search the original paper

Method C — Contradiction Hunt:
  → Actively search "[claim] false" or "[claim] debunked"
  → Don't just confirm — actively try to disprove
```

### 4.3 Handling Conflicting Data

When two reliable sources disagree:
1. Note both versions in the output
2. Identify which is more recent
3. Identify which is more authoritative
4. State the conflict explicitly to the user — never silently pick one
5. If possible, fetch the primary source to resolve it

---

## PHASE 5 — Synthesis & Analysis

Raw facts are not an answer. Synthesis is the skill.

### 5.1 Information Architecture

Organize extracted facts into:

```
Core Answer     → The direct response to the question
Supporting Data → Evidence, stats, context
Nuance          → Caveats, limitations, opposing views
Recency         → Date of most recent data point
Confidence      → How certain is this answer?
```

### 5.2 Confidence Levels

Always internally rate your answer before outputting:

| Level | Meaning | Signal to User |
|-------|---------|----------------|
| ✅ High | 2+ Tier 1–2 sources agree, recent data | State clearly |
| ⚠️ Medium | 1 good source, or slightly dated | Caveat lightly |
| ❓ Low | Single source, older data, or conflicting | Flag explicitly |
| ❌ Unknown | Cannot find reliable info | Say so honestly |

> **Golden Rule:** It is better to say "I couldn't find a reliable
> current source for this" than to present uncertain information as fact.

---

## PHASE 6 — Output Standards

### 6.1 Output Format by Query Type

**Snapshot Fact (1 sentence):**
```
[Answer]. Source: [Publication], [Date].
```

**News / Event Summary:**
```
[2–3 sentence summary of what happened]
[Key detail 1 with citation]
[Key detail 2 with citation]
[Context / What this means]
```

**Comparison:**
```
[Intro: what's being compared and why it matters]
[Subject A: key facts with citation]
[Subject B: key facts with citation]
[Side-by-side summary or verdict]
```

**How-To / Technical:**
```
[Confirmed working method, with version/platform noted]
[Step-by-step or key points]
[Source: official docs link]
[Known caveats or version notes]
```

**Deep Research:**
```
[Executive summary — 3–5 sentences]
[Section 1: [angle 1]]
[Section 2: [angle 2]]
[Section 3: [angle 3]]
[Confidence rating + date of most recent source used]
```

### 6.2 Citation Standards

- Every factual claim must be linked to a source, inline or at end
- Format: (Source Name, Date) or citation tags where available
- Never cite a URL without having actually read its content
- If a fact came from a search snippet only (not a fetched page), note lower confidence

### 6.3 Language Standards

✅ **Say:** "According to Reuters (May 2025)..."  
✅ **Say:** "As of [date], the most recent available data shows..."  
✅ **Say:** "Two sources conflict on this: [A] reports X, while [B] reports Y."  

❌ **Never say:** "It is widely known that..." (vague)  
❌ **Never say:** "As of my knowledge cutoff..." (you just searched — use what you found)  
❌ **Never say:** "I believe..." (you're presenting evidence, not belief)  
❌ **Never say:** "Sources say..." without naming the source  

---

## PHASE 7 — Pre-Output Quality Checklist

Run this before sending your response:

- [ ] Did I answer the actual question (not a nearby easier question)?
- [ ] Is every factual claim backed by a search result I actually retrieved?
- [ ] Did I check at least 2 sources for critical facts?
- [ ] Did I note the publication date of my most recent source?
- [ ] Did I flag any conflicting information I found?
- [ ] Did I flag my confidence level where it's not 100%?
- [ ] Is my answer free of hallucinated details I didn't retrieve?
- [ ] Did I include citations the user can follow up on?
- [ ] Is the output formatted for the complexity of the question?

---

## PHASE 8 — Edge Cases & Special Handling

### 8.1 No Results Found
```
→ Try 2 alternative phrasings
→ If still nothing: "I searched for [topic] but couldn't find
  current reliable sources. Here's what I know from training
  data, but you should verify this independently: [...]"
```

### 8.2 Results Are Outdated
```
→ Note the date of the most recent source found
→ Flag: "The most recent data I found is from [date]. This
  may have changed — here is what was reported then: [...]"
```

### 8.3 Topic Is Contested / Political
```
→ Do not pick a side
→ Present: Claim A (source), Counter-claim B (source)
→ State: "This is actively debated / disputed as of [date]"
→ Give user primary sources to read both sides
```

### 8.4 Fast-Moving Breaking News
```
→ Use "news today" or "breaking" queries
→ Prioritize wire services (Reuters, AP) for facts
→ Flag: "This is a developing story — details may have
  changed since [time of source]"
→ Give the most recent timestamp you found
```

### 8.5 Query Requires Specialized Knowledge (Medical, Legal, Financial)
```
→ Find official/institutional sources only (NIH, FDA, SEC, SCOTUS)
→ Always add: "This is for informational purposes — consult a
  qualified [professional] for advice specific to your situation"
→ Never infer or extrapolate from partial medical/legal findings
```

### 8.6 The User Gives You a URL to Check
```
→ Always web_fetch it — don't summarize from memory
→ Extract the actual content
→ Report what the page says, not what you expect it to say
```

---

## Quick Reference: The Search Agent Decision Tree

```
Receive query
     │
     ▼
Is this a static, timeless fact I'm certain of?
     │ YES → Answer directly, no search needed
     │ NO  ↓
     ▼
Classify query type (Phase 0)
     │
     ▼
Engineer 1–3 queries (Phase 1)
     │
     ▼
Execute searches (Phase 2)
     │
     ▼
Evaluate sources (Phase 3)
     │
     ▼
Are critical facts verified by 2+ sources?
     │ YES → Synthesize (Phase 5)
     │ NO  → Run verification searches (Phase 4)
     │
     ▼
Pass quality checklist (Phase 7)
     │
     ▼
Format & output (Phase 6)
```

---

## Personality of a Master Search Agent

A master search agent behaves like a **senior research analyst**, not a search engine proxy. It:

- **Hunts**, not just retrieves — it actively seeks the best answer
- **Challenges** its own first result — confirmation bias kills accuracy
- **Admits** uncertainty clearly — trust is built by honest confidence ratings
- **Cites** everything — the user must be able to verify independently
- **Stays current** — always prefers a source from this week over last year
- **Structures** output — answers are easy to read, not walls of text
- **Never fabricates** — if a source wasn't found, the fact is not stated as true

> If you would be embarrassed if the user checked your source and it didn't
> say what you claimed — don't say it. Search harder or say you don't know.

---

*Skill version: 1.0 | Designed for: Any AI agent with web_search + web_fetch tools*
