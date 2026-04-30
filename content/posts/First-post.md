+++
date = '2026-04-27T07:59:32-04:00'
draft = false
title = 'Creating a blog using Hugo'

description = "Step by step guide to deploying a Hugo blog on Cloudflare Pages for free."

+++


## Prerequisites

- Git installed
- GitHub account
- Cloudflare account (free)
- Homebrew (Mac) or winget (Windows)

---

## Step 1: Install Hugo

```bash
# Mac
brew install hugo

# Windows
winget install Hugo.Hugo.Extended

# Verify — note your version number, you'll need it later
hugo version
```

---

## Step 2: Create Site

```bash
hugo new site myblog
cd myblog
git init
```

---

## Step 3: Install PaperMod Theme

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

---

## Step 4: Configure hugo.toml

Open `hugo.toml` in your editor and replace contents with:

```toml
baseURL = 'https://YOURNAME.pages.dev'   # update after Cloudflare setup
locale = 'en-us'
title = 'Your Blog Title'
theme = 'PaperMod'

[params]
  googleAnalytics = ""                   # leave blank to avoid build errors
```

> Note: Leave baseURL as placeholder for now. Update after Step 8.

---

## Step 5: Test Locally

```bash
hugo server
# Open browser: http://localhost:1313
# Ctrl+C to stop
```

> If site loads locally, you're good to proceed.

---

## Step 6: Push to GitHub

```bash
# Create repo at github.com first, then:
git add .
git commit -m "initial site"
git remote add origin https://github.com/yourusername/myblog.git
git push -u origin main
```

---

## Step 7: Deploy on Cloudflare Pages

```
1. Go to cloudflare.com → Log in
2. Left sidebar → Workers & Pages → Create
3. Click "Pages" tab → Connect to Git
4. Authorize GitHub → Select your repo
5. Fill in build settings:
   - Project name:        YOURNAME        ← this becomes YOURNAME.pages.dev
   - Production branch:   main (or master)
   - Framework preset:    Hugo
   - Build command:       hugo
   - Output directory:    public
6. Scroll down → Environment Variables → Add variable:
   - Variable name:  HUGO_VERSION
   - Value:          [your version from Step 1, e.g. 0.160.1]
7. Click Save and Deploy
8. Wait ~60 seconds → site is live
```

> CRITICAL: Set project name carefully here. It becomes your permanent URL.
> Once set, the domain does NOT change even if you rename the project later.
> If you want a different name, delete the project and recreate it.

---

## Step 8: Update baseURL

```toml
# hugo.toml
baseURL = 'https://YOURNAME.pages.dev'   # use your actual Cloudflare URL
```

```bash
git add .
git commit -m "fix baseURL"
git push
```

---

## Daily Workflow: Writing Posts

```bash
# Create new post
hugo new posts/your-post-title.md

# Open the file at: content/posts/your-post-title.md
# Edit it:
+++
date = '2026-04-27T07:00:00Z'
draft = false                    # ← change from true to false
title = 'Your Post Title'
+++

Write your content here in markdown.

# Preview locally
hugo server

# Publish
git add .
git commit -m "your post title"
git push
# Cloudflare auto-deploys in ~60 seconds
```

---

## Markdown Basics

```markdown
# Heading 1
## Heading 2

**bold**
*italic*

- bullet item
- another item

`inline code`

​```python
# code block
print("hello")
​```

[Link text](https://example.com)
```

---

## Common Roadblocks

### Site not loading after deploy
```
Check: Workers & Pages → your project → Deployments → View details
Look for ERROR lines in build log
```

### PaperMod version incompatibility error
```
WARN: Module "PaperMod" is not compatible with this Hugo version: Min 0.146.0
Fix: Update HUGO_VERSION in Cloudflare environment variables to match your local hugo version
     Workers & Pages → project → Settings → Environment Variables → Edit
```

### google_analytics.html not found error
```
Fix: Add this to hugo.toml:
[params]
  googleAnalytics = ""
```

### baseURL wrong / site broken after deploy
```
Fix: Update hugo.toml baseURL to match your actual Cloudflare URL
     Commit and push again
```

### Project name vs domain mismatch
```
Cause: Renaming project in Cloudflare Settings does NOT change the domain
Fix:   Delete project → Recreate with correct name from start
       Workers & Pages → project → Settings → General → Delete project
```

### Want to change free URL
```
Options:
- Free:  Pick different project name at creation time (e.g. koolsing.pages.dev)
- Paid:  Buy custom domain (~$12/year at Cloudflare Registrar)
         Workers & Pages → project → Custom Domains → Add domain
```

### hugo.toml vs hugo.yaml
```
Both work. hugo new site creates hugo.toml by default in newer Hugo versions.
TOML syntax:  key = 'value'
YAML syntax:  key: value
Don't mix formats in same file.
```

---

## Reference

| Thing | Location |
|---|---|
| Site config | `hugo.toml` in root |
| Posts | `content/posts/` |
| Theme | `themes/PaperMod/` |
| Built site | `public/` (auto-generated, don't edit) |
| Cloudflare build logs | Workers & Pages → project → Deployments → View details |
| Hugo version | Run `hugo version` in terminal |
| Cloudflare env vars | Workers & Pages → project → Settings → Environment Variables |
