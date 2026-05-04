# Git & GitHub class task

As part of devops team you are required to learn the team's collaboration flow on GitHub: initialize a repository locally, publish it to a remote, branch off to make a change in parallel with `main`, and resolve a merge conflict directly through the GitHub web UI.

## Part 1 — Set up the repository

- Create a new empty repository under your user name in GitHub named: `liber-arena`
    - Do **not** initialize it with a README, `.gitignore` or license (we want it empty so the first push comes from your machine).
- On your machine, create a project directory and initialize it:
    - `mkdir liber-arena && cd liber-arena`
    - `git init -b main`
- Configure your identity for this repo if you have not set it globally:
    - `git config user.name "<your name>"`
    - `git config user.email "<your github email>"`

## Part 2 — Add files and push to remote

- Inside the project create the following files with **exactly** this content:
    - `README.md`
        ```
        # liber-arena
        Sandbox repo used during the git & github class task.
        ```
    - `servers.conf`
        ```
        # Active upstream servers
        primary   = server-01.example.com
        secondary = server-02.example.com
        port      = 8080
        ```
    - `.gitignore`
        ```
        *.log
        .DS_Store
        ```
- Stage, commit and connect the remote, then push:
    - `git add README.md servers.conf .gitignore`
    - `git commit -m "initial commit"`
    - `git remote add origin git@github.com:<your-user>/liber-arena.git`
    - `git push -u origin main`
- Verify on GitHub that the three files appear on the `main` branch.

## Part 3 — Create a side branch and edit a file

- From `main`, create and switch to a feature branch:
    - `git checkout -b feature/change-port`
- Edit `servers.conf` and change the `port` line to:
    ```
    port      = 9090
    ```
- Commit and push the branch:
    - `git add servers.conf`
    - `git commit -m "switch upstream port to 9090"`
    - `git push -u origin feature/change-port`

## Part 4 — Force a conflict on `main`

The goal of this step is to make `main` move forward in a way that conflicts with the feature branch. The conflict must be on the **same line** of the **same file**.

- Switch back to `main`:
    - `git checkout main`
- Edit `servers.conf` and change the `port` line to a **different** value than the one you used on the feature branch:
    ```
    port      = 7070
    ```
- Commit and push directly to `main`:
    - `git add servers.conf`
    - `git commit -m "hotfix: move upstream port to 7070"`
    - `git push origin main`

At this point `main` and `feature/change-port` both modified the same line of `servers.conf` to different values. Any attempt to merge them will conflict.

## Part 5 — Open a Pull Request and resolve the conflict on GitHub

- On GitHub open a Pull Request:
    - base: `main`
    - compare: `feature/change-port`
    - Title: `Change upstream port`
- GitHub will display the banner **"This branch has conflicts that must be resolved"** and disable the merge button.
- Click **Resolve conflicts** to open the GitHub web conflict editor.
- In the editor you will see the conflict markers in `servers.conf`:
    ```
    <<<<<<< feature/change-port
    port      = 9090
    =======
    port      = 7070
    >>>>>>> main
    ```
    - Delete the `<<<<<<<`, `=======` and `>>>>>>>` marker lines.
    - Keep a single, final value for `port` (decide which one wins, or pick a third value such as `8443`).
- Click **Mark as resolved**, then **Commit merge** — this creates a merge commit on the feature branch.
- Back on the PR page, click **Merge pull request** and confirm. The PR is now merged into `main`.

## Part 6 — Sync your local clone

- Locally, bring the resolved `main` down and clean up the merged branch:
    - `git checkout main`
    - `git pull --ff-only origin main`
    - `git branch -d feature/change-port`
    - `git push origin --delete feature/change-port`
- Confirm with `git log --oneline --graph --all` that you see the merge commit produced by the GitHub web UI.
