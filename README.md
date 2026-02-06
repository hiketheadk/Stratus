# Stratus Website

A professional website for Stratus by Carbon Capital Advisors - a platform providing policy clarity for carbon markets.

## 📁 What's Included

This package contains a complete, ready-to-deploy website with:

- **9 HTML pages**: Homepage, About, Policy Scorecard, Modeling, Government Advisory, For Developers, For Investors, Insights, and Contact
- **Professional CSS styling**: Clean, institutional design with responsive layouts
- **Mobile-friendly navigation**: Hamburger menu for small screens
- **Consistent branding**: Based on the design specifications from your business plan

## 🚀 How to Upload to GitHub Pages

### Step 1: Upload Files to Your Repository

1. Go to your GitHub repository: `github.com/YOUR-USERNAME/stratus-website`
2. Click the **"Add file"** button → **"Upload files"**
3. Drag and drop ALL files from this folder into the upload area:
   - All `.html` files (index.html, about.html, etc.)
   - The entire `css` folder
   - This README.md file (optional)
4. Scroll down and click **"Commit changes"**

### Step 2: Enable GitHub Pages

1. In your repository, click **"Settings"** (top menu)
2. Click **"Pages"** in the left sidebar
3. Under **"Source"**, select **"main"** branch
4. Click **"Save"**

### Step 3: View Your Site

After a few minutes, your site will be live at:
```
https://YOUR-USERNAME.github.io/stratus-website/
```

You'll see a blue box at the top of the Settings > Pages section with your URL once it's ready.

## 📄 Page Structure

### Main Pages
- **index.html** - Homepage with hero section and key pillars
- **about.html** - About Stratus and approach
- **policy-scorecard.html** - Policy Scorecard platform overview
- **modeling.html** - Modeling & Market Intelligence
- **government-advisory.html** - Services for governments
- **for-developers.html** - Solutions for project developers
- **for-investors.html** - Solutions for investors & buyers
- **insights.html** - Research & publications
- **contact.html** - Contact information and access requests

### Navigation
All pages share consistent navigation:
Home | Platform | Solutions | Insights | About | Contact

## 🎨 Design Features

- **Color Palette**: Professional blues and grays (no bright greens or loud colors)
- **Typography**: Inter (headings/UI) and Georgia (body text)
- **Layout**: Clean, minimal, data-driven aesthetic
- **Responsive**: Works on desktop, tablet, and mobile
- **Credibility**: Designed to appeal to governments, investors, and institutions

## 🔧 Customization

### Changing Colors
Edit `css/style.css` and modify the `:root` variables at the top:
```css
:root {
  --deep-blue-gray: #2C3E50;
  --slate-blue: #5D7A99;
  --teal: #4A90A4;
  ...
}
```

### Updating Content
Simply edit the HTML files directly. All text is in plain HTML with clear structure.

### Adding Pages
1. Copy an existing HTML file
2. Update the content
3. Add a link to it in the navigation (in all files)
4. Upload to GitHub

## 📱 Mobile Support

The site automatically adapts to mobile screens:
- Navigation collapses to hamburger menu
- Text resizes appropriately
- Cards stack vertically
- Touch-friendly buttons

## 🌐 Custom Domain (Optional)

To use your own domain (e.g., stratuscarbon.com):
1. In Settings > Pages, enter your domain under "Custom domain"
2. Follow GitHub's instructions to configure DNS
3. Your site will be accessible at your custom domain

## 📝 Notes

- All links work between pages
- Footer appears on every page
- Hero section only on homepage
- Consistent professional tone throughout
- No external dependencies except Google Fonts

## ✅ Testing

After uploading, check:
- [ ] All pages load correctly
- [ ] Navigation links work
- [ ] Mobile menu functions
- [ ] Styling appears correctly
- [ ] Text is readable and professional

## 🆘 Troubleshooting

**Site not showing up?**
- Wait 5-10 minutes after enabling Pages
- Check Settings > Pages for the green "Your site is published" message
- Make sure index.html is in the root directory (not in a subfolder)

**CSS not loading?**
- Verify the `css` folder is uploaded
- Check that file paths are correct (should be `css/style.css`)

**Navigation not working on mobile?**
- The hamburger menu requires JavaScript (already included in each page)
- Make sure you uploaded the complete HTML files

## 📧 Questions?

If you need help customizing or have questions about the code, feel free to ask!

---

Built by Claude for Carbon Capital Advisors
