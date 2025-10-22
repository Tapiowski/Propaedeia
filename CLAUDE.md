# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Propaedeia** is an automated medical study pipeline for Claude that transforms medical lecture transcripts (sbobine) into structured study materials:
- **Notion pages** with clinical callouts, Mermaid diagrams, and integrated questions
- **Anki decks** (max 25 CORE cards) with anti-confusion patterns
- **Database properties** extracted across 4 categories (Etiology, Clinical, Diagnosis, Therapy)
- **Automatic linking** between related topics

The system is designed to run on **Claude Web Projects** (browser-based, no local code execution) and enforces **CCI criteria** (Clinical Clarity Integration): sentences ≤18 words, main message first, active voice, numbers with units.

## Core Architecture

### Workflow Engine: orchestrator.txt (~1180 lines)

The orchestrator is the **execution engine** that Claude reads and follows. It defines a 7-phase workflow organized into 3 groups with mandatory pauses:

**Group A - Notion Content** (~35-50 min, auto-executes Phases 0-5):
- **Phase 0** (multi-doc only): Analyzes 2+ documents on the same topic, identifies overlap/complementary/conflicts, generates weighted integration plan
- **Phase 1**: Creates H2/H3 outline (integrated if multi-doc)
- **Phase 2**: Generates page directly on Notion with 5-7 callouts (internal CCI validation, no chat output)
- **Phase 3**: Inserts Mermaid diagram via append_blocks
- **Phase 4**: Updates Pitch property (170-200 words with **bold** Markdown) + Status
- **Phase 5**: Calculates Complexity + Study Time, updates properties
- **[Pause A]**: Waits for user "continue" or "stop"

**Group B - Anki** (~5-8 min, after "continue"):
- **Phase 6**: Generates `[topic_name]_anki.txt` file (max 25 CORE cards with anti-confusers)
- **[Pause B]**: Waits for user "continue" or "stop"

**Group C - Database** (~3-5 min, after "continue"):
- **Phase 7**: Extracts terms (2-3 per category), batch processes DB Voci, creates bidirectional relations
- **Workflow complete**

### Multi-Document Integration (v4.7)

When 2+ documents on the same topic are detected:
1. **Phase 0 auto-starts**: Analyzes scope coverage per source (Etiology, Clinical, Diagnosis, Therapy scored 0-3)
2. **Identifies patterns**:
   - **Overlap**: aspects covered by 2+ sources
   - **Complementary**: unique aspects per source (e.g., surgical techniques from Plastic Surgery)
   - **Conflicts**: discrepancies resolved automatically (priority to specialized source)
3. **Generates weights**: proportional content development per H2 (e.g., "75% Plastics, 25% Dermatology" for Surgical Therapy section)
4. **Single integrated outline**: Phase 1 creates unified H2/H3 structure including ALL content from both sources
5. **Weighted content generation**: Phase 2 develops each H2 according to source weights, resolves conflicts, signals resolved conflicts in RED callouts

Example: melanoma from Plastic Surgery (reconstructive focus) + Dermatology (clinical/staging focus) → single complete Notion page.

### Scratchpad System

The orchestrator uses an internal state tracker (`workflow_state`) to:
- Track current phase and completed phases
- Store multi-doc integration plan (weights, conflicts, overlap)
- Save outputs (page_id, URLs, extracted terms)
- Monitor performance (API calls, cache hits, retries)
- Validate prerequisites before each phase

### CCI Enforcement (Clinical Clarity Integration)

Applied automatically during Phase 2 content generation:
1. **Main message first**: Each H2 opens with 1-2 key sentences
2. **Sentence limit**: ≤18 words (strict threshold, not average)
3. **Numbers with units**: All quantitative data has explicit units ("5 days", NOT "5")
4. **Active voice**: Operations use imperative/infinitive verbs
5. **Incremental validation**: Auto-check after each H2 (if ≥2 fail → auto-correct)
6. **Final quick check**: If >20% sentences >18 words → STOP

### Cognitive Limits

| Element | Range | Rationale |
|---------|-------|-----------|
| **Callouts** | 5-7 | Working memory capacity |
| **H2 Pillars** | 4-6 | Optimal chunking |
| **Questions** | 5-7 | Spaced retrieval |
| **Anki CORE** | max 25 | High-yield essentials only |
| **Paragraphs** | 2-4 sentences | Readability |
| **Sentences** | ≤18 words | Sentence complexity (strict threshold) |
| **Pitch** | 170-200 words | Elevator pitch standard |
| **Diagram nodes** | ≤11 | Visual clarity |

## Notion Integration

### Database IDs

**Argomenti (Topics)**:
- ID: `1b5282519c2c80a68c37ce5e4bd56f22`
- Data Source: `collection://1b528251-9c2c-8065-a61e-000bfdfab7c7`
- Search property: **"Nome"** (NOT "title")

