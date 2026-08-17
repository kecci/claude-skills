---
name: glab-mr-comments
description: Fetch and summarize all comments and discussions on the current GitLab Merge Request. Use when the user says "show MR comments", "what did reviewers say", "read the review feedback", "any comments on my MR", or asks what is unresolved on a merge request.
---

# MR Comments

Fetch all comments/discussions from the current GitLab Merge Request.

## Instructions

1. Determine the current branch: `git branch --show-current`
2. Get the GitLab project path from the git remote: `git remote get-url origin`, then extract the project path (remove .git suffix and host prefix)
3. URL-encode the project path (replace `/` with `%2F`)
4. Fetch all discussions (comments) for that MR:

```
   glab mr view --comments
```

5. Parse and present the comments in a clear format:
   - Show author, date, and body for each comment
   - Group threaded replies together
   - Indicate which comments are resolved vs unresolved
   - Show the file path and line number for inline comments
6. Summarize: total comments, unresolved count, and key action items

## Requirements

- glab (GitLab CLI) has to be installed & signed-in
- If using a self-hosted GitLab instance, replace `gitlab.com` with your instance URL
