# Safely Syncing `CollectionBuilder/collectionbuilder-csv` into `Digital-Grinnell/collectionbuilder-csv` and then into `Digital-Grinnell/GCCB-project-template`

This guide describes a conservative, reviewable workflow for bringing upstream changes from `CollectionBuilder/collectionbuilder-csv` into the `Digital-Grinnell/collectionbuilder-csv` fork, and then merging the updated fork into the local `GCCB-project-template` repository. GitHub’s documented fork workflow is to configure an `upstream` remote, fetch upstream changes, check out the local default branch, and merge the upstream default branch into it.[cite:12][cite:1]

## Why this order is safest

The safest order is:

1. Sync the fork with upstream.
2. Verify the fork still works.
3. Update the local project-template repo from the fork.
4. Resolve template-specific conflicts only after the fork is current.

This keeps the “vendor/template source” update separate from any project-template customization, which makes conflicts easier to understand and reduces the chance of overwriting local work accidentally.[cite:1][cite:15]

## Before touching anything

Make sure both repositories are clean before merging. A clean working tree means `git status` shows no uncommitted changes; if there are local edits, commit them to a temporary branch or stash them before proceeding.[cite:1]

Recommended precautions:

- Create a backup branch in each repository before the merge.
- Record the current remotes with `git remote -v`.
- Record the current default branch name; many repositories now use `main`, but some older repositories still use `master`, so verify rather than assume.[cite:12][cite:1]
- Prefer a normal merge over a force-push workflow for this task; force pushes are more disruptive and should not be the default for a shared fork.[cite:4][cite:1]

## Part 1: Sync the fork repository

Run these commands inside the local clone of `Digital-Grinnell/collectionbuilder-csv`.

### 1. Verify remotes

```bash
git remote -v
```

You should see `origin` pointing to `Digital-Grinnell/collectionbuilder-csv`. If `upstream` is not already configured, add it like this:[cite:12]

```bash
git remote add upstream https://github.com/CollectionBuilder/collectionbuilder-csv.git
git remote -v
```

GitHub documents `git remote add upstream https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git` as the standard way to connect a fork to its source repository.[cite:12]

### 2. Create a safety branch

```bash
git checkout main
git pull origin main
git checkout -b backup/pre-upstream-sync-$(date +%Y%m%d)
```

A backup branch gives you an easy return point if the merge becomes messy.

### 3. Fetch upstream changes

```bash
git fetch upstream
```

Fetching stores upstream commits locally without changing your working tree, and GitHub notes that upstream branch commits will be available as refs such as `upstream/main`.[cite:1]

### 4. Inspect what will change

```bash
git checkout main
git log --oneline --left-right --graph HEAD...upstream/main
git diff --stat HEAD..upstream/main
```

This step is not required by GitHub’s minimal workflow, but it is a good safety check before merging because it shows divergence and the rough size of the update.

### 5. Merge upstream into the fork locally

```bash
git merge upstream/main
```

GitHub documents this exact pattern—check out the local default branch and merge `upstream/main`—to bring a fork into sync without losing local changes.[cite:1][cite:12]

If there are conflicts:

- Run `git status` to see conflicted files.
- Resolve conflicts carefully, keeping Digital-Grinnell customizations only where they are still intentional.
- After resolving, run `git add <resolved-files>` and then `git commit`.

### 6. Test and review

Before pushing, review the result:

```bash
git status
git diff HEAD~1..HEAD --stat
git log --oneline -5
```

Then run whatever local validation makes sense for this repo, such as a site build, dependency install, or smoke test.

### 7. Push the updated fork

```bash
git push origin main
```

After the local merge is validated, pushing updates the fork on GitHub so it becomes the new source for the next stage.[cite:7][cite:9]

## Part 2: Merge the updated fork into `GCCB-project-template`

Run these commands inside the local `Digital-Grinnell/GCCB-project-template` repository.

### 1. Confirm the source remote

First inspect existing remotes:

```bash
git remote -v
```

If this repo does not already have a remote pointing at `Digital-Grinnell/collectionbuilder-csv`, add one with a clear name such as `template-source`:

```bash
git remote add template-source https://github.com/Digital-Grinnell/collectionbuilder-csv.git
git fetch template-source
```

