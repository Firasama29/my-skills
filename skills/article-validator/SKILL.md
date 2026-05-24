---
name: article-validator
description: Validates technical articles for factual accuracy by cross-checking claims against official documentation and legitimate online sources. Use this skill whenever the user asks to validate, fact-check, review, or verify an article, blog post, or draft — even if they phrase it casually like "check this article", "is this accurate?", "fact-check my draft", or "verify the claims in this". Triggers on any request to review content for correctness, accuracy, or truthfulness. Covers Java, Spring Boot, AI/LLM, and Claude-related technical content. Always use this skill before responding to such requests — do not rely on memory alone.
---

# Article Validator Skill

Validate technical article claims against official documentation and legitimate internet sources. The goal is strict, thorough fact-checking — suitable for pre-publication review or learning from external content.

## Scope

Covers articles about:
- Java (core language, JVM, concurrency, collections, JPA/Hibernate, Spring Boot)
- AI/LLM concepts (transformers, embeddings, RAG, fine-tuning, prompt engineering)
- Claude and Anthropic products (Claude API, Claude Code, MCP, model capabilities)

---

## Workflow

### Step 1 — Parse the Article

Read the full article and extract every claim that is:
- A factual statement about how something works
- A technical assertion (e.g. "Spring Boot auto-configures X when Y is on the classpath")
- A version-specific claim (e.g. "This was introduced in Java 17")
- A behavioral claim about a tool, library, or API
- A best practice stated as fact

Ignore: opinions, stylistic choices, introductory fluff.

---

### Step 2 — Verify Each Claim

For **every extracted claim**, do the following:

1. **Check your own knowledge first** — do not guess; only proceed if confident
2. **Always web_search to verify** — do not rely on memory alone, even for familiar topics
   - Prioritize: official docs, GitHub repos, release notes, authoritative blogs (Baeldung for Java, Anthropic docs for Claude)
   - For Java: check [docs.oracle.com](https://docs.oracle.com), Spring docs, JPA spec
   - For Claude/Anthropic: check [docs.anthropic.com](https://docs.anthropic.com)
3. **Classify each claim** as one of:
   - ✅ **Correct** — verified by source
   - ❌ **Incorrect** — contradicted by source; provide correction
   - ⚠️ **Outdated** — was true but no longer accurate; provide current info
   - ❓ **Unverifiable** — no official or reliable source found to confirm or deny

Only surface ❌, ⚠️, and ❓ claims in the output. Do not list ✅ claims unless the user asks.

---

### Step 3 — Present Findings

Use this format exactly:

---

**Validation Results**

*X claims checked. Y issues found.*

**Issues Found:**

1. ❌ **[Short label for the claim]**
   - **Original:** [quote or close paraphrase of the claim]
   - **Correction:** [accurate version, with source linked]

2. ⚠️ **[Short label]**
   - **Original:** [claim]
   - **Update:** [what it should say now, with source]

3. ❓ **[Short label]**
   - **Claim:** [the claim]
   - **Status:** Could not be verified against official documentation or reliable sources.

---

If no issues are found:

> ✅ All checked claims appear accurate based on official documentation and available sources.

---

### Step 4 — Offer Updated Article

After presenting findings, always ask:

> "Would you like me to produce the corrected version of the article with all issues fixed?"

If the user says yes — rewrite only the affected sentences/sections. Keep the author's original voice, structure, and formatting intact. Do not rewrite sections that were correct.

---

## Rules

- **Never skip web search** — always verify, even for claims you're confident about
- **Be strict** — flag anything that is wrong, outdated, or unverifiable
- **Cite sources** — every correction must link to the source that confirms it
- **Preserve voice** — when rewriting, match the author's tone and style
- **No hallucinated corrections** — if you can't find a reliable source for a correction, mark it ❓ instead of guessing
