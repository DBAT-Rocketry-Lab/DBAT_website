this file should be modified after every iteration

# CLAUDE.md — DBAT Rocketry Lab Website

Guidance for Claude Code (and any contributor) working in this repository.
Repo: https://github.com/DBAT-Rocketry-Lab/DBAT_website
Projects-section design reference: https://dasux.github.io/portfolio/#projects

---

## 1. What this project is

We are rebuilding the DBAT Rocketry Lab website. It is **not** just a landing-page redesign. It is the lab's central digital archive: it documents our progress, justifies our design decisions, shows our rocket history in 3D, and holds long-form technical writing. It must grow with the lab: new rockets, projects, articles, specs and images should be addable without redesigning the site.

Three content types, which complement (not duplicate) each other:

| Type | Purpose |
|------|---------|
| **Projects** | Detailed records of individual engineering efforts |
| **Tree** | Chronological/relational visualization of the lab's rockets (historical, current, planned) |
| **Blog** | Long-form articles: experiments, technical decisions, tests, lessons learned |

Cross-link them (a Tree entry links to its project page; a blog post can document a test campaign).

---

## 2. Working agreement (read first)

This is an ongoing engineering and design collaboration, not template generation. The owner wants to understand and shape the architecture.

- **Audit before changing.** Inspect the repo first. Do not assume it is already Astro; verify.
- **Incremental, reviewable changes.** No giant rewrites, no unrelated refactors. Explain significant structural changes *before* making them.
- **Explain trade-offs** on decisions that matter, recommend one option, distinguish necessary complexity from optional extras.
- **Justify every new dependency** and say whether an existing one could do the job.
- **Ask targeted questions** only when the answer affects implementation. No giant questionnaires; resolve the most consequential decisions first.
- **Never invent content.** No fabricated specs, dimensions, mass, performance data, flight results, test outcomes or milestones. Documentation must distinguish completed / in-progress / planned / unverified. If mock data is needed for a prototype, label it clearly as MOCK and keep it trivially replaceable.
- **Do not silently convert open questions (section 4) into fixed requirements.** Present recommendations and wait.
- **Do not claim something works unless tested.** After meaningful changes verify: build succeeds, nav links work, shared layout is consistent, routes resolve, mobile + desktop sizes look right, no useful content/assets were lost.
- **Do not build the full Tree** until its design has been discussed and approved.
- The owner is comfortable with Python and a beginner in C++; for web/TS code, prefer clear, commented, conventional solutions over clever ones.

---

## 3. Confirmed requirements

### Stack and hosting
- Framework: **Astro** (content-first). TypeScript where appropriate.
- Content: Markdown/MDX via **Astro Content Collections**, stored in the repo.
- Styling: maintainable CSS architecture based on the existing visual identity and branding.
- 3D: Three.js (or another WebGL solution after evaluation), **isolated to the Tree page**. Other pages must not ship large JS bundles.
- Hosting: free tier preferred; **Cloudflare** (Pages vs Workers) is the leading candidate, final choice pending evaluation. Prefer **static output**.
- No backend, database, auth or CMS unless a concrete requirement justifies it. No SSR adapter just because the Tree uses WebGL; client-side 3D does not require SSR.
- Keep using the existing GitHub repo unless there is a compelling reason not to.

### Navigation (shared across every page)
- Existing **logo on the left, preserved**; clicking it goes to `/`.
- Links: **Home** (`/`), **Tree** (`/tree/`), **Blog** (`/blog/`).
- One shared layout/navbar component; no duplicated nav markup or styles. Must work on desktop and mobile, and stay identical on home, tree, blog list, article pages and future pages.

### Homepage
1. **Hero**: preserve the existing hero (title, logo, imagery, identity). Improvements to layout, responsiveness, typography or animation are welcome if they respect the existing design. Do not swap in a generic template.
2. **Projects**: must appear **immediately after the hero**, with no mission statement or other section between them. Modeled structurally on the portfolio reference above, adapted to a rocketry lab. Clickable cards that may include: title, image/render/photo, short description, status, technical category, link to documentation. The homepage is a concise overview; detail lives on project pages.

Hero followed immediately by Projects is a **fixed requirement**. Other homepage sections can be proposed later.