Using a distinct remote name avoids confusion with any existing `origin` or other remotes.

### 2. Create a safety branch and integration branch

```bash
git checkout main
git pull origin main
git checkout -b backup/pre-template-sync-$(date +%Y%m%d)
git checkout main
git checkout -b work/merge-template-source-$(date +%Y%m%d)
```

Doing the merge work on a dedicated branch gives you a clean review path and makes rollback trivial.

### 3. Review divergence

```bash
git log --oneline --left-right --graph HEAD...template-source/main
git diff --stat HEAD..template-source/main
```

This shows how far the project template has drifted from the fork and where conflicts are most likely.

### 4. Merge the updated fork

```bash
git merge --allow-unrelated-histories template-source/main
```

This is the same fetch-and-merge model GitHub recommends for upstream synchronization, applied here to a second repository relationship.[cite:1][cite:12]

If conflicts appear, classify them before editing:

- **Keep upstream/fork version** for files that should remain template-managed.
- **Keep project-template version** for local customizations that are intentionally different.
- **Manually combine** when both sides changed valid content.

Useful commands during conflict resolution:

```bash
git status
git diff
# optional file-level choices
git checkout --ours path/to/file
git checkout --theirs path/to/file
```

Be careful with `--ours` and `--theirs`; in a merge, “ours” is the current branch in `GCCB-project-template`, and “theirs” is the incoming `template-source` branch.

### 5. Test the merged template repo

At minimum, check:

- The repo still builds successfully.
- Configuration files still reflect local project needs.
- Any Digital-Grinnell customizations still behave as expected.
- Generated site output, workflows, includes, and dependency files look sane.

Then review the final delta:

```bash
git status
git diff main..HEAD --stat
git log --oneline --decorate -10
```

### 6. Commit and merge back

If the merge was done on a work branch and everything looks good:

```bash
git checkout main
git merge --no-ff work/merge-template-source-$(date +%Y%m%d)
git push origin main
```

Using a dedicated work branch keeps the reviewable integration commit separate from routine work.

## Recommended conflict strategy

When the upstream repo has been evolving for a while, avoid trying to resolve everything from memory. Use this triage approach:

- Treat infrastructure files first, such as workflow files, dependency manifests, build scripts, and shared includes.
- Then resolve content and configuration files.
- Leave generated artifacts or lockfiles for last, because they are often easiest to regenerate after the merge.

This staged approach reduces the chance of making inconsistent fixes across related files.

## Commands checklist

Replace `main` with the actual branch name, usually `main`.

### In `Digital-Grinnell/collectionbuilder-csv`

```bash
git remote -v
git remote add upstream https://github.com/CollectionBuilder/collectionbuilder-csv.git   # if needed
git checkout main
git pull origin main
git checkout -b backup/pre-upstream-sync-YYYYMMDD
git fetch upstream
git checkout main
git log --oneline --left-right --graph HEAD...upstream/main
git diff --stat HEAD..upstream/main
git merge upstream/main
# resolve conflicts, test
git push origin main
```

### In `Digital-Grinnell/GCCB-project-template`

```bash
git remote -v
git remote add template-source https://github.com/Digital-Grinnell/collectionbuilder-csv.git   # if needed
git fetch template-source
git checkout main
git pull origin main
git checkout -b backup/pre-template-sync-YYYYMMDD
git checkout -b work/merge-template-source-YYYYMMDD
git log --oneline --left-right --graph HEAD...template-source/main
git diff --stat HEAD..template-source/main
git merge template-source/main
# resolve conflicts, test
git checkout main
git merge --no-ff work/merge-template-source-YYYYMMDD
git push origin main
```

## Notes specific to this setup

Because `GCCB-project-template` was cloned from a fork of `collectionbuilder-csv`, it is best to think of the process as a two-hop update path: first sync the fork from `CollectionBuilder/collectionbuilder-csv`, then merge that refreshed fork into the project-template repo. GitHub’s guidance supports the fetch-and-merge workflow at each hop, and using named remotes keeps those relationships explicit and auditable.[cite:1][cite:12][cite:15]

If there has been substantial divergence, consider opening the merge in a dedicated branch on GitHub as well, so the final diff can be reviewed in the web UI before it lands in the default branch.
