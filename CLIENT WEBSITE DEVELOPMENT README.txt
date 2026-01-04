# Client Website Development Workflow

A step-by-step guide for developing and publishing a static website for a client using GitHub, Netlify, and a custom domain.

---

## 📋 Overview

This workflow uses:
- **GitHub** - Version control and source of truth
- **Netlify** - Free hosting with automatic deployments
- **Custom Domain** - Client's domain (e.g., from IONOS, GoDaddy, etc.)

**Key Principle:** All changes go through **Git → GitHub → Netlify**. Never deploy directly without committing to Git first.

---

## 🚀 Phase 1: Project Setup

### Step 1: Create Project Folder Structure

```
client-website/
├── index.html          # Homepage
├── style.css           # Main stylesheet
├── script.js           # JavaScript (if needed)
├── assets/             # Images, logos, icons
│   ├── logo.png
│   └── images/
├── services/           # Additional pages (optional)
│   └── service-1.html
├── netlify.toml        # Netlify configuration
├── .gitignore          # Git ignore file
└── README.md           # Project documentation
```

### Step 2: Create Essential Files

#### `.gitignore`
```
.DS_Store
Thumbs.db
*.log
node_modules/
.env
*.bak
```

#### `netlify.toml`
```toml
[build]
  publish = "."
  command = ""

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "SAMEORIGIN"
    X-Content-Type-Options = "nosniff"
    X-XSS-Protection = "1; mode=block"
    Referrer-Policy = "strict-origin-when-cross-origin"
    Cache-Control = "public, max-age=3600"

[[headers]]
  for = "/assets/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
  for = "/*.css"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
  for = "/*.js"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"
```

---

## 🔧 Phase 2: GitHub Repository Setup

### Step 1: Create GitHub Repository

1. Go to https://github.com
2. Click **"New repository"**
3. Name it (e.g., `alnwick-hairdressers-website`)
4. Set to **Private** (for client work)
5. Click **"Create repository"**

### Step 2: Initialize Local Git & Push

```powershell
# Navigate to project folder
cd "C:\path\to\client-website"

# Initialize Git
git init

# Add all files
git add .

# Initial commit
git commit -m "Initial website setup"

# Add remote origin (replace with your repo URL)
git remote add origin https://github.com/YOUR_USERNAME/alnwick-hairdressers-website.git

# Push to GitHub
git branch -M main
git push -u origin main
```

---

## 🌐 Phase 3: Netlify Deployment

### Step 1: Connect Netlify to GitHub

1. Go to https://app.netlify.com
2. Sign up/login (free account)
3. Click **"Add new site"** → **"Import an existing project"**
4. Choose **GitHub**
5. Authorize Netlify to access your repositories
6. Select your repository
7. Configure build settings:
   - **Branch to deploy:** `main`
   - **Build command:** (leave empty for static HTML)
   - **Publish directory:** `.` or leave empty
8. Click **"Deploy site"**

### Step 2: Note Your Site Details

After deployment, note:
- **Site Name:** e.g., `random-name-123`
- **Site ID:** Found in Site settings → General
- **Netlify URL:** e.g., `https://random-name-123.netlify.app`
- **Admin URL:** `https://app.netlify.com/projects/random-name-123`

---

## 🔗 Phase 4: Custom Domain Setup

### Step 1: Add Domain in Netlify

1. In Netlify dashboard, go to **Domain settings**
2. Click **"Add custom domain"**
3. Enter the client's domain (e.g., `alnwickhairdressers.co.uk`)
4. Click **"Verify"**
5. Note the DNS configuration instructions

### Step 2: Configure DNS at Domain Registrar

#### For Root Domain (e.g., `alnwickhairdressers.co.uk`)

