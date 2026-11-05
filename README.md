# Fish Log Website

Static website for the Fish Log mobile app, providing the Privacy Policy, Terms of Use, Support, and Account Deletion pages required by the Apple App Store and Google Play Store.

## Deployment (GitHub Pages)

### Quick Setup

1. Create a new GitHub repository named `fishlogco.github.io`
2. Push the contents of this `website/` folder to the repository:

```bash
cd website
git init
git add .
git commit -m "Initial website deployment"
git branch -M main
git remote add origin https://github.com/fishlogco/fishlogco.github.io.git
git push -u origin main
```

3. GitHub Pages will automatically deploy from the `main` branch
4. Your site will be live at: **https://fishlogco.github.io**

### Custom Domain (Later)

When you're ready to use a custom domain (e.g., `fishlog.app` or `fishlog.co`):

1. Add a `CNAME` file to this folder containing your domain name
2. In your domain registrar, add a CNAME record pointing to `fishlogco.github.io`
3. In the GitHub repository settings, enable "Enforce HTTPS"
4. Update the `WEBSITE_BASE_URL` constant in `src/screens/LegalDocumentScreen.tsx`

## Pages

| Page | File | URL | Purpose |
|------|------|-----|---------|
| Home | `index.html` | `/` | App landing page with features and download links |
| Privacy Policy | `privacy.html` | `/privacy.html` | Required by App Store and Play Store |
| Terms of Use | `terms.html` | `/terms.html` | Required by both stores |
| Support | `support.html` | `/support.html` | Contact info and FAQ |
| Delete Account | `delete-account.html` | `/delete-account.html` | Required by both stores (account deletion instructions) |

Also served: `robots.txt` and `sitemap.xml`. Every page carries a canonical
URL plus Open Graph / Twitter card tags pointing at `images/og-image.png`
(1200x630) -- regenerate that image if the hero copy or screenshot changes.

## App Store URLs

Use these URLs when submitting to the app stores:

- **Privacy Policy URL:** `https://fishlogco.com/privacy.html`
- **Terms of Use URL:** `https://fishlogco.com/terms.html`
- **Support URL:** `https://fishlogco.com/support.html`
- **Account Deletion URL:** `https://fishlogco.com/delete-account.html`

## Updating Content

All pages are plain HTML/CSS with no build step required. Edit the `.html` files directly, commit, and push. Changes deploy automatically.

### Screenshots

The pages reference `images/*.webp`, not the `.png` originals. The PNGs are the
1290x2796 source exports; the WebP copies are resized to roughly 2x their
largest on-page size, which takes the home page from ~8.2 MB of images to
~130 KB on first paint (the three below-the-fold screenshots are
`loading="lazy"`).

After replacing a screenshot PNG, regenerate its WebP:

```bash
python3 -c "
from PIL import Image
w = 720  # 800 for hero-phone.png
im = Image.open('images/screen-harvest.png').convert('RGB')
im.resize((w, round(im.height * w / im.width)), Image.LANCZOS).save(
    'images/screen-harvest.webp', 'WEBP', quality=82, method=6)"
```

Then update that `<img>`'s `width`/`height` attributes to the new WebP's pixel
dimensions -- they're what reserves layout space before the image loads.

### Local setup

Run this once per clone to enable the repo's git hooks:

```bash
git config core.hooksPath .githooks
```

The `pre-commit` hook stamps each page's `<link rel="stylesheet">` with a hash
of `style.css` (`style.css?v=<hash>`) whenever `style.css` is part of a commit.
GitHub Pages serves CSS with `cache-control: max-age=600`, so without the stamp
a browser can load updated HTML alongside a cached stylesheet and render the
page with stale styling. Don't edit the `?v=` value by hand.

If the hook reports that a page has unstaged changes, stage or stash them and
commit again -- it refuses to re-add a file whose working copy has edits you
didn't stage.

The in-app legal content in `src/screens/LegalDocumentScreen.tsx` should be kept in sync with the website. The app shows a summary and links to the full hosted document via the "View Full ..." button.
