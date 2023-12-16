# Customizing Git
how to customize `git` to your preference

---
## Get in Touch
+ Telegram: https://t.me/sudw1n
+ Discord: `sudw1n`
+ GitHub/GitLab: `sudw1n`
+ blog: `sudw1n.gitlab.io`
+ !!! I'M NOT YOUR TECH SUPPORT !!!
---

## Reference
https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration
---
## Configuration levels
- system-wide (least precedence; most global)
- user-wide
- project-wide (most precedence; least global)
--
### System Configuration
- path: `[path]/etc/gitconfig`

e.g., Windows: `C:\Program Files\Git\etc\gitconfig`

- flag: `git config --system`
--
### User Configuration
- path: `~/.gitconfig` or `~/.config/git/config`
- flag: `git config --global`
--
### Local Configuration
- path: `.git/config`
- flag: `git config [--local]`

---
## Useful Options
+ view all options: `git config --help`
+ `core.editor <name>`: editor to open for commit messages, etc.
+ `commit.template <file>`: default message for commits
--
example template:
```
Subject line (try to keep under 50 characters)

Multi-line description of commit,
feel free to be detailed.

[Ticket: X]
```

--
+ `core.excludesfile <file>`: global `.gitignore`
+ `help.autocorrect <delay>`: autocorrect delay
--
- windows uses CRLF (`\r\n`)
- editors silently replace `\n` with CRLF
+ `core.autocrlf <true/input/false>`: auto convert CRLF to LF or vice versa; `input` disables "vice versa" for Unix
+ TL;DR: fuck Windows
--
- `alias.<alias-name> <git command>`: make aliases for git commands
---
## Attributes
+ apply settings for paths
+ file: `.gitattributes`
--
+ e.g., identify binary files
+ `qBitTorrent` example
--
+ e.g., filter content before checking in or out of git
+ `git-crypt` example: https://github.com/AGWA/git-crypt
---
## Hooks
+ allows running custom scripts when certain actions occur
+ client-side: triggered by committing, merging, etc.
+ server-side: receiving pushed commits
+ install a hook: place an executable file that's named appropriately at `.git/hooks`