# Sanity Studio Setup - Next Steps

## ✅ Completed
- [x] Sanity Studio installed and configured
- [x] All 6 content schemas created (about, project, skill, experience, contact, settings)
- [x] Schema types registered in config
- [x] Project ID configured (8lwtqgtp)

---

## 🎯 What to Do Next

### Step 1: Start the Studio Locally
```bash
npm run dev
```
- Opens at `http://localhost:3333`
- You'll see all 6 content types in the sidebar
- Start adding your portfolio content

### Step 2: Add Your Content
Add content in this order:

1. **Site Settings** (singleton)
   - Site Title: "Kris Rodriguez"
   - Site Tagline: Your tagline

2. **About Me** (singleton)
   - Add your bio/about content

3. **Skills**
   - Add each skill with name, category, level
   - Examples: "React" (Frontend, Expert), "Figma" (Design, Advanced)

4. **Experiences**
   - Add work history in reverse chronological order
   - Include role, company, dates, description

5. **Contacts**
   - Add social links (GitHub, LinkedIn, Twitter, etc.)
   - Platform name, display label (@username), URL

6. **Projects**
   - Add portfolio projects
   - Include title, slug, summary, images, tags, URLs
   - Upload featured images for each project

### Step 3: Deploy Studio (Optional but Recommended)
```bash
npm run deploy
```
- Choose a studio hostname (e.g., `kris-portfolio`)
- Access anywhere at `https://your-hostname.sanity.studio`
- Edit content from any device

### Step 4: Get Your Astro Frontend Repository
You need to locate or clone your Astro portfolio site that currently uses Keystatic.

**If you have it locally:**
- Navigate to that project folder
- Continue to Step 5

**If you need to clone it:**
```bash
cd ..
git clone <your-astro-repo-url>
cd <your-astro-project>
```

### Step 5: Install Sanity in Your Astro Project
In your Astro project directory:
```bash
npm install @sanity/astro @sanity/client @sanity/image-url
```

### Step 6: Create Sanity Client in Astro
Create `src/lib/sanity.ts`:
```typescript
import { createClient } from '@sanity/client'
import imageUrlBuilder from '@sanity/image-url'

export const sanityClient = createClient({
  projectId: '8lwtqgtp',
  dataset: 'production',
  useCdn: true,
  apiVersion: '2024-01-01',
})

const builder = imageUrlBuilder(sanityClient)

export function urlFor(source: any) {
  return builder.image(source)
}
```

### Step 7: Update Astro Config
Add Sanity integration to `astro.config.mjs`:
```javascript
import sanity from '@sanity/astro'

export default defineConfig({
  // ... existing config
  integrations: [
    // ... existing integrations
    sanity({
      projectId: '8lwtqgtp',
      dataset: 'production',
      useCdn: true,
      apiVersion: '2024-01-01',
    }),
  ],
})
```

### Step 8: Update Data Fetching in Pages
Replace Keystatic imports with Sanity queries.

**Before (Keystatic):**
```typescript
import { getCollection } from 'astro:content'
const projects = await getCollection('projects')
```

**After (Sanity):**
```typescript
import { sanityClient, urlFor } from '../lib/sanity'

const projects = await sanityClient.fetch(`
  *[_type == "project"] | order(published_at desc) {
    _id,
    title,
    "slug": slug.current,
    summary,
    client,
    year,
    published_at,
    featured_image,
    content,
    tag,
    url
  }
`)
```

### Step 9: Update Image References
Replace image paths with Sanity image URLs:

**Before:**
```astro
<img src={project.image} alt={project.title} />
```

**After:**
```astro
<img src={urlFor(project.featured_image).width(800).url()} alt={project.title} />
```

### Step 10: Add Environment Variables
Create `.env` in your Astro project:
```
PUBLIC_SANITY_PROJECT_ID=8lwtqgtp
PUBLIC_SANITY_DATASET=production
```

Add to Vercel:
- Go to your Vercel project settings
- Add environment variables:
  - `PUBLIC_SANITY_PROJECT_ID` = `8lwtqgtp`
  - `PUBLIC_SANITY_DATASET` = `production`

### Step 11: Test Locally
```bash
npm run dev
```
- Check all sections load correctly
- Verify animations still work
- Test image loading
- Check all content displays properly

### Step 12: Deploy to Vercel
```bash
git add .
git commit -m "Migrate from Keystatic to Sanity"
git push
```
- Vercel will auto-deploy
- Test production site

### Step 13: Clean Up (Optional)
Once everything works:
- Remove Keystatic dependencies from `package.json`
- Delete `src/content/` folder (keep a backup first!)
- Remove Keystatic config from Astro config

---

## 📋 Quick Reference

**Sanity Studio Commands:**
- `npm run dev` - Start studio locally
- `npm run build` - Build studio
- `npm run deploy` - Deploy studio to Sanity hosting

**Sanity Project Info:**
- Project ID: `8lwtqgtp`
- Dataset: `production`
- Studio URL (after deploy): `https://your-hostname.sanity.studio`

**Common GROQ Queries:**
```groq
// All projects
*[_type == "project"] | order(published_at desc)

// Featured projects (first 4)
*[_type == "project"] | order(published_at desc) [0...4]

// All skills
*[_type == "skill"] | order(_createdAt asc)

// About singleton
*[_type == "about"][0]

// All experiences
*[_type == "experience"] | order(_createdAt desc)

// All contacts
*[_type == "contact"] | order(_createdAt asc)
```

---

## 🆘 Troubleshooting

**Studio won't start:**
- Check Node.js version (need 18+)
- Run `npm install` again
- Check for port conflicts (default: 3333)

**Can't see content types:**
- Verify schemas are imported in `schemaTypes/index.ts`
- Restart the studio

**Images not loading in Astro:**
- Use `urlFor()` helper function
- Check image field name matches schema
- Verify Sanity client is configured

**Deployment fails:**
- Check you're logged in: `npx sanity login`
- Verify project ID is correct
- Check network connection

---

## 🎉 You're Ready!

Start with Step 1 and work through each step. The studio setup is complete, now you just need to add content and integrate with your Astro frontend.

Need help with any step? Just ask!