Add an **A record**:
- **Host/Name:** `@`
- **Points to:** `75.2.60.5` (Netlify's load balancer)

Add a **CNAME record** for www:
- **Host/Name:** `www`
- **Points to:** `[your-site-name].netlify.app`

#### For Subdomain (e.g., `www.alnwickhairdressers.co.uk`)

Add a **CNAME record**:
- **Host/Name:** `www`
- **Points to:** `[your-site-name].netlify.app`

### Step 3: Enable HTTPS

1. Wait for DNS propagation (5-30 minutes, up to 24 hours)
2. Check status at https://dnschecker.org
3. In Netlify: **Domain settings** → **HTTPS**
4. Click **"Verify DNS configuration"**
5. Click **"Provision certificate"**
6. Wait for SSL activation (usually 1-2 minutes)

---

## 🔄 Phase 5: Development Workflow

### Making Changes

```powershell
# 1. Pull latest changes
git pull origin main

# 2. Make your edits to HTML/CSS/JS files

# 3. Test locally (open index.html in browser)

# 4. Commit changes
git add .
git commit -m "Brief description of changes"

# 5. Push to GitHub
git push origin main

# 6. Netlify auto-deploys within 1-2 minutes
```

### Using Netlify CLI (Optional but Recommended)

#### Install Netlify CLI
```powershell
npm install -g netlify-cli
```

#### Authenticate
```powershell
netlify login
```

#### Link to Site
```powershell
cd "C:\path\to\client-website"
netlify link
```

#### Preview Deployment (Test Before Live)
```powershell
netlify deploy --dir=.
```
This creates a temporary preview URL without affecting the live site.

#### Production Deployment
```powershell
netlify deploy --prod --dir=.
```

---

## 📁 Recommended Project Files

### Minimum Required
- `index.html` - Homepage
- `style.css` - Styles
- `netlify.toml` - Netlify config

### Recommended Additions
- `script.js` - JavaScript functionality
- `assets/` - Images and media
- `favicon.ico` or `favicon.png` - Browser tab icon
- `robots.txt` - Search engine instructions
- `sitemap.xml` - For SEO

### Example `robots.txt`
```
User-agent: *
Allow: /
Sitemap: https://alnwickhairdressers.co.uk/sitemap.xml
```

---

## 📊 Phase 6: Analytics & Forms (Optional)

### Add Google Analytics

Add before `</head>` in `index.html`:
```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

### Contact Form Options

#### Option 1: Formspree (Easiest)
1. Sign up at https://formspree.io (free: 50 submissions/month)
2. Create form, get form ID
3. Add to HTML:
```html
<form action="https://formspree.io/f/YOUR_FORM_ID" method="POST">
  <input type="text" name="name" placeholder="Your Name" required>
  <input type="email" name="email" placeholder="Your Email" required>
  <textarea name="message" placeholder="Your Message" required></textarea>
  <button type="submit">Send</button>
</form>
```

#### Option 2: Web3Forms (Free, unlimited)
1. Sign up at https://web3forms.com
2. Get access key
3. Add to form:
```html
<form action="https://api.web3forms.com/submit" method="POST">
  <input type="hidden" name="access_key" value="YOUR_ACCESS_KEY">
  <input type="text" name="name" required>
  <input type="email" name="email" required>
  <textarea name="message" required></textarea>
  <button type="submit">Send</button>
</form>
```

#### Option 3: Netlify Forms
Add `netlify` attribute to form:
```html
<form name="contact" method="POST" data-netlify="true">
  <input type="text" name="name" required>
  <input type="email" name="email" required>
  <textarea name="message" required></textarea>
  <button type="submit">Send</button>
</form>
```

---

## ✅ Pre-Launch Checklist

### Technical
- [ ] All pages load correctly
- [ ] All links work (internal and external)
- [ ] Images load and are optimized
- [ ] Mobile responsive design works
- [ ] HTTPS enabled (green padlock)
- [ ] Forms submit correctly
- [ ] No console errors

### SEO
- [ ] Page titles set (`<title>` tag)
- [ ] Meta descriptions added
- [ ] Favicon added
- [ ] robots.txt created
- [ ] sitemap.xml created (optional)

### Content
- [ ] Contact information correct
- [ ] Opening hours accurate
- [ ] Prices up to date
- [ ] Images have alt text
- [ ] Privacy policy page (if collecting data)

### Performance
- [ ] Images compressed
- [ ] CSS/JS minified (optional)
- [ ] Test on PageSpeed Insights

---

## 🆘 Troubleshooting

### Site Not Updating After Push
1. Check Netlify dashboard for deploy status
2. Clear browser cache (Ctrl+Shift+R)
3. Wait 2-3 minutes for CDN propagation

### DNS Not Propagating
1. Verify DNS records are correct
2. Check at https://dnschecker.org
3. Wait up to 24 hours (usually 5-30 minutes)

### SSL Certificate Issues
1. Ensure DNS is fully propagated first
2. In Netlify: Domain settings → HTTPS → Verify
3. Force HTTPS in Netlify settings

### Rollback to Previous Version
```powershell
# Via Git
git revert HEAD --no-edit
git push origin main

# Via Netlify CLI
netlify rollback
```

---

## 📞 Key URLs & Resources

### Netlify
- Dashboard: https://app.netlify.com
- Docs: https://docs.netlify.com
- CLI Docs: https://docs.netlify.com/cli/get-started/

### GitHub
- Dashboard: https://github.com
- Docs: https://docs.github.com

### DNS Checker
- https://dnschecker.org

### Performance Testing
- https://pagespeed.web.dev
- https://gtmetrix.com

---

## 📝 Project Handover Checklist

When handing over to client:

- [ ] Provide GitHub repository access (or transfer ownership)
- [ ] Provide Netlify account access (or transfer site)
- [ ] Document domain registrar login details
- [ ] Provide this README for future reference
- [ ] Document any form service accounts (Formspree, etc.)
- [ ] Document Google Analytics access

---

## 🎯 Quick Reference Commands

```powershell
# Clone existing project
git clone https://github.com/USERNAME/REPO.git

# Pull latest
git pull origin main

# Commit and push
git add .
git commit -m "Description"
git push origin main

# Netlify preview
netlify deploy --dir=.

# Netlify production
netlify deploy --prod --dir=.

# Check Netlify status
netlify status

# Open live site
netlify open:site
```

---

**Ready to build!** Start with Phase 1 and work through each phase systematically.
