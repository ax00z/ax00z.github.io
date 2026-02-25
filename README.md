# amir-blog

Personal blog and portfolio built with [Astro](https://astro.build/). Deployed on GitHub Pages.

## Setup

```bash
# Clone the repo
git clone https://github.com/ax00z/ax00z.github.io.git
cd ax00z.github.io

# Install dependencies
npm install

# Start dev server
npm run dev

# Build for production
npm run build
```

## Writing a new post

Create a new `.md` file in `src/content/blog/`:

```markdown
---
title: "Your Post Title"
description: "A short description for previews and SEO."
date: "2025-03-01"
tags: ["tag1", "tag2"]
---

Your content here. Supports full markdown — code blocks, images, lists, links.
```

The post will automatically appear on the blog index and homepage.

## Deployment

Push to `main` branch. GitHub Actions handles the build and deploys to GitHub Pages automatically.

**First-time setup:** In your GitHub repo settings, go to Pages → Source → select "GitHub Actions".

## Project structure

```
src/
├── components/     # Header, Footer
├── content/blog/   # Markdown blog posts
├── layouts/        # Page layouts (Base, BlogPost)
├── pages/          # Routes (home, blog, projects, about)
└── styles/         # Global CSS
```
