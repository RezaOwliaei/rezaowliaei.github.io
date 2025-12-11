# Reza Owliaei's Blog

Personal blog hosted on GitHub Pages: [https://rezaowliaei.github.io](https://rezaowliaei.github.io)

## About

This blog explores software development topics including TypeScript, clean code, software architecture, and design patterns.

## Adding New Posts

1. Create your post as an HTML file in the `posts/` directory
2. Update `posts-manifest.json` with the new post metadata:
   ```json
   {
     "id": "your-post-slug",
     "title": "Your Post Title",
     "date": "YYYY-MM-DD",
     "description": "Brief description of your post",
     "tags": ["Tag1", "Tag2"],
     "url": "./posts/your-post.html"
   }
   ```
3. Commit and push to GitHub - the site will update automatically

## Local Development

Simply open `index.html` in your browser to preview the blog locally.

## Structure

```
.
├── index.html              # Homepage with post listing
├── posts-manifest.json     # Post metadata registry
├── styles/
│   └── main.css           # Shared styles
├── posts/
│   └── *.html            # Individual blog posts
└── 404.html              # Custom error page
```

## Design

The blog uses a premium monochrome theme with:
- Google Sans Flex typography
- Smooth animations and transitions
- Fully responsive design
- Consistent design language across all pages

---

Built with ❤️ by Reza Owliaei