# ]Git — A Git Interface for APL+Win

A user command that brings full Git version control to [APL+Win](https://www.apl.com/) — no command line required. Manage repositories, branches, commits, diffs, and merges entirely from within the APL+Win session.

`]Git` works by calling the Git for Windows application under the hood and bridging the gap between APL+Win workspaces and Git's text-file-based workflow. APL objects are automatically converted to and from UTF-8 text files (`.aplwfn`, `.aplwcv`, `.aplwvr`) so that Git can track them with full diff and merge support.

## Prerequisites

- **Git for Windows** — download from [git-scm.com](https://git-scm.com/downloads). During installation, link Git to your GitHub account for remote repository access.
- **APL+Win** — a working APL+Win installation.

## Quick Install

Download the latest **GITCMDS.sf** user command file from the [Releases](../../releases) page and run:

```
]UFILE C:\path\to\GITCMDS
```

This installs three user commands: `]Git`, `]TOTXT`, and `]FROMTXT`.

**Alternative:** Download the **GIT_NN.w3** workspace from Releases and use `]USAVE` to copy the individual objects into your own user command file. See the [full object list](#included-objects) below.

## How It Works

APL+Win stores code in binary workspace files (`.w3`), which Git can't meaningfully diff or merge. `]Git` solves this by maintaining a parallel set of UTF-8 text files alongside the workspace:

- `]Git Add MYFUNC` exports the APL object to a `.aplwfn` text file and stages it for commit
- `]Git Pull` fetches remote changes and imports them back into the workspace via `]FROMTXT`
- `]Git Clone` downloads a repository, creates a workspace, and imports all APL text files into it

The workspace and its text files live in the same directory, and `]Git` keeps them in sync.

## Getting Started

### Put an Existing Workspace on GitHub

1. Create a new empty repository on [github.com](https://github.com)

2. Move your workspace into its own directory with a matching name.
   For example, `C:\Workspace\MyCode.w3` should become `C:\Workspace\MyCode\MyCode.w3`

3. `)LOAD` or `)XLOAD` the workspace, then:

```apl
]Git Init                              ⍝ Initialize a local repo
]Git Remote Add origin https://github.com/you/your-repo.git
]Git Add .                             ⍝ Export & stage all objects
]Git Commit "Initial commit"
]Git Push -u origin master             ⍝ First push sets upstream
```

After that initial setup, future pushes are just `]Git Push`.

### Clone an Existing Repository

```apl
]Git Clone MyRepo "C:\Workspace\" "https://github.com/you/MyRepo.git"
```

This creates `C:\Workspace\MyRepo\`, clones the repo into it, creates `MyRepo.w3`, imports all APL text files, and saves the workspace.

## Daily Workflow

```apl
]Git Fetch                             ⍝ Check for remote changes
]Git Diff origin/master                ⍝ Review what changed
]Git Pull                              ⍝ Update workspace from remote

⍝ ... edit code in the workspace ...

]Git Add BankRpt                       ⍝ Export & stage the object
]Git Diff --staged                     ⍝ Verify staged changes
]Git Commit "Fixed VALUE ERROR in BankRpt"

]Git Push                              ⍝ Push commits to GitHub
```

## Command Reference

| Command | Description |
|---|---|
| `]Git Add [objects]` | Export APL objects to text files and stage them. Use `.` for all objects. Use `/F=file` for non-APL files. |
| `]Git Branch {name}` | List branches, or create a new branch if a name is given. |
| `]Git Clone [name] [dest] [url]` | Clone a remote repo, create a workspace, and import all APL objects. |
| `]Git Commit [message]` | Commit all staged changes with the given message. |
| `]Git Diff {params}` | Show differences — unstaged changes (default), between commits, or against a remote branch. |
| `]Git Fetch` | Download remote repo metadata without changing local files. |
| `]Git Ignore [file]` | Add a file to `.gitignore`. |
| `]Git Init` | Initialize the workspace's parent directory as a Git repository. |
| `]Git Log {params}` | Show commit history. Pass `--follow [file]` to see history for a single file. |
| `]Git Merge {params}` | Merge fetched changes. Leaves conflict markers in text files if auto-merge fails. |
| `]Git Pull` | Fetch, merge, and update the workspace and text files from the remote repo. |
| `]Git Push {params}` | Push local commits to the remote repo. |
| `]Git Remote {action} {params}` | Manage remote aliases (list, add, remove, rename). |
| `]Git Remove [file]` | Delete a file locally and stage the deletion for the next commit. |
| `]Git Reset {file}` | Unstage changes (all, or for a specific file). |
| `]Git Restore [file/params]` | Restore a file to a specific branch or commit. |
| `]Git Stash {action} {msg}` | Manage temporary stashed changes. *(Under construction)* |
| `]Git Status` | Show the status of tracked and modified files. |
| `]Git Switch [branch]` | Switch to a different branch. |

## Handling Merge Conflicts

If `]Git Pull` or `]Git Merge` encounters a conflict, Git's standard conflict markers are left in the text files:

```
<<<<<<< HEAD
This is the text on the main branch.
=======
This is the incoming text from the feature branch.
>>>>>>> feature-branch
```

Edit the code or text file to resolve the conflict, then stage and commit the fix:

```apl
]Git Add MyFunction
]Git Commit "Resolved merge conflict in MyFunction"
]Git Push
```

## File Types

| Extension | Contents |
|---|---|
| `.aplwfn` | APL+Win function definition (UTF-8 text) |
| `.aplwcv` | APL+Win cover function (UTF-8 text) |
| `.aplwvr` | APL+Win variable (UTF-8 text) |
| `.w3` | APL+Win workspace (binary — not tracked by Git) |
| `.sf` | APL+Win user command file (binary distribution format) |

## Included Objects

<details>
<summary><strong>]Git command objects</strong></summary>

CMDGIT, DATEPLUS, GIT∆DOC, GRPCMDGIT, ⍙COMMAND, ⍙GitAdd, ⍙GitBranch, ⍙GitClone, ⍙GitCommit, ⍙GitDiff, ⍙GitFetch, ⍙GitIgnore, ⍙GitInit, ⍙GitLog, ⍙GitMerge, ⍙GitPull, ⍙GitPush, ⍙GitRemote, ⍙GitRemove, ⍙GitReset, ⍙GitRestore, ⍙GitStatus, ⍙GitSwitch

</details>

<details>
<summary><strong>]TOTXT and ]FROMTXT command objects</strong></summary>

APL2TXT, CMDFROMTXT, CMDTOTXT, FROMTXT∆DOC, GRPCMDFROMTXT, GRPCMDTOTXT, TOTXT∆DOC, TXT2APL

</details>

## Resources

- [Git Cheat Sheet (PDF)](https://education.github.com/git-cheat-sheet-education.pdf) — helpful for understanding the Git concepts behind each `]Git` action
- [Git for Windows download](https://git-scm.com/downloads)
