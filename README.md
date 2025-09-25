# A Gentle Git Primer

> **Disclaimer:** This is a beginner-level starter that deliberately presents a simplified layer over Git's full complexity.

## Core Philosophy: What to Skip Initially

- The Staging Index (`git add`)
- The `git restore` command  
- The `git reset` command
- `git cherry-pick`
- Non-interactive `git rebase`

## What Git Is, In a Nutshell

Git is a snapshot tracker. It takes a full picture (image backup) of your project directory at a point in time, allowing you to revisit, compare, organize, restore, and share different stages of your work.

## Main Scopes

- **Working Directory:** Your current files and folders.
  - **Unsaved Files:** New files Git is not tracking. Git ignores changes to these files.
  - **Saved Files:** Files from a previous snapshot. Git tracks all future changes to these files.
- **Snapshot:** A saved point in time of your entire project. It's a timestamped backup.
- **Branch:** An independent line of development (a stack of snapshots).
- **Repository (Repo):** The hidden `.git` folder where Git stores everything.
- **Stash:** A temporary shelf for your unsaved changes.
- **Remote Repository:** A shared repository on a server (like GitHub or GitLab).
- **Configs:** Customizable settings for your machine, user, or project.
- **Log:** A record of your saved snapshots.
- **Reflog:** A complete history of your actions. This is your safety net for recovering recently deleted work (until Git automatically cleans it after ~30 days).

___

## What You Can Do (Actions Mapped to Concepts)

### BROWSE & LIST
- `git i` - See a short status of changed files.
- `git b` - List all branches.
- `git l` - View the history of snapshots for the current branch.
- `git la` - View the history of snapshots for all branches.
- `git sh` - View stashed changes.
- `git cf` - List all configuration settings.
- `git diff` - Compare changes between files, snapshots, or stages.
- `git g` - Check for orphaned objects (what garbage collector would remove).

### SAVE
- `git save <message>` - Create a new snapshot of all changes.

### EDIT & RECOVER
- `git ecf` - Edit configuration settings.
- `git el` - Interactively reorganize and rewrite snapshot history.

### DELETE
- `git unsave [N]` - Undo the last N saves (default: 1), keeping their changes as modifications.
- `git delete [N]` - Permanently drop the last N snapshots (default: 0), discarding all their changes.
- `git ungit` - Interactively and permanently delete unsaved files.

### NAVIGATE
- `git j <branch>` - Jump to a different branch.
- `git snapshot <hash>` - Time travel to examine an old snapshot. (Use `git j <branch>` to return).

### DISTRIBUTE & SYNC
- `git clone <url>` - Download a remote repository and its complete history.
- `git address <repo> <url>` - Add a remote repository.
- `git vacate <repo>` - Remove a remote repository.
- `git b2b <repo>/<branch>` - Connect the current local branch to a remote branch for easy syncing.
- `git push` - Send your snapshots to a remote repository.
- `git pull` - Retrieve new snapshots from a remote repository.

## Advanced Actions

### INTEGRATE
- `git merge <branch>` - Combines two branches, creating a new snapshot on top of the current branch that integrates both timelines. If changes conflict, Git pauses for manual resolution.
- `git rebase <branch>` - Moves an entire branch to begin on the tip of another, replaying snapshots to create a linear timeline.

___

## Critical Concepts

- **Rewriting History:** Changing existing snapshots (e.g., amended, merged, replayed), which alters their unique IDs (hashes).
- **The HEAD Pointer:** A reference to your current location (e.g., HEAD~, HEAD^, @{N}).
- **Detached HEAD State:** When HEAD points directly to a snapshot instead of a branch tip.
- **Tracking:** Git's monitoring of changes to saved files.
- **Orphan Branch:** A new branch that doesn't share history with the branch it was created from. Its first snapshot becomes the root snapshot.
- **Orphan Snapshot:**  A snapshot that becomes unreachable after operations like deletion or history rewriting. These snapshots are temporarily preserved in the reflog before being garbage collected.
- **Empty Snapshot:** A "dummy" snapshot that contains only a message with no file changes.
- **Garbage Collection:** Git's automated cleanup process that permanently removes unreachable objects after their reflog protection expires.
- **Reflog Protection:** Orphaned objects must pass two expiration checks defined by the Reflog and the Prune Expiration settings before the garbage collector can remove them.
- **Ignoring:** Files Git is configured to skip via `.gitignore`.
- **Git workflow:** Team's agreed-upon branching strategy.

___

## Custom Configuration

Add author details to `~/.gitconfig` under the `[user]` section.

```gitconfig
[user]
  name = Your Name
  email = your@email.com
```

Add the following aliases to `~/.gitconfig` (or `/.git/config`) under an `[alias]` section.

```gitconfig
[alias]
  ### LIST
  i = status --short
  b = branch
  l = log --graph --oneline
  la = log --graph --oneline --all
  sh = stash list
  cf = config --list
  g = fsck --unreachable

  ### SAVE
  save = "!f() { a=\"${@:2}\" && git add ${a:--A} && git commit -m \"$1\"; }; f"

  # Optional - alternatively doable via git rebase -i
  amend = "!f() { a=\"${@:2}\" && git add ${a:--A} && git commit --amend -m \"$1\"; }; f"

  ### EDIT & RECOVERY
  ecf = config --edit
  el = rebase -i

  ### DELETE
  # Undo the last save (or N saves), keeping changes
  unsave = "!f() { a=\"${@:2}\" && git reset --soft HEAD~${1:-1} \"${a:-}\"; }; f"

  # Permanently drop the last N snapshots (default: 0), discarding tracked changes
  # Optional - alternatively doable via git rebase -i
  delete = "!f() { a=\"${@:2}\" && git reset --hard HEAD~${1:-0} \"${a:-}\"; }; f"

  # Safely delete unsaved files (interactive)
  ungit = clean -fd -i

  # Force immediate garbage collecting
  force-erase = gc --prune=now

  ### NAVIGATE
  # Time travel to a snapshot
  snapshot = switch --detach
  ss = switch --detach
  # Jump to a branch
  j = switch

  ### DISTRIBUTE
  address = remote add
  vacate = remote remove
  b2b = branch --set-upstream-to
```
