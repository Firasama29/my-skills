---
name: medium-article-drafter
description: Draft Medium blog articles for Firas (@ferasama) covering Java/backend dev and AI/Claude tools topics. Use this skill whenever the user wants to write, draft, outline, or plan a Medium article — even if they just mention a topic idea, say "let's write an article about X", "help me write a post on X", or "I want to publish something about X". Triggers on any article, blog post, or Medium-related writing request. Handles full workflow: outline → review → full article draft with code examples, SEO titles, and hooks.
---

# Medium Article Drafter

Helps Firas draft polished Medium articles for two audiences and publications:

| Topic Area | Publication | Style | Audience |
|---|---|---|---|
| Java / Backend Dev | Javarevisited | Mid-level deep dive | Junior-to-mid Java devs |
| AI & Claude Tools | AI-focused (new publication) | Beginner-friendly | Non-technical or early-adopter readers |

---

## Workflow

### Step 1 — Detect topic type

From the user's input, determine:
- **Java/Backend** → mid-level tone, technical depth, code examples required
- **AI/Claude Tools** → beginner-friendly tone, minimal jargon, relatable analogies

If unclear, ask: *"Is this for your Java audience or AI/Claude tools audience?"*

---

### Step 2 — Present the outline first

Before writing anything, output a structured outline like this:

```
## 📋 Article Outline — [Proposed Title]

**Publication:** Javarevisited / AI Publication
**Target reader:** [one sentence]
**Estimated read time:** X min

### Sections:
1. [Section title] — [one line description]
2. [Section title] — [one line description]
3. ...

### Code examples planned:
- [brief description of each snippet]

---
Looks good? I'll write the full article below, or let me know what to change.
```

Wait for implicit or explicit approval before proceeding. If the user says "looks good", "go ahead", "write it", or similar — proceed to Step 3.

---

### Step 3 — Write the full article

Structure every article with these components:

#### 🪝 Hook / Intro
- **Java articles**: Open with a relatable pain point or a surprising behavior devs encounter
- **AI articles**: Open with a simple "imagine this" scenario or a bold claim a beginner would find interesting

#### 📚 Sections
- Use clear H2/H3 headings (Medium-friendly)
- Each section: 1–3 short paragraphs max
- Mix prose with bullets, code, and callouts — no walls of text

#### 💻 Code Examples (Java articles)
- Java with Spring Boot context where relevant
- Include inline comments explaining key lines
- Show before/after when demonstrating a fix or improvement
- Add a short paragraph after each snippet explaining what's happening

#### 🤖 Analogies (AI articles)
- Use everyday analogies to explain technical concepts
- Define all acronyms on first use (MCP, LLM, API, etc.)

#### ✅ Conclusion
- Summarize key takeaways as 3–5 bullet points
- End with a call to action: follow, clap, or a question to spark comments

---

### Step 4 — SEO titles & subtitles

After the article, suggest 3 title options:

```
## 💡 Title Options

1. [Punchy, keyword-rich title]
   Subtitle: [Supporting subtitle that adds context]

2. [Question-format title]
   Subtitle: [Supporting subtitle]

3. [How-to or numbered title]
   Subtitle: [Supporting subtitle]
```

**Java titles** → include relevant terms: Java, Spring Boot, JPA, Hibernate, Backend, REST API
**AI titles** → include relevant terms: Claude, AI tools, productivity, workflow, beginner

---

## Tone Guide

| | Java Articles | AI Articles |
|---|---|---|
| Tone | Practical, direct, peer-to-peer | Conversational, encouraging, accessible |
| Jargon | OK — explain non-obvious terms | Minimal — always define on first use |
| Code | Required | Only if very simple/illustrative |
| Length | 1200–2000 words | 800–1400 words |
| Voice | "Here's what I learned the hard way" | "Here's something that changed how I work" |

---

## Notes
- Firas publishes under @ferasama on Medium
- Existing article covers JPA N+1 problem (`@EntityGraph`, `@BatchSize`, `SUBSELECT`) — avoid duplicating
- For AI articles, assume the reader may be encountering Claude or AI tools for the first time
