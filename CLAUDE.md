# Lenny's Podcast -- LLM Knowledge Base

You are maintaining a knowledge base built from 303 episodes of Lenny's Podcast, stored as an Obsidian vault. The human rarely edits the wiki directly -- **you are the primary author**. Obsidian is the reader.

## Vault Structure

```
lennys-podcast-transcripts/
├── episodes/         # Raw source: 303 episode transcripts (guest-name/transcript.md)
├── index/            # Pre-built topic index (89 topic files)
├── wiki/             # LLM-compiled articles -- concept-based knowledge base (YOU write this)
│   └── _index.md     # Master index of all wiki articles (YOU maintain this)
├── journal/          # Daily notes (YYYY-MM-DD.md) -- scratch pad and inbox
├── output/           # Generated reports, slides, visualizations
├── assets/           # Images and binary attachments
├── templates/        # Note templates
└── scripts/          # Existing index build scripts
```

## Content Map

- **`episodes/`** = raw source material (Karpathy's `raw/`). 303 transcripts with YAML frontmatter (guest, title, youtube_url, publish_date, duration, keywords, etc.)
- **`index/`** = pre-existing topic index. Useful as a reference but shallow -- just lists episodes per keyword
- **`wiki/`** = YOUR compiled knowledge base. Concept articles that synthesize insights ACROSS episodes. This is where the real value lives.

## Your Roles

### 1. Compile Wiki (`episodes/` -> `wiki/`)

When asked to compile or when processing new episodes:
- Read transcripts from `episodes/` (use grep for targeted searches -- transcripts are large)
- Extract key frameworks, mental models, quotes, and actionable advice
- Create or update concept-based articles in `wiki/` that synthesize across multiple episodes
- Always update `wiki/_index.md` after changes

Wiki articles should:
- Have YAML frontmatter: `tags`, `created`, `updated`, `episodes` (list of guest names), `sources`
- Use `[[wikilinks]]` to cross-link related concepts
- Be organized by **concept**, not by episode -- multiple episodes merge into one article
- Include direct quotes with attribution (e.g. "-- Brian Chesky")
- Be factual and actionable -- no filler

### 2. Q&A

When the user asks a question:
- First consult `wiki/_index.md` and `index/` to find relevant topics/episodes
- Read the relevant wiki articles and episode transcripts
- Synthesize an answer with citations (guest name + episode)
- If the answer reveals a gap in the wiki, offer to create/update articles

### 3. Output (`output/`)

When generating reports, analyses, or visualizations:
- Save them to `output/` with descriptive names
- File key findings back into `wiki/` to compound knowledge

### 4. Lint & Health Checks

When asked to lint or audit:
- Check for inconsistent advice across articles
- Find concepts mentioned in transcripts but missing from `wiki/`
- Identify missing cross-links between related concepts
- Flag stale or incomplete articles
- Suggest new article candidates based on topic connections
- Update `wiki/_index.md` if it's out of sync

### 5. Journal Processing

When asked to process the journal:
- Read recent journal entries from `journal/`
- Extract questions, insights, and references
- Research answers across the transcript corpus
- File findings into `wiki/` articles

## Working with Large Transcript Files

Transcript files are large (often 25,000+ tokens). Use these strategies:

### 1. Use Grep for targeted searches (preferred)
```
Grep pattern="product.market fit" path="episodes/"
```

### 2. Read frontmatter first (lines 1-15)
```
Read file_path="episodes/guest-name/transcript.md" limit=15
```

### 3. Read in chunks when needed
```
Read file_path="..." offset=1 limit=500
Read file_path="..." offset=500 limit=500
```

### 4. Use Task tool with Explore agent
For research across multiple transcripts:
```
Task subagent_type="explore" prompt="Find insights about X across episodes/"
```

## Transcript Format

Each `episodes/{guest-name}/transcript.md` contains:
- **YAML frontmatter**: guest, title, youtube_url, video_id, publish_date, description, duration_seconds, duration, view_count, channel, keywords
- **Transcript content**: Timestamped speaker dialogue

## Obsidian CLI

You have access to the `obsidian` CLI to interact with the running Obsidian app:

```bash
obsidian read file="My Note"
obsidian create name="New Article" path="wiki/New Article.md" content="..." silent
obsidian append file="Daily Note" content="- New item"
obsidian search query="search term" limit=10
obsidian daily:read
obsidian daily:append content="- [ ] New task"
obsidian backlinks file="My Note"
obsidian tags sort=count counts
obsidian property:set name="status" value="done" file="My Note"
```

Use `vault="lennys-podcast-transcripts"` if targeting this vault specifically. Use `silent` flag when creating/modifying files to avoid disrupting the user's view.

## Conventions

- **Filenames**: Use descriptive names, spaces are fine (`Product-Market Fit.md`)
- **Tags**: Use lowercase, hyphenated (`#product-management`, `#growth-strategy`)
- **Dates**: ISO 8601 (`2026-04-07`)
- **Wiki links**: Always prefer `[[wikilinks]]` over markdown links for internal references
- **Images**: All images go to `assets/`
- **Attribution**: Always cite the guest and episode when quoting

## Workflow Summary

```
User asks question    -->  LLM searches episodes/ + wiki/  -->  answers + wiki update
User says "compile X" -->  LLM reads episodes/  -->  creates/updates wiki/ articles
User says "lint"      -->  LLM audits wiki/  -->  fixes + suggestions
User says "process journal"  -->  LLM reads journal/  -->  updates wiki/
```

## Rebuilding the Topic Index

```bash
./scripts/build-index.sh
```

This calls Claude CLI for each episode to generate keywords. The script is idempotent.

<!-- deepvista-auto-capture -->
## DeepVista Auto-Capture

When the user shares any of the following during a conversation, automatically save it to DeepVista notes using `deepvista notes +quick "<text>"` without asking for confirmation:

- Personal or professional facts (role, company, team, background)
- Decisions made or conclusions reached
- Key insights, learnings, or observations from podcast episodes
- Action items or commitments
- Notable quotes or frameworks discovered

Use `deepvista notes +quick` for single-line facts. For longer structured notes, use `deepvista notes create --title "..." --content "..."`.

If `deepvista` is not authenticated, prompt the user to run `deepvista auth login` before saving.
<!-- /deepvista-auto-capture -->
