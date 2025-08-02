# PKM Specification

This document provides the authoritative operational guidelines for the Edie Personal Knowledge Management (PKM) system. It consolidates all instructions, rules, and examples for managing the PKM.

**Important**: This specification focuses on operational consistency - the tactical "how to" of PKM operations. For vision, philosophy, and deeper purpose, see `/self/projects/edie/`.

## Table of Contents

1. [Introduction](#introduction)
2. [System Standards](#system-standards)
3. [Entity Management](#entity-management)
4. [Linking & References](#linking--references)
5. [Email Processing](#email-processing)
6. [Handbooks](#handbooks)
7. [Debug Report Generation](#debug-report-generation)
8. [Git Operations](#git-operations)
9. [Maintenance & Updates](#maintenance--updates)
10. [Quick Reference](#quick-reference)

## Introduction

The Edie PKM is a personal knowledge management system designed to be managed entirely by an LLM. It stores interconnected markdown files representing people, projects, daily logs, and areas of life.

### Core Principles

1. **Atomicity**: Each file represents a single entity
2. **Linkability**: Entities connect via `[[wikilink]]` syntax
3. **Machine Readability**: Consistent structure for LLM parsing

## System Standards

### Directory Structure

**IMPORTANT**: All PKM directories MUST be located under the `/self/` directory. This is the root of the personal knowledge management system.

```
/self/                          # Root of PKM - ALL PKM content goes here
├── about-me.md                 # Central biographical information
├── daily/                      # Daily entries (YYYY-MM-DD-HST.md)
├── people/                     # Individual files (kebab-case-name.md)
├── places/                     # Locations and venues (kebab-case-place.md)
├── organizations/              # Companies, non-profits, groups (kebab-case-org.md)
├── animals/                    # Animals and pets (kebab-case-name.md)
├── items/                      # Physical possessions (can have subdirectories)
├── projects/                   # Project directories with index.md (have defined endpoints)
├── areas/                      # Ongoing life responsibilities (no endpoint)
├── handbooks/                  # Comprehensive guides to different domains
├── _inbox/                     # Email processing queue
│   ├── pending/               # Emails waiting to be processed
│   ├── processing/            # Emails currently being processed
│   ├── processed/             # Successfully processed emails
│   ├── sent/                  # Sent email records
│   └── errors/                # Failed processing attempts
├── _media/                     # Non-markdown files (images, PDFs, etc.)
├── _debug/                     # Debug reports for all PKM changes
│   └── YYYY-MM-DD/            # Daily debug report directories
└── archive/                    # Completed/inactive content
```

### File Naming Conventions

| Directory | Pattern | Example |
|-----------|---------|---------|
| `/self/` | `about-me.md` | `about-me.md` |
| `/daily/` | `YYYY-MM-DD-HST.md` | `2025-07-13-HST.md` |
| `/people/` | `kebab-case-full-name.md` | `jane-doe.md` |
| `/places/` | `kebab-case-place-name.md` | `hawaii-prince-golf-course.md` |
| `/organizations/` | `kebab-case-org-name.md` | `zaarly.md` |
| `/animals/` | `kebab-case-name.md` | `leah-the-dog.md` |
| `/items/` | `kebab-case-item-name.md` | `ducati-monster-796.md` |
| `/projects/` | `kebab-case-name/index.md` | `shield-wall-ramp/index.md` |
| `/areas/` | `kebab-case-area/index.md` | `nutrition/index.md` |
| `/handbooks/` | `domain-name/section.md` | `software-engineering/debugging.md` |

### Frontmatter Template

```yaml
---
title: {Full Title}
aliases: [{Alias1}, {Alias2}]  # Optional
tags: [{tag1}, {tag2}]          # Optional
created: YYYY-MM-DD             # Required
updated: YYYY-MM-DD             # Optional, update on changes
---
```

### Time & Date Standards

**All dates and times use Hawaii Standard Time (HST, UTC-10)**

**CRITICAL**: Always check current date/time with `date` command before:
- Creating daily log files
- Adding timestamps to entries
- Updating `created` or `updated` fields
- Processing emails

**Timestamps should reflect when the user mentioned it**:
- For conversational entries: Check via the `date` bash command
- For emails: Use the timestamp when the email was sent (converted to HST)

Format standards:
- Daily file names: `YYYY-MM-DD-HST.md`
- Daily entry headers: `## h:MM AM/PM - Entry Title` (12-hour format)
- UTC display: Always mark UTC times clearly (e.g., "2:05 AM UTC" or "02:05Z")

## Entity Management

### Creating Entities

When any entity (person, place, organization, animal, item, project) is mentioned:

1. **Check current date** with `date` command
2. Check if entity already exists (by title or aliases)
3. If new, create file in appropriate directory
4. Add required frontmatter with correct `created` date
5. Include basic description
6. Commit with: `CREATE({type}): Add {name}`

Example:
```bash
# Check if person exists
ls self/people/ | grep -i "jane"

# Create new person
cat > self/people/jane-doe.md << 'EOF'
---
title: Jane Doe
aliases: [Jane]
tags: [friend, colleague]
created: 2025-07-17
---

## Overview

Jane is a colleague from the organic farms project.

## Log

- **2025-07-17**: Met Jane at coffee shop to discuss project.
EOF

git add self/people/jane-doe.md
git commit -m "CREATE(people): Add Jane Doe"
```

### Entity Tags

**People tags** (can combine multiple):
- Personal: `friend`, `family`, `acquaintance`, `neighbor`
- Professional: `coworker`, `colleague`, `client`, `mentor`
- Public figures: `public-figure`, `artist`, `author`, `celebrity`
- Descriptors: `inspiration`, `hero`, `role-model`

**Place requirements**:
- Include city, state/country
- Add Google Maps link (search if needed)
- Format: `- **Google Maps**: https://maps.google.com/?q=Place+Name`

**Organization tags** (can combine multiple):
- Business: `company`, `startup`, `corporation`, `business`
- Non-profit: `nonprofit`, `charity`, `foundation`, `ngo`
- Community: `club`, `group`, `association`, `society`
- Education: `school`, `university`, `institute`
- Other: `government`, `agency`, `organization`

### Areas vs Projects

**Areas** represent ongoing life responsibilities without endpoints:
- Continuous maintenance or improvement
- No "completion" state
- Examples: nutrition, fitness, home-maintenance, finances, relationships

**Projects** have defined goals and endpoints:
- Specific outcome to achieve
- Can be marked complete
- Examples: Shield Wall Ramp, website redesign, learn Spanish

### Updating Entities

When content contains `[[wikilinks]]`, evaluate if the linked entity should be updated:

**Update when**:
- Significant action occurs
- New information learned
- Media/content shared
- First occurrence of something
- Milestone reached
- Goals or objectives are mentioned for the project

**Don't update when**:
- Routine mention
- Repeated activity
- Passing reference

**If updating**:
1. Add dated entry to entity's Log section
2. Include back-reference: "See [[daily/YYYY-MM-DD-HST]]"
3. Update `updated` timestamp
4. Commit separately: `UPDATE({type}/{name}): {description}`

### Contextual Inference from Relational Language

**REQUIRED**: For EVERY user input, analyze language for biographical implications about the user.

**Keywords indicating shared characteristics**:
- "fellow" (e.g., "fellow YC founder" → user is also a YC founder)
- "also a/an" (e.g., "she's also a developer" → user is a developer)
- "another" (e.g., "met another engineer" → user is an engineer)
- "like me" (e.g., "a founder like me" → user is a founder)
- "we both" (e.g., "we both went to Stanford" → user went to Stanford)
- "as a" when comparing (e.g., "as a fellow parent" → user is a parent)

**Keywords indicating contrasting characteristics**:
- "unlike me" (e.g., "unlike me she doesn't live in Waimanalo" → user lives in Waimanalo)
- "whereas I" (e.g., "he works remotely whereas I go to the office" → user works in office)
- "but I" (e.g., "she's vegetarian but I eat meat" → user eats meat)
- "while I" (e.g., "he sleeps late while I wake up early" → user wakes early)
- Negative contrasts (e.g., "she doesn't have kids" when discussing shared experiences → implies user has kids)

**Process (ALWAYS execute for every user input)**:
1. Scan for ANY biographical implications (not just keywords)
2. Extract the implied characteristic about the user
3. ALWAYS check if this information exists in `/self/about-me.md`
4. If missing, update the appropriate section
5. If present, note in debug report that check was performed but no update needed
6. Create debug report documenting the inference check (whether updated or not)

**Examples**:
- User: "Unlike me, Holly doesn't live in Waimanalo"
  - Inference: User lives in Waimanalo
  - Action: Check `/self/about-me.md` for location
  - Result: Already present, no update needed but check documented

- User: "Met another YC founder today"
  - Inference: User is a YC founder
  - Action: Check `/self/about-me.md` for YC affiliation
  - Result: If missing, add to appropriate section

### Updating Personal Biography

When new biographical information about the user is discovered:

**Sources of biographical information**:
- Direct statements ("I am a...")
- Contextual inference ("fellow...", "also...")
- Professional relationships implying roles
- Educational or work history mentions
- Achievements or affiliations mentioned in passing

**Update process**:
1. Check relevant section in `/self/about-me.md`
2. Add information if not present
3. Update `updated` timestamp
4. Commit with: `UPDATE(about-me): Add {discovered information}`
5. Include back-reference to source: "Discovered from [[daily/YYYY-MM-DD-HST]]"

## Linking & References

### Wikilink Syntax

- Use `[[Entity Name]]` to link to any entity
- Match against `title` or `aliases` in frontmatter
- Can use pipe for display text: `[[jane-doe|Jane]]`

### Media Embedding

- Store all media in `/self/_media/`
- **Always use absolute paths**: `/self/_media/filename.ext`
- Images: `![Description](/self/_media/image.jpg)`
- Documents: `[Document name](/self/_media/document.pdf)`

**When processing input with media that updates daily logs**:
- Media files must be both saved to `_media/` AND embedded in the daily log entry
- Include all images using markdown syntax: `![Description](/self/_media/filename.jpg)`
- Position media embeds logically within the entry text
- Use descriptive alt text that explains what's shown in the image

## Email Processing

### Workflow

1. **Check current date**: `date`
2. Check pending: `ls -la self/_inbox/pending/`
3. Move to processing: `mv self/_inbox/pending/{id}/ self/_inbox/processing/`
4. Read email content and **view all image attachments**
5. Create/update entities based on content AND visual analysis
6. Move attachments to `_media/` with descriptive names
7. Create `result.json` with processing summary
8. Move to processed: `mv self/_inbox/processing/{id}/ self/_inbox/processed/YYYY-MM-DD-HST/`

### Email Rules

- Convert UTC timestamps to HST for daily entries
- Extract visual context from images for richer entries
- Handle duplicate attachment names with counters
- Maintain sender whitelist for security
- Never delete original emails (archive only)
- **When email contains images**: Must embed all images in the daily log entry using `![Description](/self/_media/filename.jpg)` syntax, not just save to _media


### Email Reply Guidelines

When composing email replies, always ensure the response is thoughtful, appropriate, and adds value to the conversation:

#### Reply Content Standards

1. **Tone & Voice**:
   - Write as a helpful, knowledgeable assistant
   - Match the formality level of the sender
   - Be warm and personable while remaining professional
   - Use natural language, avoiding robotic or templated responses

2. **Content Quality**:
   - Address all questions or points raised by the sender
   - Provide specific, actionable information when relevant
   - Reference PKM content to add context and depth
   - Include relevant details from past interactions or shared experiences
   - Anticipate follow-up questions and address them proactively

3. **Response Structure**:
   - Open with appropriate greeting acknowledging the sender
   - Address main points in logical order
   - Close with appropriate next steps or offers of further help
   - Sign as "Edie" with contextually appropriate closing

4. **Contextual Awareness**:
   - Reference previous conversations when relevant
   - Acknowledge shared projects, people, or experiences
   - Show understanding of the sender's situation or needs
   - Demonstrate continuity in ongoing discussions

5. **Value Addition**:
   - Go beyond simple acknowledgment when possible
   - Offer insights, suggestions, or resources when appropriate
   - Connect related topics or people when beneficial
   - Provide updates on related projects or activities

## Handbooks

Handbooks are comprehensive guides documenting approaches to different domains. They live in `/self/handbooks/`.

### Structure

```
/self/handbooks/
├── software-engineering/
│   ├── index.md                    # Table of contents
│   ├── philosophy.md               # Core principles
│   ├── methodology/                # How-to guides
│   └── case-studies/              # Real examples
└── [other-domains]/
```

### Operational Guidelines

When observing a decision or methodology:
1. Identify which handbook it belongs to
2. Extract the underlying principle or pattern
3. Update the relevant chapter with the new insight
4. Add a case study if it's a significant example

When making decisions:
1. Consult the relevant handbook chapter
2. Apply the documented methodology
3. Note any deviations and reasons
4. Update the handbook if the approach evolves

### Citation Requirements

When adding information to handbooks:
1. **User-provided information**: Include source attribution
   - Format: `According to {person} on {date}: "{quote or paraphrase}"`
   - Example: `According to Daniel on 2025-07-27: "mevals would be a good name - evals for me"`

2. **Research tools**: Cite all sources from web searches or other research
   - Format: `Source: [{title}]({url})`
   - Include retrieval date if content may change
   - Example: `Source: [OpenAI Documentation](https://platform.openai.com/docs) (retrieved 2025-07-27)`

3. **Experiential knowledge**: Note when information comes from observed patterns
   - Format: `Based on experience with {context}`
   - Example: `Based on experience with production deployments at scale`

4. **Citation placement**:
   - Inline for specific facts or quotes
   - End of section for general references
   - In footnotes for extensive citations

## Debug Report Generation

For EVERY change made to the PKM, generate a debug report that documents what happened and why.

### Debug Report Structure

1. **Create debug directory if needed**: `/self/_debug/YYYY-MM-DD/`
2. **Generate unique report ID**: `HHMMSS-{brief-description}`
3. **Create report file**: `/self/_debug/YYYY-MM-DD/HHMMSS-{brief-description}.md`

### Debug Report Template

```markdown
---
title: Debug Report - {Change Summary}
change_id: {YYYY-MM-DD-HHMMSS}
created: {timestamp}
trigger_type: {user_message|email|system}
---

# Debug Report: {Change Summary}

## Trigger
**Type**: {user_message|email|system}
**Time**: {actual time when trigger occurred}
**Content**:
```
{exact text that triggered this change}
```

## Change Summary
{1-2 sentences describing what was done}

## Files Changed
### Created
- `/path/to/file.md` - {why created}

### Modified  
- `/path/to/file.md`
  - Lines added: {count}
  - Lines removed: {count}
  - Summary: {what changed}

### Deleted
- `/path/to/file.md` - {why deleted}

## PKM Rules Applied
1. **Rule**: "{quote from PKM_Spec.md}"
   - Source: PKM_Spec.md line {number}
   - How applied: {explanation}

## Decisions Made
- {Decision 1}: {why}
- {Decision 2}: {why}
- Alternatives considered: {what else could have been done}

## Git Commit
- Message: `{planned commit message}`
```

### When to Generate Debug Reports

Generate a debug report for:
- Creating any new file
- Modifying any existing file
- Deleting any file
- Processing emails
- Any operation that will result in a git commit

## Git Operations

### Commit Message Format

`{TYPE}({scope}): {description}`


### Types

- `CREATE` - New files/entities
- `UPDATE` - Modifications
- `DELETE` - Removals
- `REFACTOR` - Moving/renaming
- `EMAIL` - Email processing changes

### Examples

```bash
git commit -m "CREATE(people): Add Jane Doe

Debug: /self/_debug/2025-07-15/084532-add-jane-doe.md"

git commit -m "UPDATE(daily/2025-07-13-HST): Add Shield Wall Ramp project

Debug: /self/_debug/2025-07-13/142315-shield-wall-update.md"
```

## Maintenance & Updates

### Updating the PKM Spec

- Updates to this specification should only be made with direct user instruction
- Changes should be committed with clear explanation of what changed and why

### Edge Case Documentation

When encountering situations not covered by this spec:
1. Document in `/spec/edge-cases/YYYY-MM-DD-description.md`
2. Include:
   - The situation encountered
   - Decision made
   - Reasoning
   - Questions for user review

### Handling Contradictions

If this spec contradicts existing PKM content:
1. Bring to user's attention immediately
2. Do not make changes until clarified
3. Document the contradiction for resolution

### Decision Principles

When the spec doesn't cover something, apply these principles:
- **Consistency Over Innovation** - Follow existing PKM patterns
- **Atomic Over Complex** - Prefer separate entities over combined ones
- **Explicit Over Implicit** - Document decisions rather than leaving them implicit
- **User Intent Over Literal Interpretation** - Consider what the user is trying to achieve
- **Preserve Over Delete** - When unsure about removal, keep and flag for review
- **Fail Loudly** - Ask the user rather than guessing

## Quick Reference

### Path Examples

- About Me: `/self/about-me.md`
- Daily: `/self/daily/2025-07-13-HST.md`
- Person: `/self/people/jane-doe.md`
- Project: `/self/projects/shield-wall-ramp/index.md`
- Media: `/self/_media/2025-07-13-screenshot.png`
- Handbook: `/self/handbooks/software-engineering/debugging.md`

### Important Notes

- **ALWAYS run `date` command before creating files or adding timestamps**
- Git repository root is the project directory (contains `/self/`)
- Wikilinks use relative paths
- Media uses absolute paths from repository root
- Commit after each atomic operation
- Process emails atomically (fully complete or move to errors)
