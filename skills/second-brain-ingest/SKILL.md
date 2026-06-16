---
name: ingest
description: Ingests new files from Firas's my-second-brain Obsidian vault into the wiki knowledge base. Use this skill whenever Firas asks to process new articles, ingest raw sources, update the wiki, "check what's new in the vault", "add articles to the brain", "sync my raw sources", or anything about ingesting/processing files from raw-sources or my-notes into the wiki. Always trigger when the user says "ingest", "process new files", "update my wiki", "what's new to process", or asks about new raw sources — even if they don't say "second brain" explicitly.
---

# Ingest

Processes new files from the `my-second-brain` vault into the wiki knowledge base without touching the source files.

## Vault Location

`/home/firas/private-projects/claude/my-second-brain`

All paths below are relative to this root.

---

## Vault Architecture

Two **immutable** source layers feed the wiki (never modify these):
- `raw-sources/` — clipped articles (`.md` files; ignore `raw-sources/images/`)
- `my-notes/` — personal notes (`.md` files; ignore `my-notes/images/`)

The wiki lives in `wiki/` with three category sub-directories:

| Category | Topics | Sub-index |
|---|---|---|
| `java` | Java language, JVM, GC, streams, collections, OOP, design patterns, testing, concurrency | `wiki/java/index-java.md` |
| `spring-boot` | Spring Boot, Spring Framework, Maven, JPA/Hibernate, SQL, dependency injection | `wiki/spring-boot/index-spring-boot.md` |
| `claude` | Claude Code, CLAUDE.md, skills, slash commands, AI tooling, token limits, MCP | `wiki/claude/index-claude.md` |

The master `wiki/index.md` only holds category-level entries — don't add individual pages there unless creating a brand-new category.

Log lives at `wiki/log.md`.

---

## Gotchas

- **Log parsing is fuzzy, not exact.** The log uses prose, not a filename list. A file titled "HashMap Deep Dive" in the log may correspond to `hashmap-internals.md` in raw-sources — treat close title matches as already processed rather than risk duplicating.

- **Ambiguous category files.** A file covering both Spring Boot and JPA goes to `spring-boot`, not `java`. When in genuine doubt, pick the category whose sub-index has the closest existing page — that's where the merge will land anyway.

- **Orphan check false negatives.** A `[[wikilink]]` with a path prefix like `[[raw-sources/foo]]` does NOT satisfy the orphan check — only bare `[[foo]]` entries in index files count. Do not manually eyeball this; always run the script.

- **"Not ingested" files still need index entries.** Rejected files (low-signal, duplicates) must get a `[[stem]]` entry in the appropriate category index's raw-sources section, or they will show as orphans on the next run.

- **Append-only for wiki pages.** Never restructure or reorder existing content when merging — only append a new demarcated section at the bottom. Reorganizing existing content causes silent content loss that won't be caught by the orphan check.

---

## Ingestion Steps

### Step 1 — Parse `wiki/log.md` to find already-processed files

Read `wiki/log.md` in full. The log is prose-based (not a filename list), so extract processed filenames by scanning for:
- Quoted article titles inside ingestion entries (e.g., `"HashMap Deep Dive"`)
- `.md` filenames explicitly referenced in log entries
- Source filenames mentioned under "Sources ingested" or "Sources migrated" headings

Build a set of already-processed filenames (without path prefix). When matching against `raw-sources/` filenames, be conservative: if a log title closely corresponds to a raw-sources filename, treat it as processed. Flag ambiguous cases rather than risk duplicating work.

### Step 2 — Scan source directories (filenames only, no contents yet)

List all `.md` files in:
- `raw-sources/` — exclude `raw-sources/images/`
- `my-notes/` — exclude `my-notes/images/`

### Step 3 — Diff: identify new/unprocessed files

Candidates = source files NOT found in the log's processed set.

### Step 4 — Early exit if nothing new

Report: **"Nothing to process — all files in raw-sources/ and my-notes/ are already reflected in the wiki."** Stop.

### Step 5 — Process each new file (one at a time, content loaded on demand)

#### 5a. Read the file content.

#### 5b. Infer the category.

Assign exactly one of `java`, `spring-boot`, or `claude` based on the dominant topic. For mixed content, pick the category that covers the majority of new concepts.

#### 5c. Load the category sub-index.

Read `wiki/<category>/index-<category>.md` to see what pages already exist and what they cover. This is your merge-vs-create decision map.

#### 5d. Extract key concepts and takeaways.

Identify the 3–7 most important ideas that are genuinely new or additive. Skip content that is:
- Already fully covered in an existing wiki page
- Purely promotional, motivational, or experience-narrative with no technical depth
- A near-duplicate of another already-ingested source

If nothing is genuinely new, mark the file as "not ingested" with a clear reason.

#### 5e. Decide: merge into existing page or create a new page.