### Tree (`/tree/`), the flagship interactive feature
- Interactive visualization of rocket-development history: **historical**, **current**, and **planned/future** rockets, showing relationships (predecessor/successor) and chronology; future rockets appear further along the progression.
- Must support **real 3D models** (genuine GLB/glTF assets, not images or pre-renders) that users can inspect in the browser.
- Flow: open Tree → see the tree → select a rocket → explore its 3D model → view specs.
- Interactions: orbit, zoom, pan (where useful), select, switch rockets / return to tree, view tech info. Intuitive, responsive, works on desktop and mobile.
- Spec panel fields (all optional; **never display invented values**; support missing/undocumented data): name & designation, generation/chronological position, status, dimensions & mass, propulsion, avionics/control, flight/test history, description & objectives, images/renders/docs, links to related projects/articles.
- Data-driven: rocket content separate from visual representation. No hardcoded rocket info scattered through components.
- Performance/accessibility: lazy-load models, optimize geometry/textures, don't render many detailed models at once, loading + error states, respect `prefers-reduced-motion`, graceful no-WebGL fallback, specs readable without relying on the 3D view.
- GLB/glTF is the initial candidate; validate against real models before committing.

### Blog (`/blog/`, `/blog/[slug]/`)
- Listing: **long horizontal cards** (image one side, text the other) with image, title, excerpt, date, optional category, clear "open" affordance. Not a grid of small square cards.
- Article page: full article on its own route (never expanded inside the card). Title, summary, author (where appropriate), date, cover image, headings, paragraphs, lists, images with captions, code blocks, tables, links to projects/repos/articles. Comfortable long-form typography and line length. Shared navbar.
- Authoring: Markdown/MDX in repo. Frontmatter (adapt to the Astro version): `title`, `description`, `pubDate`, `author?`, `heroImage?`, `category`/`tags?`, `draft?`.
- Listing and article pages generated from the same collection; adding a valid article makes it appear automatically. **Drafts must not appear in the public build.**

### Projects (`/projects/[slug]/`, optional, needs approval)
Template may include: overview/objectives, status, specs, design decisions/architecture, hardware & software, CAD/photos/diagrams, test methodology & results, known limitations, milestones, links to repos/articles. Not every project needs every section.
Example subject areas (not a finalized list): rocket development, thrust-vector control, avionics, flight computers, ground-control systems, simulations, control-system development.

### Design principles
Cohesive product, reusable components, aerospace-credible look. Consistent typography/spacing, clear hierarchy, good contrast, responsive, fast loads, accessible nav, subtle purposeful animation only.
Shared components to plan for: Navbar, Footer, page/section layouts, ProjectCard, BlogCard, article metadata, image+caption, spec panel, buttons/links, 3D viewer loading/error states.

---

## 4. Open decisions (do NOT decide silently)

- Exact Tree design, animation, orientation and branching rules (a conventional vertical tree may not be right)
- Rocket selection UX: inline expansion vs larger viewer vs separate detail view
- Exact spec fields per rocket
- Final project-card design
- Homepage sections beyond Hero + Projects
- Whether every project gets a dedicated page
- Final styling of redesigned pages
- Final hosting platform (Cloudflare Pages vs Workers vs other)
- Whether a CMS is eventually needed
- Model assets and optimization pipeline

---

## 5. Suggested architecture (proposal, to be validated by the audit)

```
/
├─ astro.config.mjs
├─ package.json / tsconfig.json
├─ public/                      # static assets: favicon, logo, models/*.glb (or src/assets for optimized images)
├─ src/
│  ├─ content.config.ts         # collections + zod schemas
│  ├─ content/
│  │  ├─ blog/*.md(x)
│  │  ├─ projects/*.md(x)
│  │  └─ rockets/*.json|md      # structured rocket data driving the Tree
│  ├─ layouts/
│  │  ├─ BaseLayout.astro       # <head>, Navbar, Footer, slot
│  │  └─ ArticleLayout.astro    # long-form reading layout
│  ├─ components/
│  │  ├─ Navbar.astro, Footer.astro
│  │  ├─ Hero.astro, ProjectCard.astro, ProjectsSection.astro
│  │  ├─ BlogCard.astro
│  │  └─ tree/                  # client-only: TreeApp, RocketViewer, SpecPanel, loaders
│  ├─ pages/
│  │  ├─ index.astro            # Hero + Projects
│  │  ├─ tree/index.astro
│  │  ├─ blog/index.astro
│  │  ├─ blog/[...slug].astro
│  │  └─ projects/[...slug].astro   # if approved
│  └─ styles/                   # tokens (colors/type/spacing), global, per-component
└─ CLAUDE.md
```

