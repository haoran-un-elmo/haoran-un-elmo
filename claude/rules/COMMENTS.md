# Comments

Write comments.

Comments should NOT be a historical record of how the code got that way.

Comments MAY include references to JIRA tickets, or other relevant code.

Comments should be succinct, simple and terse.

Comments MUST not be out of date. When code changes, re-assess relevant
comments.

## The test

Would this help someone who opens the file cold — no ticket, no PR, no
conversation, six months from now?

If yes, write it. If it only lands for someone who watched the code being
written, cut it.

## Worth writing

- **What the file is:** one comment saying what it is and what it does — on
  the exported class, or at the top of the file when there is no single class.
  Never both.
- **Function comments:** what it is for, and its modes of operation if it has
  more than one.
- **Block-level and inline comments should:**
    - summarise complex code succinctly for an ordinary developer — a block,
      never a single line
    - explain things you knew that they don't, e.g.:
        - **A constraint from outside the file:** a DB uniqueness rule, an API
          contract, an upstream bug being worked around, an ordering the types
          can't express.
        - **A trap:** why the obvious simplification breaks.
    - explain decisions, requirements and constraints
        - e.g. include the JIRA reference, "...(see LAB-511)"
        - **A pointer:** a ticket id is a lookup, not an explanation — a reader
          who has not opened it must still understand the comment. State the
          thing, then trail the reference in parentheses. A bare
          `// see LAB-511` is right only where there is genuinely nothing else
          to say.

## Budget

Function/class doc: 6 lines. Code comment: 4 lines.

Longer means it belongs in the ticket, `docs/`, the commit message, or a test
name. Link to it and cut the prose.

One exception: code a competent developer would otherwise delete as
unnecessary — a guard, a workaround, a deliberate duplication. One sentence on
what breaks without it, then stop.

## Using Claude

Claude comments from the conversation, not from the code. It has the ticket, the
bug and the reasoning in mind, and mistakes that for what a reader needs.

- **Don't ask it to "add comments".** As a standalone task it produces one per
  block regardless of whether any was needed. Ask about the specific thing that
  is hard to follow.
- **Read what it generates as a stranger would.** If a comment only works
  because you remember the session, cut it. That session is the one reader who
  will never open the file again.
- **A comment explaining the change is a commit message.** "this now handles…",
  "unlike the previous version…" — those go in the commit, not the file.

## Never

- **Include change history.** "previously", "used to", "no longer",
  "pre-LAB-797 this was…". Describe what the code does now.
- **Proofs a bug can't happen.** Impossible cases go in a test, not a paragraph.
- **Reasoning narration.** "note that", "worth pointing out", "this deliberately
  does not…".
- **A ticket id as the subject.** `// LAB-672: keyed by source and id`. The
  prefix teaches a reader without the ticket nothing, and reads as though the
  id were the reason. Put the reason first and the id at the end.
- **Restating the signature.** `@param tenantId - The tenant UUID.`
- **Restating the line below it.**
