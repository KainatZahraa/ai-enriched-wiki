# AI Enrichment Prompt

This is the prompt template used in the n8n workflow's "Build Prompt" node.
It's sent to the Gemini API to generate the enrichment content for each 
wiki page.

## Purpose

Given a raw intern-written wiki page (What I Learned / Key Concepts /
Mistakes I Made / References), this prompt asks the model to generate:

1. A TLDR summary
2. FAQs
3. A quiz
4. Related topics (grounded in the existing wiki, not hallucinated)
5. Suggested next learning topics

The output is required to be strict JSON so the workflow can parse it
programmatically and write each field back into its own Notion property.

## Template

\`\`\`
You are helping build an internal engineering wiki. Below is a page written by an intern documenting something they learned.

Generate the following, based ONLY on the content provided:

1. TLDR: 2-3 sentence summary
2. FAQs: 3-5 likely questions with concise answers
3. Quiz: 3-5 questions to test retention, with answers
4. Related Topics: from this list of existing topics: {{existing_topic_titles}}, pick any that are meaningfully related. Return empty list if none are.
5. Suggested Next Learning: 2-3 topics a learner should study next

Return ONLY valid JSON, no other text, in this structure:
{
  "tldr": "",
  "faqs": [{"q": "", "a": ""}],
  "quiz": [{"question": "", "answer": ""}],
  "related_topics": [],
  "next_learning": []
}

PAGE CONTENT:
{{page_content}}
\`\`\`

## Variables

| Variable                | Source                                                        |
|--------------------------|----------------------------------------------------------------|
| `{{existing_topic_titles}}` | Titles of all other pages in the database, fetched via the "Get many database pages" node, excluding the current page |
| `{{page_content}}`       | The `Content` property of the page currently being enriched, fetched via the Notion Trigger |

## Design notes

- **Grounding for Related Topics**: the model is only allowed to choose from
  a real list of existing topics, rather than inventing plausible-sounding
  but nonexistent related pages. It's explicitly told to return an empty
  list if nothing is genuinely related, rather than forcing a connection.
- **Strict JSON output**: this is required so the "Parse Response" node in
  the workflow can reliably `JSON.parse()` the result. The workflow strips
  markdown code fences (` ```json ... ``` `) before parsing, in case the
  model wraps its output despite instructions.
- **"Based ONLY on the content provided"**: this constraint reduces
  hallucination — the model is discouraged from inventing facts about the
  topic beyond what the intern actually wrote.

## Post-processing

The raw JSON output is not written directly into Notion. A formatting step
(in the "Parse Response" Code node) converts the structured JSON into
human-readable plain text before it's written to Notion's text properties,
e.g.:

\`\`\`
Q: What is a Git commit?
A: A commit is a saved snapshot of changes with a descriptive message.
\`\`\`

instead of raw JSON like `{"q": "...", "a": "..."}`.

## Model used

This project currently calls the Gemini API (`gemini-flash-latest`) via a
direct HTTP Request node, since it offers a genuinely free tier without
requiring billing setup. The prompt format above is model-agnostic and
would work unchanged if swapped toanother LLM provider — only the HTTP 
Request node's URL, auth, and request/response shape would need
to change.