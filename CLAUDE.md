# CLAUDE.md

This file provides guidance to Claude Code when working with the Edie personal knowledge management system.

## PKM Operational Guidelines

The following operational guidelines are extracted from PKM_Spec.md for immediate reference. For full details and examples, see **[spec/PKM_spec.md](spec/PKM_spec.md)**.

### Critical Rules

1. **All PKM directories MUST be located under the `/self/` directory** - This is the root of the personal knowledge management system.

2. **Always check current date/time with `date` command before**:
   - Creating daily log files
   - Adding timestamps to entries  
   - Updating `created` or `updated` fields
   - Processing emails
   
### Decision Principles

When the spec doesn't cover something, apply these principles:
- **Consistency Over Innovation** - Follow existing PKM patterns
- **Atomic Over Complex** - Prefer separate entities over combined ones
- **Explicit Over Implicit** - Document decisions rather than leaving them implicit
- **User Intent Over Literal Interpretation** - Consider what the user is trying to achieve
- **Preserve Over Delete** - When unsure about removal, keep and flag for review
- **Fail Loudly** - Ask the user rather than guessing
   
   
**VERY VERY IMPORTANT** The first time you run, update this CLAUDE.md file with the necessary information for Claude Code to efficiently maintain the PKM from the PKM spec. Delete this message when complete. 
