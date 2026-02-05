# Stratus Website

A professional website for Stratus by Carbon Capital Advisors - providing policy clarity for carbon markets.

## Quick Start - Deploy to GitHub Pages

### Step 1: Upload Files to GitHub

1. Go to your GitHub repository: `github.com/yourusername/stratus-website`
2. Click "Add file" → "Upload files"
3. Drag and drop ALL the files and folders from this zip:
   - index.html
   - about.html
   - platform.html
   - modeling.html
   - solutions.html
   - government.html
   - developers.html
   - investors.html
   - insights.html
   - contact.html
   - css/ (folder with style.css)
   - js/ (folder with main.js)
4. Scroll down and click "Commit changes"

### Step 2: Enable GitHub Pages

1. In your repository, click "Settings"
2. Click "Pages" in the left sidebar
3. Under "Source", select "main" branch
4. Click "Save"
5. Wait 1-2 minutes for deployment

### Step 3: View Your Site

Your site will be live at: `https://yourusername.github.io/stratus-website/`

## Website Structure

```
stratus-website/
├── index.html          # Homepage
├── about.html          # About Stratus
├── platform.html       # Policy Scorecard
├── modeling.html       # Modeling & Intelligence
├── solutions.html      # Solutions overview
├── government.html     # For Governments
├── developers.html     # For Project Developers
├── investors.html      # For Investors & Buyers
├── insights.html       # Research & Insights
├── contact.html        # Contact page
├── css/
│   └── style.css       # Main stylesheet
└── js/
    └── main.js         # Mobile menu functionality
```

## Design Features

- **Color Palette**: Deep blue-gray, slate blue, teal accents
- **Typography**: Georgia (serif) for body, Inter (sans-serif) for headings
- **Responsive**: Mobile-friendly navigation and layout
- **Professional**: Clean, restrained design suitable for institutional clients

## Customization

### Update Contact Information
Edit `contact.html` to add your actual email and office locations.

### Add Logo
Replace the text "Stratus" in the header with an `<img>` tag:
```html
<a href="index.html" class="logo">
    <img src="images/logo.png" alt="Stratus">
</a>
```

### Change Colors
Edit the CSS variables in `css/style.css`:
```css
:root {
  --deep-blue-gray: #2C3E50;
  --slate-blue: #5D7A99;
  --teal: #4A90A4;
  /* Add your colors here */
}
```

### Add Real Research Publications
Edit `insights.html` to link to actual PDFs or research pages.

## Custom Domain (Optional)

To use your own domain (e.g., stratuscarbon.com):

1. In your repository root, create a file named `CNAME`
2. Add your domain: `stratuscarbon.com`
3. Configure DNS with your domain registrar:
   - Add A records pointing to GitHub's IPs
   - Or add a CNAME record pointing to `yourusername.github.io`

See: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site

## Browser Support

- Chrome, Firefox, Safari, Edge (latest versions)
- Mobile browsers (iOS Safari, Chrome Mobile)

## License

© 2026 Stratus by Carbon Capital Advisors. All rights reserved.