**Merge** when:
- Content adds depth, new examples, or additional angles to a topic already covered
- New content is too thin for a standalone page (<30% of a normal page's scope)
- Topic is a subtopic or extension of an existing page

**Create new page** when:
- Content introduces a distinct major topic not covered anywhere
- Content is substantial enough to stand alone as a reference

Multiple raw sources may share one wiki page (synthesize them together, don't create one page per article).

#### 5f. Write or update the wiki page.

**Place new pages at:** `wiki/<category>/<kebab-case-topic-name>.md`

**New page format:**
```markdown
# Topic Name

Brief description (1–2 sentences).

(source: <raw-sources-filename.md>)

---

## Section 1

...

## See Also
- [[related-page]]
- [[another-related-page]]
```

**When merging:** Append a clearly demarcated section at the end of the existing page. Never overwrite or restructure existing content — append only.

Use `[[topic-name]]` Obsidian wikilinks for internal cross-references. For cross-category links: `[[spring-boot/spring-boot-architecture]]`.

Cite sources inline as `(source: <filename>)`.

---

### Step 6 — Update the category sub-index

After all files in a category are processed, update `wiki/<category>/index-<category>.md`:
- Add newly created pages to the Topics table with a one-line description
- Add **every processed raw-source file** to the raw sources section — including files marked "not ingested". Every file in `raw-sources/` and `my-notes/` must have a `[[wikilink]]` entry in the appropriate category index. This is the only way the orphan check (Step 8) will pass.
- Use the exact filename stem (without `.md`) as the wikilink text — no `raw-sources/` path prefix

**Do not modify** `wiki/index.md` (master) unless you've added a brand-new category.

---

### Step 7 — Append to `wiki/log.md`

Follow the established rich log format. Include all files ingested AND all files explicitly rejected (with reason).

Follow the established rich log format exactly. New entries go at the **top** of the log (most recent first), immediately after the `---` separator:

```markdown
## [YYYY-MM-DD] ingest | batch | <brief description>

**Sources ingested (<N> new files):**

1. "<Title>" — <source-domain>, <date> — <1-line summary of what it covers>
2. ...

*Not ingested (low-signal/reason):*
- "<Title>" — <reason>

**Wiki pages created (<N>):**
- `wiki/<category>/<page>.md` — <what it covers>

**Wiki pages updated (<N>):**
- `wiki/<category>/<page>.md` — <what was added>

**Editorial decisions:**
- <key synthesis choices and why>
```

### Step 8 — Mandatory Orphan Check (do not skip)

After updating all indexes, run this exact Python script via Bash to detect orphans:

```python
import os, re

raw_sources_dir = '/home/firas/private-projects/claude/my-second-brain/raw-sources'
wiki_dir = '/home/firas/private-projects/claude/my-second-brain/wiki'

raw_files = [f for f in os.listdir(raw_sources_dir) if f.endswith('.md') and 'images' not in f]

index_files = [
    os.path.join(wiki_dir, 'java/index-java.md'),
    os.path.join(wiki_dir, 'spring-boot/index-spring-boot.md'),
    os.path.join(wiki_dir, 'claude/index-claude.md'),
    os.path.join(wiki_dir, 'index.md'),
]
index_content = ''
for p in index_files:
    try:
        with open(p, 'r', encoding='utf-8') as f:
            index_content += f.read() + '\n'
    except:
        pass

wikilinks = set(re.findall(r'\[\[([^\]]+)\]\]', index_content))

orphans = [f for f in raw_files if f[:-3] not in wikilinks]
print(f"Orphans: {len(orphans)}")
for f in sorted(orphans):
    print(f"  {f}")
```

**Rules:**
- A file is an **orphan** if its stem (filename without `.md`) does not appear as a bare `[[wikilink]]` in any of the four index files.
- Mentions in `log.md`, wiki pages, or wikilinks with a path prefix (`[[raw-sources/foo]]`) do NOT count — only bare `[[stem]]` entries in index files count.
- If orphan count > 0: add the missing `[[stem]]` entries to the correct category index raw sources section, then re-run the script. Repeat until the script reports **Orphans: 0**.
- **Do not report completion until the script outputs `Orphans: 0`.**

---

## Step 9 — Push and Create a PR (mandatory, do not skip)

After the orphan check confirms `Orphans: 0`:

1. **Create a feature branch** named `task-ingest-DD-MM-YY` (e.g. `task-ingest-22-05-26`) and commit all changes:
   ```bash
   git checkout -b task-ingest-DD-MM-YY
   git add <all changed/new wiki files and my-notes files — never .obsidian/>
   git commit -m "ingested new content - DD-MM-YY"
   ```

2. **Push the branch** to origin:
   ```bash
   git push -u origin task-ingest-DD-MM-YY
   ```

3. **Create a PR against `main`** using `gh pr create`:
   ```bash
   gh pr create --title "ingested new content - DD-MM-YY" --body "..."
   ```
   PR body must include: sources processed, wiki pages created/updated, and a note that orphan check passed (`Orphans: 0`).

4. **Return the PR URL** in the output summary.

If `gh` is not authenticated, stop and tell the user: *"gh is not authenticated — please run `! gh auth login --web` in the prompt, then say 'try again'."* Do not silently skip this step.

---

## Output Summary

After completing all steps (including PR creation and a confirmed `Orphans: 0` result), report:

```
✅ Processed: <N> new files
📄 Wiki pages created: <N> — [page-name, page-name, ...]
✏️  Wiki pages updated: <N> — [page-name, page-name, ...]
⏭️  Not ingested: <N> — [reason summary]
🔗 PR: <url>
```

---

## Hard Constraints

- **Never modify** `raw-sources/` or `my-notes/` — read-only source layers
- **Never overwrite** wiki pages — append or merge only
- **Always do category routing** — load the sub-index before deciding where content goes; never dump files directly in `wiki/`
- **Never add individual pages to master `wiki/index.md`** — only category-level entries belong there
- **Log format must match** the established rich style — not a one-liner; include sources, pages created/updated, and editorial decisions
- **Follow `CLAUDE.md`** schema (read it at the vault root if you are uncertain about any convention)
- **One wiki page per major topic**, not one per article — synthesize and merge wherever possible
- **Every raw-sources file must have a `[[wikilink]]` in a category index** — even files not ingested (low-signal/rejected). Register them in the raw sources section of the most appropriate index. Never leave a file unregistered.
- **Run the Step 8 orphan script and confirm `Orphans: 0` before reporting completion** — do not trust manual inspection or substring searches
