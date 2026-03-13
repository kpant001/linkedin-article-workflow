---
name: linkedin-article-workflow
description: >
  Agentic workflow for creating high-quality LinkedIn articles. Use when the user wants to write,
  draft, or create a LinkedIn article or post on a technical topic. The user provides technical
  details or a topic, and the skill orchestrates a multi-agent pipeline: a Research & Writer agent
  drafts the article using deep web research, a Reviewer agent fact-checks for accuracy and
  hallucination, and they iterate up to 3 rounds. The final draft is saved and the user is notified
  in-app. Trigger phrases: "write a LinkedIn article", "draft a LinkedIn post", "create an article
  for LinkedIn", "LinkedIn content", "write a technical article".
metadata:
  author: kapil
  version: '1.0'
---

# LinkedIn Article Agentic Workflow

## When to Use This Skill

Use this skill when the user asks to:

- Write, draft, or create a LinkedIn article or post
- Produce a technical article for LinkedIn on a given topic
- Create thought leadership content for LinkedIn
- Draft a long-form LinkedIn article with research backing

## Overview

This skill implements a multi-agent pipeline with three phases:

1. **Research & Draft** — A subagent researches the topic deeply and produces a first draft
2. **Review & Fact-Check** — A separate subagent reviews the draft for accuracy, hallucination, and quality
3. **Iterate** — Writer and Reviewer go back and forth up to 3 rounds until quality passes

The orchestrator (you) coordinates the handoffs, manages the feedback loop, and notifies the user when the article is ready.

## Architecture

```
User (topic/details)
    │
    ▼
┌─────────────────────┐
│  Orchestrator Agent  │  ← You (the main agent)
└─────────┬───────────┘
          │
          ▼
┌─────────────────────────┐
│  Research & Writer Agent │  ← Subagent 1
│  • Deep web research     │
│  • Draft article         │
│  • Save to workspace     │
└─────────┬───────────────┘
          │
          ▼
┌─────────────────────────┐
│  Reviewer Agent          │  ← Subagent 2
│  • Fact-check claims     │
│  • Check for hallucination│
│  • Verify sources        │
│  • Produce feedback      │
└─────────┬───────────────┘
          │
          ▼
    ┌───────────┐
    │ Approved? │──── Yes ──→ Notify User → Done
    └─────┬─────┘
          │ No (issues found)
          ▼
    Feed issues back to Writer Agent
    (repeat up to 3 rounds)
```

## Step-by-Step Instructions

### Step 0: Gather Input from User

Before starting the pipeline, ensure you have:

- **Topic**: The main subject of the article
- **Technical details**: Key points, technologies, frameworks, or concepts to cover
- **Target audience** (optional): Who is this article for? (e.g., engineers, CTOs, product managers)
- **Tone** (optional): Professional, conversational, thought-leadership, tutorial-style
- **Length** (optional): Short post (~300 words), standard article (~800-1200 words), or long-form (~2000+ words)

If the user hasn't specified audience, tone, or length, use these defaults:
- Audience: Technical professionals on LinkedIn
- Tone: Professional but approachable thought-leadership
- Length: Standard article (800-1200 words)

Save the user's input to a briefing file:

```
write(file_path="/home/user/workspace/linkedin-article/briefing.md", content=<structured briefing>)
```

### Step 1: Spawn the Research & Writer Agent

Spawn a **research** subagent with the following objective. Preload the `research-assistant` skill.

