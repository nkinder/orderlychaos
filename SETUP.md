# Orderly Chaos — Blog Setup Checklist

Follow these steps in order. Each section has a ✅ checkbox — mark them off as you go.

---

## Part 1: GitHub Repository

### 1.1 Create the repo

> **Why not `nkinder.github.io`?** You already have a `nkinder.github.io` repo hosting images linked from your Smart Card Removinator README. If we put the blog there, GitHub would redirect `nkinder.github.io` → `orderlychaos.io` and break those image embeds. GitHub Pages custom domains work on *any* repo name, so we use a separate repo and leave the old one untouched.

1. Go to [github.com/new](https://github.com/new)
2. Set repository name to: **`orderlychaos`**
3. Set to **Public**
4. Do NOT initialize with a README (we'll push our own files)
5. Click **Create repository**

---

### 1.2 Enable GitHub Pages with GitHub Actions

1. Go to your new repo → **Settings** → **Pages** (left sidebar)
2. Under **Source**, select **GitHub Actions** (not "Deploy from a branch")
3. Leave everything else as-is for now — the workflow will handle it

---

## Part 2: Push the Site Files

Run these commands in your terminal from wherever you want to keep this project locally. 

> **Note:** The `orderlychaos/` folder in your Blog workspace folder contains all the site files.

```bash
# Navigate to the site folder (adjust path to where Cowork saved it)
cd ~/Blog/orderlychaos

# Initialize git
git init
git branch -M main

# Add the PaperMod theme as a submodule
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod themes/PaperMod

# Stage all files
git add .
git commit -m "Initial site scaffold with Hugo + PaperMod"

# Connect to GitHub and push
git remote add origin https://github.com/nkinder/orderlychaos.git
git push -u origin main
```

After pushing, go to your repo → **Actions** tab. You should see a workflow called "Deploy Hugo to GitHub Pages" running. It takes about 60 seconds. Once it shows a green checkmark, your site is building — it won't have a pretty URL yet until Part 4 connects the custom domain.

---

## Part 3: Configure GoDaddy DNS for orderlychaos.io

1. Log into your GoDaddy account
2. Go to **My Products** → find `orderlychaos.io` → click **DNS**
3. Delete any existing A records for the `@` host (the default GoDaddy parking records)
4. Add these **4 A records** (one at a time):

   | Type | Name | Value | TTL |
   |------|------|-------|-----|
   | A | @ | 185.199.108.153 | 600 |
   | A | @ | 185.199.109.153 | 600 |
   | A | @ | 185.199.110.153 | 600 |
   | A | @ | 185.199.111.153 | 600 |

5. Add a **CNAME record** for `www`:

   | Type | Name | Value | TTL |
   |------|------|-------|-----|
   | CNAME | www | nkinder.github.io | 3600 |

   > The CNAME for `www` still points to `nkinder.github.io` — that's correct. GitHub uses this to verify ownership and route traffic, even though the blog lives in a different repo. GitHub's infrastructure handles the mapping from there to `orderlychaos.io`.

6. Save all changes. DNS propagation takes anywhere from a few minutes to 24 hours.

---

## Part 4: Connect the Custom Domain in GitHub

### 4.1 Verify your domain (security step — do this first)

1. Go to [github.com/settings/pages](https://github.com/settings/pages)
2. Click **Add a domain** under "Verified domains"
3. Enter `orderlychaos.io`
4. GitHub will show you a TXT record to add to your DNS
5. Go back to GoDaddy DNS and add that TXT record
6. Return to GitHub and click **Verify** — you may need to wait a few minutes for DNS to propagate

### 4.2 Set the custom domain on your repo

1. Go to your `orderlychaos` repo → **Settings** → **Pages**
2. Under **Custom domain**, enter: `orderlychaos.io`
3. Click **Save**
4. GitHub will check that the CNAME file exists (it does — it's in `static/CNAME`) and that DNS is configured
5. Wait for the **Enforce HTTPS** checkbox to become available (usually within an hour, sometimes up to 24h for cert provisioning)
6. Once available, check **Enforce HTTPS**

Your site is now live at **https://orderlychaos.io** 🎉

---

## Part 5: Local Preview Setup (Optional but Recommended)

Install Hugo Extended on your Mac so you can preview posts before pushing:

```bash
# Using Homebrew
brew install hugo

# Preview the site locally
cd ~/Blog/orderlychaos
hugo server -D    # -D includes draft posts
```

Open [http://localhost:1313](http://localhost:1313) in your browser. Changes are hot-reloaded.

---

## Part 6: Writing Your First Real Post

When you're ready to write a post:

```bash
# Create a new post from the template
hugo new posts/my-post-title.md
```

This creates a file in `content/posts/` using the `archetypes/default.md` template (which is already set up with the standard post structure).

When the post is ready to publish, change `draft: true` to `draft: false` in the front matter, then commit and push. GitHub Actions will deploy it within ~60 seconds.

---

## Quick Reference: File Structure

```
orderlychaos/   (the new blog repo)
├── .github/workflows/hugo.yml   ← Auto-deploys on every push to main
├── archetypes/default.md        ← Template for new posts
├── content/
│   ├── _index.md               ← Home page (text set in hugo.toml)
│   ├── about.md                ← About page
│   ├── search.md               ← Search page
│   └── posts/                  ← Your blog posts go here
│       └── welcome-to-orderly-chaos.md
├── static/
│   └── CNAME                   ← Custom domain config
├── themes/PaperMod/            ← Theme (git submodule)
├── .gitmodules                 ← Tells git about the theme submodule
├── .gitignore
└── hugo.toml                   ← Main site configuration
```

---

## Troubleshooting

**Build fails in GitHub Actions:** Check the Actions tab for error details. Most common cause is the PaperMod submodule not being fetched — make sure you ran `git submodule add` before pushing.

**Custom domain not working:** DNS propagation can be slow. Use [dnschecker.org](https://dnschecker.org) to verify your A records are live. Try `dig orderlychaos.io A` from your terminal.

**HTTPS not available:** Wait up to 24 hours after setting the custom domain. GitHub provisions a Let's Encrypt cert automatically.

**Posts not showing up:** Make sure `draft: false` is set in the post front matter.
