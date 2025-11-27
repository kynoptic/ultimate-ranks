# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Ultimate Ranks is a monorepo containing three interconnected components with a clear user journey:

**`data/`** → **`web/`** → **`app/`**

- **`data/`** - Pure Python data pipeline that aggregates 100+ publications using Condorcet voting to generate canonical rankings (feeds both web and app)
- **`web/`** - SEO-optimized static website that presents rankings as read-only content, targets long-tail queries (e.g., "best RPGs of the 2010s for PlayStation"), and funnels users to the mobile app
- **`app/`** - iOS SwiftUI mobile app where users track their personal backlog progress through the ranked list (core value proposition)

### User journey

1. **Discovery**: User finds site via search (e.g., "best RPGs of all time")
2. **Browse**: User explores rankings on SEO-optimized website
3. **Convert**: Website directs user to download iOS app
4. **Engage**: User tracks backlog, marks completed games, measures progress against critical consensus

Each component has its own dedicated CLAUDE.md file with specific guidance. **Always read the component-specific CLAUDE.md when working within that directory.**

## Current status (November 2025)

**Milestone: Ultimate Ranks 0.1** (Due: December 1, 2025)

**Goal**: First coordinated release where all three repos work together as a unified product. Users can discover top-ranked games on web, download the app, and track their backlog.

**Status**: Nearly complete - 2/4 open issues remaining (data QID bugs)

### Per-component status:

