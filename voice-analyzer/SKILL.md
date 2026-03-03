---
name: voice-analyzer
description: "Analyze a user's writing voice from their sent emails and Slack messages, then build a reusable voice profile for drafting communications that sound like them. Use this skill whenever any other skill needs to draft messages, emails, or communications on the user's behalf — it's the foundation for authentic voice matching. Also use it when the user says things like 'learn my voice,' 'analyze my writing style,' 'build a voice profile,' 'make it sound like me,' or whenever you're about to write something that needs to match the user's tone and style."
---

# Voice Analyzer & Mimicry Engine

## Purpose
Messages Claude drafts must sound like the user while meeting Elements of Style standards. Without a voice profile, every draft requires heavy editing. This skill analyzes real sent communications to build a cached, reusable voice profile.

## When This Skill Loads
Any skill that drafts communication on behalf of the user should load this skill's output (the voice profile). It is also triggered directly when building or updating a voice profile.

## Inputs
- 20–30+ real sent emails and Slack messages
- Segmented by audience (exec, peer, vendor, engineer) and channel (email, Slack)

## Outputs
- Reusable voice profile document (under 2,000 words), saved as a file
- Separate Elements of Style rules file layered on top

## Workflow

### Step 1: Collect Samples
Gather 20–30+ sent messages across audience types and channels. Group them:
- Executive communications
- Peer/team communications
- Vendor/external communications
- Engineer/technical communications
- Email vs. Slack split within each

### Step 2: Analyze Patterns
For each audience segment, analyze:
- Sentence length distribution (short/punchy vs. complex)
- Vocabulary level and jargon usage
- Punctuation habits (em dashes, ellipses, exclamation points)
- Openers and closers (how they start and end messages)
- Formality range (most formal to least formal)
- Emoji usage patterns (frequency, types, contexts)
- Capitalization tendencies
- Paragraph structure preferences
- How they handle disagreement or pushback
- How they deliver bad news vs. good news

### Step 3: Build the Voice Profile
Structure the profile document with these sections:

**Core Voice** (~400 words)
- Default sentence rhythm and length
- Signature phrases and transitions
- Punctuation fingerprint
- Formality baseline

**Audience Sub-Profiles** (~300 words each, 4 max)
- How voice shifts per audience type
- Specific openers/closers per audience
- Formality adjustments
- What gets more/less formal

**"Never Do This" Section** (~200 words)
- Phrases that sound wrong for this person
- Tones to avoid
- Common Claude-isms that clash with their voice
- Negative examples from calibration

### Step 4: Layer Elements of Style
Create a separate style rules file that layers on top:
- Where natural tendencies conflict with Strunk & White, note the tension
- Sharpen, don't replace — the goal is the user's voice made tighter, not a different voice
- The style filter can be toggled off for quick casual Slack messages

### Step 5: Validate with Test Scenarios
After generating the profile, run 3–4 test drafts:
1. Vendor pushback reply
2. Team Slack update
3. Executive status response
4. Casual Slack message

Present each to the user for calibration. Log any corrections as refinements.

## Profile Maintenance
- Voice profile and style rules stored as two separate files
- Updates happen via changelog — short log entries per edit
- Consolidate changelog into updated profile every 20 edits
- Profile stays under 2,000 words with forced prioritization (most common patterns first, edge cases last)
- Style filter togglable for casual messages

## Context Rules
- Voice profile: load only when drafting or updating
- Style rules: separate file, layered at draft time
- Keep profile under 2,000 words — if it grows past that, trim least-used patterns
- Store both files for reuse across sessions