**Voci (Terms)**:
- ID: `290282519c2c801ea214d30b803c78f8`
- Data Source: `collection://29028251-9c2c-8024-bd71-000bcc303592`
- Property "Categoria": multi_select `["Eziologia", "Clinica", "Diagnosi", "Terapia"]`

### API Format (CRITICAL)

**Relations must be JSON strings** (not arrays):
```json
✅ CORRECT:
"Voci": "[\"https://notion.so/page1\", \"https://notion.so/page2\"]"

❌ WRONG:
"Voci": ["https://notion.so/page1"]
"Voci": ["{{https://notion.so/page1}}"]  // Double braces only in Notion output
```

**notion-update-page requires "data" wrapper** (v4.8 fix):
```json
{
  "data": {
    "page_id": "[ID]",
    "command": "update_properties",
    "properties": {
      "Voci": "[\"url1\", \"url2\"]"
    }
  }
}
```

### Notion Markdown Format

**Toggle headers**: Use ▶ symbol (U+25B6)
```markdown
▶## H2 Title
	Content with 1 TAB (level 1 indentation)

	▶### H3 Title
		Content with 2 TAB (level 2 indentation, NO blank line after H3)
```

**Indentation**: Use TAB (U+0009), NOT spaces
- H2: 0 TAB before ▶##
- H3: 1 TAB before ▶### (for nesting in H2)
- H2 content: 1 TAB
- H3 content: 2 TAB

**Callouts** (5-7 total, distributed across H2s):
```markdown
<callout icon="/icons/warning_red.svg" color="red_bg">
<span color="red">Key content with **bold** on specific keywords</span>
</callout>
```

Types:
- RED (warning_red.svg): Critical warnings, contraindications, resolved conflicts
- BLUE (light-bulb_blue.svg): Pathophysiological principles, mechanisms, paradoxes
- GREEN (star_green.svg): High-yield facts, criteria, threshold values

**Bold in callouts**: ONLY on specific content keywords, NEVER on standard titles like "**Warning**:" or "**High-yield**:"
- ✅ Correct: "in **pregnancy**", "value **>200 mg/dL**", "**paradox**"
- ❌ Wrong: "**Warning**: text", "**High-yield**: fact"

**Pitch formatting**: Plain text with ONE bold sentence using Markdown `**text**` (Notion preserves ** ** in text property)

## MedGraph Diagrams (Phase 3)

**Palette** (B/W + single accent):
- Background: `#FFFFFF`
- Text/lines: `#000000`
- **Single accent**: `#00E0CC` (ONE critical step/path only, never decorative)

**Constraints**:
- Max nodes: ≤11
- Label: concise ≤4 words
- No crossed branches
- Contrast: text ≥4.5:1, connectors ≥3:1
- Grayscale-readable (print-ready)

**Types** (auto-select):
- Flowchart: diagnostic/therapeutic algorithms (default TB; LR if horizontal branches)
- Timeline: disease progression (prefer LR)
- Mindmap: pathophysiological connections (depth ≤2)
- Sankey: cumulative complication progression (use only if clearly useful)

## Commands

**Workflow**:
```bash
workflow completo  # Phases 0-7 (~45-60 min) with 3 mandatory pauses
essenziale        # Phases 1-2 only (outline + page)
continua          # Proceed to next group (after pause)
ferma             # Stop workflow
status            # Show scratchpad state
```

**Link** (separate from workflow, NOT part of phases):
```bash
link auto [topic]         # Find related topics via Voci overlap (score ≥2)
link compare [t1] [t2]    # Create differential comparison page
```

**Optional parameters** (insert BEFORE command):
```bash
n=30              # Override Anki limit (default: max 25)
focus=diagnosis   # Restrict scope to specific section
```

## Auto-Start Behavior

**Single document**: When user uploads a single transcript without commands → auto-start "workflow completo" at Phase 1

**Multiple documents (same topic)**: When user uploads 2+ documents on same topic → auto-start Phase 0 (multi-doc integration analysis)

## Performance Optimizations

**v4.8** (current): Direct Notion output + API fixes
- ALL content generated INTERNALLY (no chat output for page/diagram/pitch/calculations)
- Group A output: minimal confirmation + URL + stats
- Dynamic Anki filename: `[topic_name]_anki.txt` (e.g., "Sifilide_anki.txt")
- Reduced chat tokens ~70% (from ~2000 to ~100 words)

**v4.7**: Multi-document integration
- Phase 0 analyzes scope, generates weighted integration plan
- Single integrated outline from multiple sources
- Proportional content development per source expertise
- Auto-conflict resolution with RED callout signaling

**v4.5**: Simplified workflow
- 7 phases (from 9): more linear workflow
- Combined Phase 2: page + callouts in single step
- Internal CCI validation (no simulation)
- Total time ~50-60 min (from ~55-65 min v4.4)

**Batch processing** (Phase 7):
- Search → create → update in batches
- Cache common terms (hit rate 20-30%)
- -80% API calls vs individual processing
- Phase 7 time: 3-5 min (from 8-12 min v4.0)

