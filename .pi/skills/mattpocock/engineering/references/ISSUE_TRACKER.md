# Issue Tracker

Shared runtime rules for skills that read from or write to a project's issue tracker.

## Resolve the tracker

Use this order:

1. **Explicit user instruction** — if the user names GitHub, GitLab, local markdown, Jira, Linear, or another workflow, follow that.
2. **GitHub remote** — if `git remote -v` points at GitHub, use GitHub Issues with the `gh` CLI.
3. **GitLab remote** — if `git remote -v` points at GitLab (`gitlab.com` or self-hosted), use GitLab Issues with the `glab` CLI.
4. **Local markdown convention** — if `.scratch/` exists and no hosted issue tracker is evident, write issues as markdown files under `.scratch/<feature>/`.
5. **Ask** — if none of the above is clear, ask the user where work is tracked before creating, editing, or closing issues.

## GitHub operations

Infer the repo from the current clone; `gh` does this automatically when run inside the repo.

- **Create**: `gh issue create --title "..." --body "..."`
- **Read**: `gh issue view <number> --comments --json number,title,body,labels,comments,state,author,createdAt,updatedAt`
- **List**: `gh issue list --state open --json number,title,body,labels,comments,updatedAt`
- **Comment**: `gh issue comment <number> --body "..."`
- **Apply/remove labels**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Close**: `gh issue close <number> --comment "..."`

Use heredocs or temp files for multi-line issue bodies/comments instead of complex shell quoting.

## GitLab operations

Infer the repo from the current clone where possible.

- **Create**: `glab issue create --title "..." --description "..."`
- **Read**: `glab issue view <number> --comments`
- **List**: `glab issue list`
- **Comment**: `glab issue note <number> --message "..."`
- **Apply labels**: `glab issue update <number> --label "..."`
- **Close**: `glab issue close <number>`

## Local markdown operations

Use `.scratch/<feature>/` for issue files. Create one markdown file per issue with:

- title as the H1
- source/parent reference if any
- labels/state
- body/acceptance criteria
- comments or follow-up notes as dated sections

## Triage labels

Unless the user or repo clearly establishes otherwise, use the labels defined by the `triage` skill:

- `bug`
- `enhancement`
- `needs-triage`
- `needs-info`
- `ready-for-agent`
- `ready-for-human`
- `out-of-scope`
