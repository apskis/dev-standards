# dev-standards

Cross-project engineering standards, kept in one place so every project uses the same copy.

## coding-standards
A Claude Code / Cursor skill. `SKILL.md` routes to the rule packs under
`coding-standards/rules/`. It is the single source of truth for coding standards.

### Use it in every project
Symlink it into your user skills directory so all Claude Code sessions load it:

```powershell
# PowerShell (Developer Mode on, or run as admin)
$dst = "$env:USERPROFILE\.claude\skills\coding-standards"
if (Test-Path $dst) { Remove-Item -Recurse -Force $dst }
New-Item -ItemType SymbolicLink -Path $dst -Target "D:\projects\dev-standards\coding-standards"
```

Editing the rules here updates the skill everywhere (the symlink points back to this repo).

Individual repos may also add a short `CLAUDE.md` that says "follow the coding-standards skill"
for belt-and-suspenders enforcement.