```python
run_subagent(
    task_name="Research & Draft LinkedIn Article",
    subagent_type="research",
    preload_skills=["research-assistant"],
    user_description="Researching topic and drafting LinkedIn article",
    objective="""
    You are the Research & Writer agent in a LinkedIn article pipeline.

    READ the briefing at /home/user/workspace/linkedin-article/briefing.md for the topic and requirements.

    ## Phase 1: Deep Research
    1. Use search_web to find 5-10 authoritative sources on the topic
    2. Use search_vertical (vertical="academic") if the topic has academic research
    3. Use fetch_url to read the most relevant sources in depth
    4. Save your research notes with source URLs to /home/user/workspace/linkedin-article/research-notes.md

    ## Phase 2: Write the Draft
    Based on your research, write a LinkedIn article that:
    - Opens with a compelling hook (question, surprising stat, or bold statement)
    - Structures content with clear sections using markdown headers
    - Includes specific data points, statistics, or examples from your research
    - Every factual claim must have a source URL noted in brackets
    - Ends with a clear takeaway or call-to-action
    - Matches the tone and length specified in the briefing
    - Uses short paragraphs (2-3 sentences) optimized for LinkedIn readability
    - Includes relevant hashtags at the end (3-5)

    ## Output
    Save the draft to: /home/user/workspace/linkedin-article/draft-v1.md
    Include a "Sources" section at the bottom with all URLs used.

    IMPORTANT: Every factual claim, statistic, or technical assertion must be backed by
    a source from your research. Do NOT make up statistics or claims.
    """
)
```

### Step 2: Spawn the Reviewer Agent

After the Writer agent completes, spawn a **research** subagent as the Reviewer.

```python
run_subagent(
    task_name="Review & Fact-Check Article",
    subagent_type="research",
    preload_skills=["research-assistant"],
    user_description="Fact-checking and reviewing the LinkedIn article draft",
    objective="""
    You are the Reviewer agent in a LinkedIn article pipeline. Your job is to ensure
    the article is accurate, well-written, and free of hallucination.

    READ the current draft at /home/user/workspace/linkedin-article/draft-v{VERSION}.md
    READ the research notes at /home/user/workspace/linkedin-article/research-notes.md

    ## Review Checklist

    ### 1. Factual Accuracy
    - Verify every statistic, date, and factual claim by searching the web
    - Cross-reference claims against the research notes and original sources
    - Flag any claim that cannot be verified with a reliable source

    ### 2. Hallucination Check
    - Identify any statements that sound plausible but aren't supported by sources
    - Check that quoted statistics match their cited sources
    - Verify that named companies, products, or people are correctly described

    ### 3. Technical Accuracy
    - Verify technical claims about technologies, frameworks, or protocols
    - Ensure terminology is used correctly
    - Check that technical explanations are accurate and not oversimplified to the point of being wrong

    ### 4. Quality & Structure
    - Is the hook compelling?
    - Does the article flow logically?
    - Are paragraphs appropriately short for LinkedIn?
    - Is the tone consistent and appropriate?
    - Does the conclusion have a clear takeaway?

    ### 5. LinkedIn Optimization
    - Is the opening line strong enough to survive the "see more" fold?
    - Are hashtags relevant and not overused?
    - Is the length appropriate?

    ## Output
    Save your review to: /home/user/workspace/linkedin-article/review-v{VERSION}.md

    Structure your review as:
    ```
    ## Overall Assessment
    **Status**: APPROVED / NEEDS_REVISION
    **Quality Score**: X/10

    ## Issues Found
    ### Critical (must fix)
    - [list any factual errors, hallucinations]

    ### Major (should fix)
    - [list significant quality or accuracy issues]

    ### Minor (nice to fix)
    - [list style, formatting, or minor issues]

    ## Specific Line Feedback
    [Quote the specific text and provide correction/suggestion]

    ## Verified Claims
    [List claims you independently verified as correct]
    ```

    Be thorough but fair. Only flag NEEDS_REVISION if there are Critical or Major issues.
    Minor issues alone should result in APPROVED with suggestions.
    """
)
```

### Step 3: Feedback Loop (Up to 3 Rounds)

After the Reviewer completes, read the review file and check the status:

```python
review = read(file_path="/home/user/workspace/linkedin-article/review-v{VERSION}.md")
```

**If status is APPROVED**: Proceed to Step 4 (notification).

**If status is NEEDS_REVISION and round < 3**: Spawn the Writer agent again with revision instructions:

```python
run_subagent(
    task_name="Revise LinkedIn Article (Round {N})",
    subagent_type="research",
    preload_skills=["research-assistant"],
    user_description="Revising article based on reviewer feedback (round {N} of 3)",
    objective="""
    You are the Research & Writer agent. Your draft has been reviewed and needs revision.

    READ your previous draft at: /home/user/workspace/linkedin-article/draft-v{VERSION}.md
    READ the reviewer feedback at: /home/user/workspace/linkedin-article/review-v{VERSION}.md
    READ the original briefing at: /home/user/workspace/linkedin-article/briefing.md
    READ research notes at: /home/user/workspace/linkedin-article/research-notes.md

    ## Instructions
    1. Address ALL Critical and Major issues from the review
    2. Address Minor issues where possible
    3. If any claims were flagged as unverified, either:
       a. Find a reliable source to back them up (search_web, fetch_url)
       b. Remove or rephrase the claim
    4. Do NOT introduce new unverified claims while fixing old ones
    5. Maintain the overall tone and structure unless the review specifically
       flagged those as issues

    Save the revised draft to: /home/user/workspace/linkedin-article/draft-v{NEW_VERSION}.md
    Include updated Sources section.
    """
)
```

Then spawn the Reviewer again on the new draft. Repeat until APPROVED or 3 rounds complete.

**If 3 rounds complete without APPROVED**: Proceed to Step 4 anyway — the article has been significantly improved through iteration.

### Step 4: Prepare Final Output and Notify

Once the loop completes:

1. Read the final draft version
2. Create a clean final version without the source annotations (for LinkedIn posting):

```python
write(file_path="/home/user/workspace/linkedin-article/final-article.md", content=<clean version>)
```

3. Also keep the sourced version for the user's reference:

```python
write(file_path="/home/user/workspace/linkedin-article/final-article-with-sources.md", content=<sourced version>)
```

4. Send in-app notification:

```python
send_notification(
    title="LinkedIn Article Ready for Review",
    body="Your article on '{topic}' has been researched, drafted, and fact-checked through {N} review rounds. Quality score: {X}/10. The final draft is ready for your review."
)
```

5. Share the final article file with the user:

```python
share_file(file_path="/home/user/workspace/linkedin-article/final-article.md", name="linkedin_article")
share_file(file_path="/home/user/workspace/linkedin-article/final-article-with-sources.md", name="linkedin_article_with_sources")
```

6. Present a summary to the user:
   - Topic covered
   - Number of review rounds completed
   - Final quality score
   - Key sources used
   - Any remaining minor suggestions from the last review
   - Remind user to review before posting to LinkedIn

## File Structure

The workflow creates and manages these files:

```
/home/user/workspace/linkedin-article/
├── briefing.md                      # User's input and requirements
├── research-notes.md                # Research findings with source URLs
├── draft-v1.md                      # First draft
├── review-v1.md                     # First review
├── draft-v2.md                      # Revised draft (if needed)
├── review-v2.md                     # Second review (if needed)
├── draft-v3.md                      # Final revision (if needed)
├── review-v3.md                     # Final review (if needed)
├── final-article.md                 # Clean version for LinkedIn
└── final-article-with-sources.md    # Version with source citations
```

## Key Principles

- **No hallucination**: Every factual claim must be research-backed
- **Source transparency**: The user gets both a clean version and a sourced version
- **Iterative quality**: Up to 3 rounds of writer-reviewer feedback
- **LinkedIn-optimized**: Short paragraphs, strong hooks, proper hashtags
- **User stays in control**: The workflow produces the draft; the user reviews and posts

## Example Usage

User says: "Write a LinkedIn article about how enterprises are adopting agentic AI workflows in 2026, focusing on the shift from single-model to multi-agent architectures. Target audience is CTOs and engineering leaders."

The orchestrator would:
1. Save this as the briefing
2. Spawn Writer to research agentic AI adoption trends, multi-agent architectures, enterprise case studies
3. Writer produces a 1000-word article with statistics from Gartner, McKinsey, etc.
4. Spawn Reviewer to verify all stats and claims
5. If issues found, iterate
6. Notify user with the polished article
