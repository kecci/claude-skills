---
name: glab-mr-create
description: Create a GitLab Merge Request for the current branch, with a title and description written from the actual commits and diff. Use when the user says "open an MR", "create a merge request", "raise an MR", "glab mr create", or asks to push the branch up for review.
---

# MR Create

Open a Merge Request for the current branch against its target.

## Instructions

1. Check the branch and state:
   ```
   git branch --show-current
   git status --short
   ```
   Refuse to continue on the default branch (`main`/`master`) — ask which branch to create instead.
   If there are uncommitted changes, tell the user and stop; do not commit for them unless asked.

2. Check whether an MR already exists for this branch:
   ```
   glab mr list --source-branch "$(git branch --show-current)"
   ```
   If one exists, report its URL and stop — use `glab mr update` for edits instead.

3. Read what is actually being merged (default target is the project default branch; use `-b` to override):
   ```
   git log --oneline origin/main..HEAD
   git diff origin/main...HEAD --stat
   ```

4. Write the title and description from that. Title: one line, imperative, no branch-name dumping. Description: what changed and why, a bullet per meaningful change, plus any migration/rollout note. Skip test plans nobody will read.

5. Create it, pushing the branch in the same step:
   ```
   glab mr create --push --yes \
     --title "<title>" \
     --description "<description>" \
     [-b <target-branch>] [--draft] [--reviewer <user>] [--label <label>]
   ```
   Use `--draft` when the branch is not ready for review. Use `--fill` instead of `--title`/`--description` only when the commits already read as a good MR body.

6. Report the MR URL back.

## Requirements

- glab installed and signed in (`glab auth status`)
- Branch is pushable to origin
