# The Peripatetic

A zero-configuration Hugo website for contemplative minds who value rigorous learning, classical wisdom, and life's finer pursuits. Built with the PaperMod theme and configured for automatic deployment to GitHub Pages.

## 🚀 Zero-Configuration Deployment

This repository is designed for **immediate deployment** with minimal setup. Follow these 3 simple steps:

### Step 1: Clone and Customize
```bash
git clone [your-repository-url]
cd [repository-name]
```

### Step 2: Enable GitHub Pages
1. Push this repository to GitHub
2. Go to your repository → Settings → Pages
3. Set Source to "GitHub Actions"
4. Your site will automatically deploy!

### Step 3: Start Writing
Add your content by editing the Markdown files in the `content/` directory.

## ✨ What's Included

### Pre-configured Pages
- **About** - Professional summary and career transition story
- **Experience** - Career timeline and accomplishments  
- **Skills** - Technical skills and certifications
- **Projects** - Portfolio of automation and security projects
- **Contact** - Professional contact information
- **Blog** - Categorized posts for documenting your journey

### Blog Categories
- **Mathesis** - Rigorous learning sessions, study notes, and knowledge exploration
- **Iliadic Insights** - Life wisdom, philosophy, and deeper reflections
- **Symposium** - Wine appreciation, tasting notes, and convivial discourse

### Features
- 📱 Mobile-responsive design
- 🎨 Professional, clean appearance
- ⚡ Fast loading and optimized
- 🔍 SEO optimized with proper meta tags
- 📊 Ready for Google Analytics
- 🔗 Social media integration
- 💻 Code syntax highlighting
- 🔄 Automatic GitHub Pages deployment

## 📝 Customization Guide

### Personal Information
Edit `hugo.toml` to update:
```toml
title = "Your Name"
description = "Your professional tagline"
baseURL = "https://yourusername.github.io"

[params]
author = "Your Name"

[[params.socialIcons]]
name = "github"
url = "https://github.com/yourusername"

[[params.socialIcons]]
name = "linkedin" 
url = "https://linkedin.com/in/yourprofile"
```

### Content Updates
1. **About Page**: Edit `content/about/_index.md`
2. **Experience**: Update `content/experience/_index.md`
3. **Skills**: Modify `content/skills/_index.md`
4. **Projects**: Customize `content/projects/_index.md`
5. **Contact**: Update `content/contact/_index.md`

### Adding Blog Posts
Create new posts in the appropriate category:
```bash
# Mathesis posts
hugo new content/posts/mathesis/study-session.md

# Iliadic Insights posts
hugo new content/posts/iliadic-insights/philosophical-reflection.md

# Symposium posts
hugo new content/posts/symposium/wine-discussion.md
```

## 🛠️ Local Development (Optional)

If you want to preview changes locally:

### Prerequisites
- [Hugo Extended](https://gohugo.io/installation/) (v0.150.0+)
- [Git](https://git-scm.com/)

### Commands
```bash
# Start development server
hugo server --buildDrafts

# Build static site
hugo --minify

# Create new content
hugo new content/posts/category/post-name.md
```

## 📂 Project Structure

```
├── .github/workflows/
│   └── hugo.yml              # GitHub Actions deployment
├── content/
│   ├── about/                # About page
│   ├── contact/              # Contact page  
│   ├── experience/           # Experience page
│   ├── projects/             # Projects page
│   ├── skills/               # Skills page
│   └── posts/                # Blog posts
│       ├── learning-journey/
│       ├── network-automation/
│       ├── azure-security/
│       └── career-transition/
├── themes/PaperMod/          # Hugo theme
├── hugo.toml                 # Site configuration
└── README.md                 # This file
```

## 🎯 Perfect For

- **Career Professionals** - Showcasing expertise and professional growth
- **Learning Documentation** - Tracking certification and skill development progress
- **Portfolio Showcase** - Highlighting technical projects and accomplishments
- **Professional Networking** - Building your personal brand and industry connections
- **Knowledge Sharing** - Contributing insights, tutorials, and lessons learned

## 🔧 Troubleshooting

### Site Not Deploying?
1. Check that GitHub Pages is enabled in repository Settings
2. Verify the source is set to "GitHub Actions"
3. Check the Actions tab for build errors
4. Ensure the repository is public (or you have GitHub Pro/Team)

### Content Not Showing?
1. Verify `draft = false` in the frontmatter
2. Check that the file is in the correct directory
3. Ensure proper frontmatter format with `+++` delimiters

### Build Failing?
1. Check the GitHub Actions log for specific errors
2. Verify all required frontmatter fields are present
3. Ensure proper YAML syntax in frontmatter

## 📄 License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.

## 🤝 Contributing

This is a personal portfolio template, but suggestions and improvements are welcome! Please feel free to:
- Open issues for bugs or feature requests
- Submit pull requests for improvements
- Share your own customizations and adaptations

## 🌟 Acknowledgments

- [Hugo](https://gohugo.io/) - The world's fastest framework for building websites
- [PaperMod Theme](https://github.com/adityatelange/hugo-PaperMod) - Clean and fast Hugo theme
- [GitHub Pages](https://pages.github.com/) - Free static site hosting
- [GitHub Actions](https://github.com/features/actions) - Automated deployment workflows

---

**Ready to showcase your professional journey? Start by pushing this repository to GitHub and enabling GitHub Pages!**

*Need help or have questions? Feel free to open an issue or reach out through the contact page once your site is live.*