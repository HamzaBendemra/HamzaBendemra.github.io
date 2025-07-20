# Republishing Medium Articles

## How to Republish with Original Timestamps

### Step 1: Create New Post Files
For each Medium article you want to republish, create a new file in `_posts/` with this naming format:
```
YYYY-MM-DD-article-title.md
```

**Important:** Use the **original publication date** from Medium, not today's date.

### Step 2: Post Template
Use this template for each republished article:

```markdown
---
layout: post
title: "Your Original Article Title"
date: YYYY-MM-DD HH:MM:SS +0400  # Original Medium publication date/time
categories: [category1, category2]
tags: [tag1, tag2, tag3]
original_url: "https://medium.com/@hamzabendemra/your-article-url"
republished: true
---

> *Originally published on [Medium]({{ page.original_url }}) on {{ page.date | date: "%B %d, %Y" }}*

Your article content here...
```

### Step 3: Example File Structure
```
_posts/
├── 2023-05-15-machine-learning-pipeline-best-practices.md
├── 2023-08-22-nlp-in-financial-services.md  
├── 2024-01-10-ai-leadership-lessons.md
└── 2025-07-20-starting-this-blog.md  # Your current post
```

### Step 4: Categories for Organization
Consider these categories for your ML/AI content:
- `machine-learning`
- `artificial-intelligence` 
- `nlp`
- `leadership`
- `data-science`
- `engineering`
- `career`

### Step 5: Medium Import Tips
1. **Copy content** from Medium (use "Export" if available)
2. **Convert formatting** to Markdown
3. **Update images** - save to `/assets/images/posts/` and update links
4. **Add canonical link** to avoid SEO issues
5. **Test locally** before publishing

### Step 6: SEO Considerations
Add to your post front matter:
```yaml
canonical_url: "https://medium.com/@hamzabendemra/original-url"
description: "Brief description for SEO"
image: "/assets/images/posts/article-featured-image.jpg"
```

Would you like me to help you create templates for specific articles you want to republish?