- **Data pipeline** (v6.2.0, Beta): 2 open issues (#529, #530 - QID mapping bugs)
  - ✅ Security CVE resolved
  - ✅ Core bug fixes complete
  - ⏳ Remaining: Deltarune Chapter Two mapping, Pokémon Legends truncation
  - Recent: Wilson Score confidence weighting, IOD normalization, Publisher consensus thresholds

- **Web frontend** (Phase 2): ✅ All milestone goals met (0 open issues)
  - ✅ Working search implemented
  - ✅ App Store funnel with prominent CTAs
  - Phase 1 complete: Static text-only rankings (2,211 games)
  - Next (post-0.1): Long-tail SEO pages, image integration

- **iOS app** (Pre-release): ✅ All milestone goals met (0 open issues)
  - ✅ Backlog tracking feature complete (#7 via PR #9)
  - ✅ Status filtering, SwiftData persistence, responsive design
  - Next (post-0.1): Image display (#8), progress stats

## Repository structure

**Important**: This is a **multi-repo setup**, not a traditional monorepo. Each component is a separate git repository:

```
ultimate-ranks/ → kynoptic/ultimate-ranks (umbrella repo)
├── .git/           # Tracks cross-component files only
├── data/           # git@github.com:kynoptic/ultimate-ranks-data.git
│   ├── .git/       # Separate repository
│   └── CLAUDE.md   # Python/data pipeline specific guidance
├── web/            # git@github.com:kynoptic/ultimate-ranks-web.git
│   ├── .git/       # Separate repository
│   └── CLAUDE.md   # Web development specific guidance
├── app/            # git@github.com:kynoptic/ultimate-ranks-app.git
│   ├── .git/       # Separate repository
│   └── CLAUDE.md   # iOS development specific guidance
├── CLAUDE.md       # This file (tracked in umbrella repo)
└── README.md       # Project overview (tracked in umbrella repo)
```

**Four repositories total:**
1. [kynoptic/ultimate-ranks](https://github.com/kynoptic/ultimate-ranks) - Umbrella repo (cross-component docs)
2. [kynoptic/ultimate-ranks-data](https://github.com/kynoptic/ultimate-ranks-data) - Data pipeline
3. [kynoptic/ultimate-ranks-web](https://github.com/kynoptic/ultimate-ranks-web) - Web frontend
4. [kynoptic/ultimate-ranks-app](https://github.com/kynoptic/ultimate-ranks-app) - iOS app

### Cross-repo coordination

**GitHub Organization Project**: [Ultimate Ranks](https://github.com/orgs/kynoptic/projects/1)
- Single project board tracking issues across all three repos
- Coordinated milestones (e.g., "Ultimate Ranks 0.1")
- Cross-component feature tracking

**Working with multiple repos:**
```bash
# Each repo has its own git workflow
cd data && git status  # Check data repo
cd ../web && git status  # Check web repo
cd ../app && git status  # Check app repo

# Use gh CLI with -R flag for cross-repo operations
gh issue list -R kynoptic/ultimate-ranks-app
gh issue list -R kynoptic/ultimate-ranks-web
gh issue list -R kynoptic/ultimate-ranks-data

# Or use organization project for unified view
gh project item-list 1 --owner kynoptic
```

## Component quick reference

| Component | Technology | Purpose | Role in user journey | Entry point |
|-----------|-----------|---------|---------------------|-------------|
| **Data** | Python 3.11+, Schulze voting | Aggregate rankings from 100+ publications | Data source (feeds web & app) | `data/CLAUDE.md` |
| **Web** | Astro, Svelte, Tailwind CSS v4 | SEO-optimized static site with long-tail query targeting | Discovery & conversion funnel | `web/CLAUDE.md` |
| **App** | Swift 6.0, SwiftUI, SwiftData | Personal backlog tracking against consensus rankings | Engagement & retention | `app/CLAUDE.md` |

## Working across components

### Data flow architecture

```
┌─────────────────────────────────────────────────┐
│  Data Pipeline (data/)                          │
│  - Aggregates 100+ publications                 │
│  - Runs Schulze elections                       │
│  - Generates canonical rankings                 │
│  - Outputs: web.json + game-rankings.json       │
└────────────┬────────────────────┬────────────────┘
             │                    │
             ▼                    ▼
    ┌────────────────┐   ┌────────────────┐
    │  Web (web/)    │   │  App (app/)    │
    │  - Static gen  │   │  - SwiftData   │
    │  - SEO pages   │   │  - Tracking    │
    │  - Funnels to→ ├───→  - Progress    │
    └────────────────┘   └────────────────┘

Discovery/SEO          Engagement/Retention
```

**Data pipeline** (`data/`) → Generates canonical rankings from publication data
**Web frontend** (`web/`) → Consumes `web.json` at build time for SEO-optimized static pages
**iOS app** (`app/`) → Seeds SwiftData from bundled `game-rankings.json` (curated subset)

### Web-to-app funnel strategy

The website's primary role is **acquisition**, not retention:

- **Long-tail SEO**: Target queries like "best PS5 RPGs", "greatest 90s platformers", "top indie games 2020s"
- **Content structure**: Dynamic pages for platform/genre/era combinations
- **Call-to-action**: Prominent app download links, emphasizing backlog tracking features
- **Read-only experience**: No user accounts or interactivity on web (drives app downloads)

The app's role is **retention and engagement** through personal progress tracking.

### Common tasks

**Updating game data across all platforms:**

1. Update source data in `data/article_data/`
2. Run pipeline: `cd data && ur run --validated-only`
3. Export web JSON: Output flows to `web/src/data/`
4. Export app JSON: Copy subset to `app/ultimate-ranks/Resources/game-rankings.json`
5. Rebuild web: `cd web && make build`
6. Rebuild app: `cd app && xcodebuild ...` (see `app/CLAUDE.md`)

**Cross-component conventions:**

- All three components use **Conventional Commits** format
- **Issue-driven workflow**: Create GitHub issues before implementing features
- **Quality gates**: Tests and lints must pass before commits
- **No secrets in code**: Use `.env` files (gitignored) for API keys
- **Coordinated releases**: Use matching milestone names across repos (e.g., "Ultimate Ranks 0.1")

### Coordinated release workflow

When working toward a cross-component milestone:

1. **Create milestone in each repo** with same name and due date
2. **Tag cross-repo issues** and add to organization project
3. **Independent development** - each repo progresses at its own pace
4. **Status check** before release:
   ```bash
   # Check milestone progress across all repos
   gh issue list -R kynoptic/ultimate-ranks-app --milestone "Ultimate Ranks 0.1"
   gh issue list -R kynoptic/ultimate-ranks-web --milestone "Ultimate Ranks 0.1"
   gh issue list -R kynoptic/ultimate-ranks-data --milestone "Ultimate Ranks 0.1"
   ```
5. **Release each component** when milestone complete (can be different days)
6. **Document cross-repo dependencies** in CLAUDE.md and issue descriptions

## When to read component-specific CLAUDE.md

| Task | Read |
|------|------|
| Election algorithms, QID mapping, article ingestion, data pipeline configuration | `data/CLAUDE.md` |
| SEO optimization, long-tail query pages, static site generation, Astro/Svelte components | `web/CLAUDE.md` |
| Backlog tracking, status management, SwiftUI views, SwiftData persistence | `app/CLAUDE.md` |
| Cross-component data synchronization, schema changes, user journey coordination | This file |

## Development commands by component

### Data pipeline

```bash
cd data
make init         # Setup Python environment
make fast         # Quick validation (30s)
ur run            # Run full pipeline
ur health         # System check
```

### Web frontend

```bash
cd web
make init         # Install dependencies (pnpm)
make dev          # Dev server on :4321
make test         # Unit + component tests
make full         # Complete quality gates
```

### iOS app

```bash
cd app
# Build
xcodebuild -project ultimate-ranks.xcodeproj -scheme ultimate-ranks \
  -destination 'platform=iOS Simulator,name=iPhone 16' build

# Test
xcodebuild -project ultimate-ranks.xcodeproj -scheme ultimate-ranks \
  -destination 'platform=iOS Simulator,name=iPhone 16' test
```

## Critical cross-component rules

1. **Respect the user journey architecture**
   - **Web is for discovery** (SEO, browsing, learning) → Read-only, no user accounts
   - **App is for engagement** (tracking, progress, personal data) → Interactive, persistent state
   - Never add user accounts or tracking features to web (breaks funnel strategy)
   - Web features should drive app downloads, not replace app functionality

2. **Never modify data formats without updating all consumers**
   - Changes to `data/` output schemas require updates to `web/` and `app/`
   - Always check data consumption points before schema changes

3. **Maintain QID consistency**
   - Wikidata QIDs are the canonical game identifiers across all platforms
   - QID mappings live in `data/config/data/qid/`
   - Changes to QID mapping must be validated: `cd data && make check-qid-parity`

4. **Version compatibility**
   - Each repo has **independent semantic versioning**
   - Data schema version: Tracked in `data/` output (currently v6.2.0)
   - Web expects specific JSON structure from data pipeline
   - App JSON is a curated subset (top 500 games for seed data)
   - **Cross-repo releases**: Milestone names (e.g., "0.1") represent coordinated feature sets, not version numbers

5. **Documentation-as-code**
   - Significant changes require ADRs in respective component `docs/adr/` directories
   - User stories go in component-specific `docs/stories/` or `docs/project-metadata/user-stories/`
   - Cross-component decisions may require ADRs in multiple components

## Getting help

- **Component-specific**: See `CLAUDE.md` in `data/`, `web/`, or `app/` directories
- **Data pipeline**: See `data/docs/` for comprehensive documentation
- **Web frontend**: See `web/docs/` for guides and stories
- **iOS app**: See `app/docs/` for architecture decisions and guides

## Component independence and active work

Each component can be developed independently:

- **Data pipeline** can run standalone for ranking generation
- **Web frontend** can use static mock data during development
- **iOS app** works with bundled seed data without live backend

Only when integrating across components do you need to ensure data format compatibility.

### Active cross-component initiatives

**Image infrastructure (Milestone: Ultimate Ranks 0.1)**

A major cross-component effort is underway to centralize game cover art handling:

- **Data repo** (Issues #543-#546): Building complete image pipeline
  - Fetch from SteamGridDB/IGDB APIs
  - Optimize images (resize, WebP conversion)
  - Upload to Cloudflare R2 (self-hosted CDN)
  - Output R2 URLs in `web.json` and `game-rankings.json`

- **Web repo** (Issue #35): Will consume R2 URLs
  - Deprecate local image fetch pipeline
  - Use `image_url` field from data output

- **App repo** (Issue #8): Will display R2-hosted images
  - Add `imageURL` field to `Game` model
  - Implement `AsyncImage` in `GameRowView`

**Status tracking** (App Issue #7 - ✅ Closed, Milestone: Ultimate Ranks 0.1):
- **Core value proposition**: What makes app worth downloading vs browsing website
- ✅ **Completed** (PR #9): Status enum, filter chips, SwiftData persistence, responsive design
- All acceptance criteria met, issue closed Nov 27, 2025

**App download funnel** (Web Issue #33):
- Add prominent CTAs on website to drive app downloads
- Emphasize backlog tracking as key differentiator
- Ensure seamless handoff from web browsing to app engagement

**When working on image-related features**, check these cross-repo issues for coordination.

### Critical cross-component issues to be aware of

**Data quality and QID mapping** (Data repo):
- Issue #530: Pokémon Legends vote name truncated
- Issue #529: Deltarune Chapter Two mapping not resolved
- Issue #480: Remake votes create temporal vote pollution
- Issue #472: Year-scoped ballots excluded from all-time elections

**Testing and quality gates** (All repos):
- Data #230: Test coverage below quality gate (current: ~60%, target: 66.6%)
- Web #15: Integration tests needed for Astro components
- All components enforce: No vanity tests, behavioral assertions required

**Pipeline concerns** (Data repo):
- Issue #481: Wilson confidence interval affects cult classics ranking
- Issue #479: Schulze ties broken by Borda (consensus vs polarizing games)
- Issue #360: Mid-year lists receive same weight as year-end lists

**When encountering QID resolution issues**, check `data/config/data/qid/` for mapping tiers and editorial decisions.

## Tool preferences (cross-component)

- **Search**: Use `rg` (ripgrep) instead of `grep`
- **GitHub**: Use `gh` CLI for issues, PRs, and project management
- **Secrets**: Use `op` (1Password CLI) for credentials
- **Package managers**:
  - Python: `pip` + `pip-tools` (data)
  - JavaScript: `pnpm` (web)
  - Swift: No external dependencies (app)

## Quality standards

All components enforce:

- **Test quality over coverage**: No vanity tests, behavioral assertions required
- **Type safety**: Python type hints, TypeScript strict mode, Swift strict concurrency
- **Linting**: Pre-commit hooks, CI validation
- **Conventional commits**: `<type>(<scope>): <description>`
- **Semantic versioning**: Default to PATCH, justify MINOR/MAJOR with evidence

## Remember

When working in this repository:

1. **Read the component-specific CLAUDE.md first** when entering a new directory
2. **Run quality gates** before committing (`make fast`, `make test`, or `make full`)
3. **Check data dependencies** when changing schemas or output formats
4. **Follow component conventions** for commits, tests, and documentation
5. **Maintain QID consistency** across all platforms when updating game mappings
