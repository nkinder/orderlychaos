# Task: Initialize and publish this Hugo blog to GitHub

This directory is a pre-scaffolded Hugo site for a blog called Orderly Chaos.
Your job is to get it pushed to GitHub and deployed via GitHub Pages.

## What's already here

- `hugo.toml` — site config (Hugo + PaperMod theme, baseURL is orderlychaos.io)
- `.github/workflows/hugo.yml` — GitHub Actions workflow that builds and deploys the site
- `content/` — starter pages (home, about, search, welcome post)
- `static/CNAME` — contains `orderlychaos.io` (custom domain config)
- `.gitmodules` — points to PaperMod theme at https://github.com/adityatelange/hugo-PaperMod
- `.gitignore`
- `archetypes/default.md` — post template

## What you need to do

Work in this directory. Run these steps in order:

### 1. Initialize git
```
git init
git branch -M main
```

### 2. Add the PaperMod theme as a submodule
```
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

### 3. Create the GitHub repo
```
gh repo create nkinder/orderlychaos --public --description "Orderly Chaos — technical blog"
```

### 4. Commit and push everything
```
git add .
git commit -m "Initial Hugo site scaffold with PaperMod theme"
git remote add origin https://github.com/nkinder/orderlychaos.git
git push -u origin main
```

### 5. Enable GitHub Pages on the repo
GitHub Pages needs to be set to deploy via GitHub Actions (not a branch).
Use the gh CLI to configure this:
```
gh api repos/nkinder/orderlychaos/pages \
  --method POST \
  -f build_type=workflow \
  -f source='{"branch":"main","path":"/"}' 2>/dev/null || \
gh api repos/nkinder/orderlychaos/pages \
  --method PUT \
  -f build_type=workflow
```

### 6. Verify the Actions workflow is running
```
gh run list --repo nkinder/orderlychaos
```

Wait for the workflow named "Deploy Hugo to GitHub Pages" to complete.
If it fails, check the logs:
```
gh run view --repo nkinder/orderlychaos --log-failed
```

## Done when

- `gh run list` shows a completed (green) run for "Deploy Hugo to GitHub Pages"
- The site is accessible at https://nkinder.github.io/orderlychaos/ or similar
  (the custom domain orderlychaos.io will be connected separately via DNS — not your job here)

## Notes

- Do NOT touch the existing `nkinder.github.io` repo — it hosts images used by another project
- The `themes/PaperMod/` directory must be added as a git submodule (not just copied in)
  so that GitHub Actions can fetch it during builds
- If `gh repo create` says the repo already exists, skip that step and just add the remote
