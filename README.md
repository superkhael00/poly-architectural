# POLY Architectural & Construction Services — Website Sample

A one-page marketing site for a fictional Philippine design-and-build firm. Built as **Portfolio Sample 1** by Khael Mendoza.

**Features**
- Full-screen hero
- Scroll-driven build progress: 5 photos (lot → foundation → frame → finishing → finished) swap as you scroll, with a stage counter and progress bar
- Services grid, 4-step "what happens next" process, project gallery with lightbox
- "Request a Quote" form with validation (PH mobile format, required fields, consent, honeypot spam trap) that POSTs JSON to an n8n webhook
- Mobile-friendly, no frameworks, no build step — plain HTML/CSS/JS

## Folder structure

```
poly-architectural/
├── index.html          ← the whole site (HTML + CSS + JS)
├── README.md
└── assets/img/
    ├── stage-1-lot.jpg … stage-5-finished.jpg   ← scroll sequence
    └── project-villa.jpg, project-interior.jpg, project-commercial.jpg
```

## 1. Preview locally
Double-click `index.html`. It opens in your browser. The form runs in **demo mode** until you add a real webhook URL — it shows the success message and prints the payload in the browser console (F12 → Console).

## 2. Publish on GitHub Pages
1. On github.com, click **+** (top right) → **New repository**.
2. Repository name: `poly-architectural` · set to **Public** · click **Create repository**.
3. On the empty repo page, click **uploading an existing file**.
4. Drag in `index.html`, `README.md` and the whole `assets` folder (drag the folder itself so the `assets/img/` path is kept).
5. Click **Commit changes**.
6. Go to **Settings** → **Pages** (left sidebar).
7. Under **Build and deployment** → **Source**: `Deploy from a branch`. Branch: `main`, folder `/ (root)` → **Save**.
8. Wait 1–2 minutes and refresh. You'll see: *"Your site is live at https://YOUR-USERNAME.github.io/poly-architectural/"*.

## 3. Connect the form to n8n
1. In n8n, create a new workflow → add a **Webhook** node.
2. **HTTP Method**: `POST` · **Path**: `poly-quote-request`.
3. **Respond**: `Immediately` (fine for now).
4. Click **Add Option** → **Allowed Origins (CORS)** → enter `https://YOUR-USERNAME.github.io`
   *Why: the browser blocks a page on one domain from posting to another domain unless that domain says it's allowed. Without this, the form shows the error message even though n8n is running.*
5. Save and **Activate** the workflow, then copy the **Production URL**.
6. In `index.html`, find `const WEBHOOK_URL = ...` near the top of the `<script>` and paste your URL.
7. Commit the change on GitHub (open the file → pencil icon → paste → **Commit changes**).

### Payload the form sends
```json
{
  "fullName": "Juan Dela Cruz",
  "phone": "09171234567",
  "email": "juan@example.com",
  "projectType": "New house construction",
  "location": "Malolos, Bulacan",
  "lotAreaSqm": 150,
  "budget": "₱2M – ₱5M",
  "timeline": "1–3 months",
  "message": "2-storey, 3 bedrooms",
  "consent": true,
  "source": "poly-website",
  "pageUrl": "https://…/poly-architectural/",
  "submittedAt": "2026-10-10T10:45:00.000Z"
}
```
In n8n, reference fields as `{{ $json.body.fullName }}`, `{{ $json.body.budget }}`, etc.

## Test checklist
- [ ] Scroll through "How We Build" — counter goes 01 → 05 and the photo changes each stage
- [ ] Submit empty form → 5 fields turn red
- [ ] Phone `12345` → error; `0917 123 4567` → accepted
- [ ] Valid submit → green "Salamat…" message, new execution appears in n8n
- [ ] Open on your phone → menu button works, no sideways scrolling

*POLY is a fictional brand. Contact details are placeholders.*
