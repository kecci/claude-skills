---
name: glab-mr-review
description: Review someone else's GitLab Merge Request — read the diff, find real problems, then post the findings as inline thread comments and optionally approve. Use when the user says "review this MR", "review MR 123", "look at this merge request", "leave comments on the MR", or pastes a GitLab MR URL and asks for a review.
---

# MR Review

Review an MR you did not write, and leave the findings on GitLab.

## Instructions

1. Resolve the target. An MR number, branch, or URL all work; with none given, use the current branch.

   ```
   glab mr view <iid|branch> --output json | jq '{iid, title, author: .author.username, source_branch, target_branch, description}'
   ```

2. Read the change. Start with the shape, then the diff:

   ```
   glab mr diff <iid> --raw > /tmp/mr.diff   # scratchpad path is fine too
   ```

   Read the surrounding files for anything non-obvious — a diff hunk alone does not show whether a caller breaks. Do not review from the diff in isolation.

3. Look for, in this order:
   - **Correctness** — logic that is wrong for real inputs, not style. Concurrency, nil/zero values, error paths swallowed, off-by-one, wrong boundary.
   - **Blast radius** — grep the callers of every changed exported function. A signature or behavior change that other call sites depend on is the finding.
   - **Data & migrations** — anything irreversible, unindexed, or unbatched against a big table.
   - **Missing check** — new non-trivial logic with no test that fails if it breaks.

   Skip nits. If a finding does not change behavior, correctness, or a decision, it does not get a comment.

4. Verify each finding before posting: name the concrete input or state that produces the wrong result. If you cannot, drop it.

5. Post inline. Inline comments need the diff SHAs:

   ```
   glab api "projects/:fullpath/merge_requests/<iid>" | jq '.diff_refs'
   ```

   Then one thread per finding: (Basic Line Comment)

   ```
   glab mr note create <mr-id> --file "src/main.js" --line 10:15 -m "Please refactor this block."
   ```

   Or Commenting on a Range of Lines:

   ```
   glab mr note create <mr-id> --file "path/to/file.ext" --line 42 -m "Your comment message here"
   ```

   For a comment on a removed Lines:

   ```
   glab mr note create <mr-id> --file "src/utils.py" --old-line 87 -m "Why was this logic removed?"
   ```

   It's better if you also add with suggestion like this

   ```suggestion:-0+0
   The corrected code goes here
   ```

   General remarks that belong to no line go top-level:

   ```
   glab mr note <iid> -m "<summary>"
   ```

   Key Flags Reference:
   --file <string>: The explicit repository path of the file you are reviewing.
   --line <string>: Targets a specific line number (e.g., 42) or range (e.g., 10:15) in the new version of the file
   --old-line <int>: Targets a line number in the old/pre-modified version of the file.
   -m, --message <string>: The body text of your comment.
   --resolvable: Automatically creates the note as a resolvable thread (defaults to true).

6. Approve only when asked, and only if nothing blocking is left:

   ```
   glab mr approve <iid>
   ```

7. Report to the user: what you posted, what you deliberately left out, and whether you would block the merge.

## Notes

- Confirm with the user before posting — comments are visible to the author and their team immediately.
- Nothing found is a valid review. Say so; do not manufacture findings to look thorough.
- Use `/code-review` instead when reviewing your own uncommitted work locally.

## Requirements

- glab installed and signed in, `jq` available
- Reporter access or above on the project to post discussions; approval rights to approve