Rocket schema (draft; do not over-engineer until Tree interactions are defined): `id/slug`, `name`, `description`, `date|year|sequence`, `status` (historical | current | planned), `predecessors[]` / `successors[]`, `specs{}` (all optional), `model` (GLB path), `images[]`, `docs[]`, `links{projects[], articles[]}`, optional `treeLayout` hints.

Blog/project schemas: see frontmatter above; keep `draft` filtering in one shared helper used by both listing and article routes (including `getStaticPaths`).

---

## 6. Phased implementation plan

### Phase 1: Audit and planning (no code changes)
1. Clone/inspect the repo. Report: framework, build setup, routes, important files, deployment config.
2. Locate and document the existing navbar, logo, hero (HTML, assets, CSS), CSS conventions, reusable bits, existing project info.
3. State what to preserve (logo + hero at minimum) and what can be refactored.
4. Recommend Astro setup/migration strategy (in-place on a branch vs new scaffold copying assets).
5. Propose route structure, shared layout, content model, hosting approach.
6. List unresolved decisions, most consequential first.
7. Propose the smallest sensible first milestone.
**Gate: owner reviews and approves before Phase 2.**

### Phase 2: Core foundation
1. Establish/migrate to Astro on a feature branch; confirm build.
2. Build `BaseLayout`, `Navbar` (logo→`/`, Home, Tree, Blog, mobile menu, active state), `Footer`.
3. Port/refine the existing hero, preserving assets and identity.
4. Build the Projects section immediately below the hero (ProjectCard + ProjectsSection), using clearly labeled placeholder entries only if real content isn't supplied.
5. Define CSS tokens and responsive rules.
6. Stub `/tree/` and `/blog/` pages so nav resolves.
7. Verify: build, nav, layout consistency, mobile + desktop.
**First milestone (smallest useful):** shared navbar + existing hero on Astro, with stub Tree and Blog pages.

### Phase 3: Documentation and blog
1. Define `blog` (and `projects`) collections + schemas; shared draft filter.
2. Implement horizontal `BlogCard` and `/blog/` listing (sorted by date).
3. Implement `/blog/[slug]/` with `ArticleLayout`: typography, captions, code, tables, cover image.
4. If approved, implement `/projects/[slug]/` with a flexible template (sections optional).
5. Link homepage project cards to project pages.
6. Write contributor docs: how to add an article/project (frontmatter, images, drafts).

### Phase 4: Tree planning and prototype
1. **Design discussion first**: orientation, branching, selection UX, camera behavior.
2. Define the rocket data schema and relationships from the agreed design.
3. Validate the GLB pipeline with one real rocket asset (size, textures, compression, e.g. Draco/meshopt, only if justified).
4. Prototype: selection, OrbitControls, model loading, spec panel, loading/error states, reduced-motion handling, no-WebGL fallback with text specs.
5. Evaluate performance and mobile behavior; keep Three.js code-split to `/tree/`.
6. Integrate the viewer into the agreed Tree experience.

### Phase 5: Deployment and refinement
1. Check **current** free-plan terms/limits and the official docs (Astro deploy guide; Cloudflare Pages and Workers Astro guides) before recommending; prefer static output.
2. Connect GitHub for push-to-deploy with preview deployments; consider custom domain.
3. Verify production build; test desktop + mobile.
4. Accessibility, performance and navigation checks.
5. Final documentation for adding projects, rockets and articles.

---

## 7. Definition of done (for any change)
- `npm run build` passes (and type-check, if configured).
- Every nav link and route resolves; logo → `/`.
- Shared layout is identical across all pages.
- Verified at mobile and desktop widths.
- No existing assets/content lost unintentionally.
- No fabricated technical content; mock data labeled.
- Dependencies added are justified in the PR/commit description.

## 8. Immediate first task
Run **Phase 1** only: audit the repo, report findings (framework/architecture/key files; what to preserve; recommended Astro structure; content architecture for blog, projects and rockets; hosting recommendation; decisions to resolve), then propose the smallest first milestone. Do not rewrite the site. Wait for approval.

## Iteration Tracking
- Phase 1: Audit and planning (no code changes) - ✅ Completed 2026-10-13 10:00
- Phase 2: Core foundation - ✅ Completed 2026-10-13 13:45
- Phase 3: Documentation and blog - ⬜ Not started
- Phase 4: Tree planning and prototype - ⬜ Not started
- Phase 5: Deployment and refinement - ⬜ Not started