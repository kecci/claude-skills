---
name: glab-mr-resolve
description: Address GitLab Merge Request review comments — fix the code, reply in the thread, and resolve the discussion. Use when the user says "address the MR comments", "reply to review comments", "resolve the threads", "fix review feedback", or asks to respond to reviewers on a merge request.
---

# MR Resolve

The action half of `glab-mr-comments`: fix, reply, resolve.

## Instructions

1. Get the MR IID for the current branch:
   ```
   glab mr view --output json | jq '.iid'
   ```

2. List discussions **with their IDs** — `glab mr view --comments` does not show them, so use the API:
   ```
   glab api "projects/:fullpath/merge_requests/<iid>/discussions?per_page=100" \
     | jq '[.[] | select(.notes[0].resolvable == true and .notes[0].resolved == false)
            | {id, file: .notes[0].position.new_path, line: .notes[0].position.new_line,
               author: .notes[0].author.username, body: .notes[0].body}]'
   ```

3. For each unresolved thread, read the referenced code before deciding. Then either:
   - **Apply the fix** — make the edit, and note it for the commit.
   - **Push back** — the comment is wrong or out of scope; say why in the reply, leave the thread unresolved for the reviewer.

   Ask the user before pushing back on anything substantive; don't silently dismiss a reviewer.

4. Reply in the thread (not a new top-level note):
   ```
   glab api --method POST "projects/:fullpath/merge_requests/<iid>/discussions/<discussion_id>/notes" \
     --field body="Fixed in <short-sha> — <one line on what changed>"
   ```

5. Resolve only the threads you actually fixed:
   ```
   glab api --method PUT "projects/:fullpath/merge_requests/<iid>/discussions/<discussion_id>?resolved=true"
   ```

6. Commit and push the fixes, then summarize: resolved count, pushed-back count with reasons, anything left for the user to decide.

## Notes

- Resolve *after* the fix is pushed, so the thread and the code agree.
- A general (non-inline) comment has `resolvable: false` — reply to it, but it cannot be resolved.
- For a different MR, pass the IID explicitly instead of relying on the current branch.

## Requirements

- glab installed and signed in, `jq` available
- Permission to resolve threads on the MR (author or maintainer)
