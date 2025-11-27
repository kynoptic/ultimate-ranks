# Ultimate Ranks

A multi-repository project for aggregating, visualizing, and tracking video game rankings from 100+ publications.

## Project Structure

This is an **umbrella repository** that coordinates three independent components:

```
ultimate-ranks/
├── data/    → kynoptic/ultimate-ranks-data   (Python data pipeline)
├── web/     → kynoptic/ultimate-ranks-web    (Astro + Svelte web frontend)
├── app/     → kynoptic/ultimate-ranks-app    (Swift iOS app)
└── CLAUDE.md  (Cross-component guidance)
```

## Component Repositories

| Component | Repository | Description | Status |
|-----------|------------|-------------|--------|
| **Data Pipeline** | [ultimate-ranks-data](https://github.com/kynoptic/ultimate-ranks-data) | Pure Python pipeline aggregating 100+ publications using Schulze voting | v6.2.0 (Beta) |
| **Web Frontend** | [ultimate-ranks-web](https://github.com/kynoptic/ultimate-ranks-web) | SEO-optimized static website with long-tail query targeting | Phase 2 |
| **iOS App** | [ultimate-ranks-app](https://github.com/kynoptic/ultimate-ranks-app) | Personal backlog tracking against consensus rankings | Pre-release |

## User Journey

```
Discovery → Browse → Convert → Engage
   (Web)     (Web)    (Web→App)  (App)
```

1. **Discovery**: User finds site via search (e.g., "best RPGs of all time")
2. **Browse**: User explores rankings on SEO-optimized website
3. **Convert**: Website directs user to download iOS app
4. **Engage**: User tracks backlog, marks completed games, measures progress

## Quick Start

### Clone all repositories

```bash
# Clone this umbrella repo
git clone https://github.com/kynoptic/ultimate-ranks.git
cd ultimate-ranks

# Clone the three component repos
git clone https://github.com/kynoptic/ultimate-ranks-data.git data
git clone https://github.com/kynoptic/ultimate-ranks-web.git web
git clone https://github.com/kynoptic/ultimate-ranks-app.git app
```

### Setup each component

**Data Pipeline:**

```bash
cd data
make init         # Setup Python environment
ur health         # Verify installation
```

**Web Frontend:**

```bash
cd web
make init         # Install dependencies (pnpm)
make dev          # Start dev server on :4321
```

**iOS App:**

```bash
cd app
open ultimate-ranks.xcodeproj  # Open in Xcode
# Build and run (Cmd+R)
```

## Current Milestone

**Ultimate Ranks 0.1** (Due: December 1, 2025)

**Goal**: First coordinated release where all three repos work together as a unified product.

**Status**: Nearly complete - 2/4 issues remaining (data QID bugs)

- ✅ **Web**: All milestone goals met (search, app funnel)
- ✅ **App**: All milestone goals met (backlog tracking)
- ⏳ **Data**: 2 QID mapping bugs remaining (#529, #530)

## Project Management

- **Organization Project**: [Ultimate Ranks](https://github.com/orgs/kynoptic/projects/1)
- **Milestones**: Coordinated across all three repos
- **Issues**: Tracked in respective component repositories

## Documentation

- **[CLAUDE.md](./CLAUDE.md)** - Cross-component guidance for AI agents
- **Component docs**:
  - [Data pipeline docs](https://github.com/kynoptic/ultimate-ranks-data/tree/main/docs)
  - [Web frontend docs](https://github.com/kynoptic/ultimate-ranks-web/tree/main/docs)
  - [iOS app docs](https://github.com/kynoptic/ultimate-ranks-app/tree/main/docs)

## Architecture

### Data Flow

```
┌─────────────────────────────────┐
│  Data Pipeline                  │
│  - Aggregates publications      │
│  - Runs Schulze elections       │
│  - Outputs: JSON files          │
└────────┬────────────────┬────────┘
         │                │
         ▼                ▼
    ┌────────┐      ┌─────────┐
    │  Web   │      │   App   │
    │  SEO   │─────→│ Tracking│
    └────────┘      └─────────┘
```

### Technology Stack

- **Data**: Python 3.11+, Schulze voting, SQLite cache, Wikidata
- **Web**: Astro, Svelte, Tailwind CSS v4, Static generation
- **App**: Swift 6.0, SwiftUI, SwiftData, iOS 17+

## Contributing

Each component has its own contribution guidelines:

- [Data pipeline CONTRIBUTING.md](https://github.com/kynoptic/ultimate-ranks-data/blob/main/CONTRIBUTING.md)
- [Web frontend CONTRIBUTING.md](https://github.com/kynoptic/ultimate-ranks-web/blob/main/CONTRIBUTING.md)
- App contribution guidelines in development

## License

MIT License - See LICENSE files in respective component repositories.
