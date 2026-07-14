---
name: markdown-review
description:
  Review recent changes in explicitly named Markdown files for correctness, clarity, structure, grammar, consistency,
  links, and examples. When a target is an AI prompt or a skill, also review its instruction design, safety, metadata,
  and operational behavior. Use only when the user explicitly invokes `$markdown-review` or asks to use the
  markdown-review skill and provides one or more exact Markdown file paths.
---

# Markdown Review

## Requirements

- Require one or more exact, existing Markdown file paths. If a target is missing or ambiguous, ask for the specific
  file before reviewing.
- Review without editing unless the user explicitly asks for fixes.
- Use the user-specified revision, range, or scope. Otherwise:
  1. Review current staged, unstaged, and untracked changes affecting the named files.
  2. If none of the named files has current changes, review their changes in the most recent commit that touched at
     least one of them.
- Read every named file in full. Keep findings focused on the selected changes; cite unchanged text only when it
  establishes an inconsistency or explains impact.
- Inspect connected repository files only when needed to verify a changed link, example, resource, placeholder, or
  contract. State any scope expansion in the review.
- Do not run live inference or browse the web merely to perform the review.

## Workflow

1. Confirm the targets and establish the review baseline.
2. Classify each target as ordinary Markdown, an AI prompt, a skill, or both prompt and skill. Treat a file as:
   - a prompt when the user identifies it as one, its path or filename clearly marks it as one, or its primary content
     instructs an AI model;
   - a skill when the user identifies it as one or it is a `SKILL.md` with skill frontmatter and instructions.
3. Read the selected changes, every complete target, and only the connected files needed for validation.
4. Perform the Markdown review for every target.
5. Perform the specialized review for every target classified as a prompt or skill.
6. Report actionable findings in severity order within the required output sections. State explicitly when a section has
   no findings.

## Markdown Review

Check every target for:

- valid Markdown and frontmatter, logical heading hierarchy, readable lists and tables, and correctly paired fences;
- typos, grammar, punctuation, awkward wording, unclear references, and terminology or tone inconsistencies;
- working links, anchors, image references, and relative paths;
- accurate and internally consistent examples, commands, code, names, and claims when they can be verified from the
  repository;
- contradictions, redundancy, missing context, misleading emphasis, and organization that obscures the document's
  purpose;
- accessibility issues such as unhelpful link text, missing image descriptions, or tables that are difficult to follow.

## Specialized Review

For a prompt, additionally check:

- whether its goal, audience, inputs, output format, constraints, and success criteria are explicit enough;
- instruction priority, conflicts, ambiguity, edge cases, unsupported assumptions, and opportunities for unintended
  behavior;
- boundaries around untrusted input, prompt injection, secrets, unsafe actions, and claims that require evidence;
- placeholder names, required values, escaping, delimiters, defaults, and consistency with in-scope callers or tests;
- unnecessary repetition, excessive context, avoidable inference cost, and whether behavior can be evaluated reliably.

For a skill, additionally check:

- whether `name`, directory naming, and trigger-oriented `description` agree and accurately cover intended use cases;
- whether instructions are imperative, actionable, correctly ordered, and compatible with the tools and permissions they
  require;
- whether scope limits, mutation rules, safety boundaries, fallbacks, and completion criteria are explicit where needed;
- whether referenced scripts, assets, and references exist, use correct relative paths, and are loaded only when needed;
- whether the skill stays concise, avoids duplicated guidance, and uses progressive disclosure for detailed material;
- whether `agents/openai.yaml` and any packaging metadata remain aligned with the skill when those files are in scope.

## Review Standard

- Explain the concrete ambiguity, inconsistency, or operational impact of every non-editorial finding.
- Assign editorial defects `Low` severity unless they materially change meaning.
- Do not expand into unchanged runtime contracts, schemas, jobs, or tests unless needed to substantiate a finding or the
  requested scope requires it.

## Output

Begin with the named files, review baseline, classifications, and checks performed. Then use:

1. `Markdown review` for findings from the general Markdown pass. Always include this section.
2. `Specialized review` for prompt- or skill-specific findings, labeled by type. Include this section only when at least
   one target is classified as a prompt or skill.

For each finding, provide the severity (`Critical`, `High`, `Medium`, or `Low`), a concise title, the exact file and
line, the problem and impact, and a focused recommendation. Keep unresolved questions within the applicable section and
separate from findings.