**Retry logic**:
- Timeout: 3x with backoff (1s, 2s, 4s)
- Rate limit 429: 3x with backoff
- Server 500+: 2x with backoff (2s)
- Network errors: 3x with backoff (1s)
- Rate limiting: 350ms between consecutive calls

## Anki Anti-Confusers (Phase 6)

Systematic patterns to avoid card interference:

1. **Age/population**: "in the *newborn*" vs "in *adult* >65 years"
2. **Temporality**: "*acute* phase (<72h)" vs "*chronic* phase (>3 months)"
3. **Severity/type**: "*intermittent* asthma" vs "*severe persistent* asthma"
4. **Location**: "*dominant hemisphere* stroke"
5. **Clinical context**: "in *absence of renal failure*"

Format: 1 card/line with `{{c1::answer}}` (NEVER c2, c3, c4)

## Development Workflow

This is a **documentation-only project** - no build/test/lint commands. The workflow is executed entirely by Claude reading the orchestrator.txt file in Claude Web Projects.

**To work on this codebase**:

1. **Modify orchestrator.txt**: This is the execution engine - changes here affect workflow behavior
2. **Update docs/**: Keep architecture.md, workflow.md, technical.md synchronized with orchestrator changes
3. **Test in Claude Web**: Upload orchestrator.txt to a Claude Web Project with custom instructions and test with sample transcripts
4. **Version tracking**: Update changelog.md with version number and changes (format: v4.X)

**Version structure**:
- Major (v4.x): Workflow restructuring (phase count/order changes)
- Minor (v4.x.y): Feature additions (multi-doc, optimizations)
- Patch: Bug fixes, format corrections

**Current version**: v4.8 (Direct Notion output + API wrapper fix)

## File Organization

```
.
├── orchestrator.txt          # Main workflow engine (~1180 lines)
├── README.md                 # User-facing overview (70 lines, -87% from v4.6)
├── docs/
│   ├── workflow.md          # Phase 0-7 detailed specs
│   ├── architecture.md      # Files, databases, CCI, cognitive limits
│   ├── examples.md          # Complete scenarios (single/multi-doc)
│   ├── changelog.md         # Versions v1.0-v4.8
│   ├── technical.md         # API, performance, formats
│   ├── claude_web_project.md # Setup guide for Claude Web
│   └── ottimizzazioni_claude_web.md # Optimizations notes
├── .claudeignore            # Exclude unnecessary files
└── .gitignore               # Git exclusions
```

**NOT code files**: orchestrator.txt is a prompt/instruction file for Claude, not executable code. It's designed to be read and followed by Claude in a browser-based environment.

## Critical Implementation Notes

### Phase 2 Search (v4.6.1 fix)
Search existing page using **"Nome" property** (NOT "title"):
```json
{
  "Nome": "[Topic]",
  "Argomento primario": true
}
```

### Phase 2-5 Output (v4.8)
Generate ALL content INTERNALLY:
- NO output for page content, callouts, diagram code, pitch text, calculations
- ONLY minimal confirmation messages after Notion update
- Benefits: cleaner UX, ~70% fewer chat tokens, everything on Notion except downloadable Anki file

### Phase 6 Anki Filename (v4.8)
Dynamic naming from topic title:
- Format: `[topic_name]_anki.txt`
- Replace spaces with underscores
- Examples: "Sifilide_anki.txt", "Melanoma_cutaneo_anki.txt"

### Link Command (v4.6.2)
REMOVED from workflow phases (no longer Phase 8/optional):
- Separate command invoked only on explicit user request
- Two modes: auto (find correlations) + compare (create differential page)
- Not part of 7-phase workflow completion

## Common Pitfalls

1. **Using arrays for relations**: Must be JSON string `"[\"url1\", \"url2\"]"`, not array `["url1"]`
2. **Missing "data" wrapper**: v4.8 requires `{"data": {...}}` wrapper for notion-update-page
3. **Wrong property name**: Search uses "Nome", not "title"
4. **Spaces instead of TABs**: Notion format requires TAB indentation, NOT spaces
5. **Wrong toggle symbol**: Use ▶ (U+25B6), not `>` (quote syntax)
6. **Callout bold titles**: Bold ONLY specific keywords, NOT standard titles like "**Warning**:"
7. **Stopping at pauses**: Workflow has 3 MANDATORY pauses (after Phase 5, 6, 7) - wait for user "continue"
8. **Outputting content in chat**: v4.8 generates internally - NO chat output for page/diagram/pitch/calculations
9. **Multiple accents in diagrams**: Use #00E0CC for ONE critical step only, rest stays B/W
10. **Skipping Phase 0**: Multi-doc requires Phase 0 analysis before Phase 1 outline

## Platform Notes

**Environment**: Claude Web Projects (browser-based)
- No local code execution
- No package dependencies
- Orchestrator.txt uploaded to project Files
- Custom instructions contain CCI rules + cognitive limits
- Notion API accessed via Claude's built-in integration

**Testing**: Upload orchestrator.txt + sample medical transcript to Claude Web Project and invoke commands.
