# Manchester United Analytics Dashboard — Build Strategy & Tickets

This document translates `build_checklist.md` into an actionable, phased ticket backlog, written
for someone building this for the first time. Each ticket includes what to do, why, what you'll
learn, and roughly how hard it is — not full step-by-step code, but enough direction that you're
not starting from a blank page.

I'm recommending a specific tech stack below to keep this concrete and learnable. These are
reasonable, widely-used choices for a project like this, not the only correct ones — treat them as
a starting recommendation, and double-check current docs/pricing for anything before committing,
since tooling and free-tier terms shift over time.

---

## Recommended Stack (for learning purposes)

| Layer | Suggestion | Why |
|---|---|---|
| Language | Python | Best ecosystem for data work, huge learning resources |
| Ingestion | Python scripts (`requests`, `BeautifulSoup`) | Simple to learn, no extra framework needed at first |
| Orchestration | Prefect or Dagster (Airflow later if you want the resume keyword) | Gentler learning curve than Airflow |
| Database | PostgreSQL | Industry-standard relational DB, transferable skill |
| Transformation | dbt | Industry-standard for the "clean → model" layer, huge resume value |
| Backend API | FastAPI | Beginner-friendly, modern, well documented |
| Frontend/Dashboard | Streamlit first, migrate to React later if time allows | Fastest way to get something real on screen while learning |
| Deployment | Docker + Render or Railway | Simple free/cheap hosting, real containerization experience |
| CI/CD | GitHub Actions | Free, standard, plenty of tutorials |

You don't need to know any of these yet — each phase below introduces only what that phase needs.

---

## How to use this backlog

- Work phases **in order** — each phase is scoped so you're never learning more than 2–3 new
  concepts at once.
- Each ticket has a **Definition of Done** — don't move on until you can check every box.
- It's fine for v1 of a ticket to be ugly/manual. Refinement tickets come later once the skeleton
  works end to end.

---

## PHASE 0 — Foundations

### TICKET-001: Set up project repository and environment
**Goal:** A working, version-controlled Python project you can build in.
**Tasks:**
- [ ] Create a GitHub repo
- [ ] Set up a Python virtual environment
- [ ] Add a `requirements.txt` / dependency manager
- [ ] Write a basic README describing the project (link back to `specs.md`)
**You'll learn:** git basics, Python environment management
**Difficulty:** Easy

### TICKET-002: Get access to your data sources
**Goal:** Confirm you can actually pull data before designing anything around it.
**Tasks:**
- [ ] Sign up for football-data.org API key, test a basic request
- [ ] Test scraping a single FBref/Understat page manually (check current terms first)
- [ ] Download and inspect StatsBomb open data for one competition
- [ ] Write a short notes file on what fields are actually available from each source
**You'll learn:** working with REST APIs, basic web scraping, reading unfamiliar data schemas
**Difficulty:** Easy–Medium

### TICKET-003: Design the core database schema (v1)
**Goal:** A first-draft schema for teams, matches, players — just enough for Phase 1.
**Tasks:**
- [ ] Install Postgres locally (or use a free hosted instance)
- [ ] Draw the schema on paper/diagram tool first (tables + relationships)
- [ ] Create tables: teams, matches, players, manager_eras
- [ ] Manually insert a few test rows to sanity check the design
**You'll learn:** relational database design, SQL basics
**Difficulty:** Medium

### TICKET-004: Populate manager-era reference table
**Goal:** The cross-cutting era-tagging dimension used everywhere.
**Tasks:**
- [ ] Research and confirm managerial date ranges since 2020 (verify current, don't assume)
- [ ] Insert into `manager_eras` table
- [ ] Write a small function that, given a match date, returns the correct era
**You'll learn:** simple lookup/date-range logic
**Difficulty:** Easy

---

## PHASE 1 — Team Overview

### TICKET-101: Build ingestion script for teams, matches, standings
**Goal:** Automate what you did manually in TICKET-002.
**Tasks:**
- [ ] Write a script that pulls fixtures/results/standings from football-data.org
- [ ] Load into your Postgres tables
- [ ] Handle basic errors (API downtime, rate limits)
**You'll learn:** ETL scripting, error handling, idempotent writes (don't duplicate rows on rerun)
**Difficulty:** Medium

### TICKET-102: Schedule the ingestion job
**Goal:** Data refreshes without you running scripts manually.
**Tasks:**
- [ ] Install Prefect (or chosen orchestrator) locally
- [ ] Wrap TICKET-101's script as a scheduled flow
- [ ] Add basic logging so you can see if a run failed
**You'll learn:** orchestration basics, scheduling, logging
**Difficulty:** Medium

### TICKET-103: Build Team Overview backend endpoints
**Goal:** An API that serves season snapshot + xG trend data.
**Tasks:**
- [ ] Set up a basic FastAPI app
- [ ] Build endpoint: season snapshot (position, points, GD, form)
- [ ] Build endpoint: xG vs actual trend
- [ ] Test endpoints manually (Postman or browser)
**You'll learn:** API development basics, connecting an API to a database
**Difficulty:** Medium

### TICKET-104: Build Team Overview dashboard page
**Goal:** First real screen of the dashboard.
**Tasks:**
- [ ] Set up Streamlit app skeleton
- [ ] Build season snapshot widget
- [ ] Build form-strip component
- [ ] Build xG vs actual trend chart
- [ ] Connect to your FastAPI backend
**You'll learn:** dashboard/frontend basics, calling an API from a frontend
**Difficulty:** Medium

### TICKET-105: Deploy a v0 skeleton
**Goal:** Get something live early — deployment gets much harder to debug the longer you wait.
**Tasks:**
- [ ] Dockerize the API
- [ ] Dockerize the dashboard
- [ ] Deploy both to Render/Railway
- [ ] Deploy Postgres (managed free tier)
- [ ] Confirm the live dashboard shows real data
**You'll learn:** Docker basics, cloud deployment, environment variables/secrets management
**Difficulty:** Medium–Hard (but critical — do this before Phase 2)

---

## PHASE 2 — Post-Match Performance Reports

### TICKET-201: Extend schema for match events and shots
**Tasks:**
- [ ] Add `match_events`, `shots`, `player_match_stats` tables
- [ ] Update ingestion to pull event/shot-level data (likely from StatsBomb open data)
**Difficulty:** Medium

### TICKET-202: Feature-engineer per-player match metrics
**Tasks:**
- [ ] Write transformation logic (raw events → per-90, per-match aggregates)
- [ ] Consider introducing dbt here for the clean/transform layer
**You'll learn:** feature engineering, dbt basics
**Difficulty:** Medium–Hard

### TICKET-203: Build match-level endpoints
**Tasks:**
- [ ] Endpoint: match summary stats
- [ ] Endpoint: shot map data
- [ ] Endpoint: pass network data
- [ ] Endpoint: xG accumulation timeline
**Difficulty:** Medium

### TICKET-204: Build Post-Match Report dashboard page
**Tasks:**
- [ ] Match selector component
- [ ] Shot map visualization
- [ ] Pass network visualization
- [ ] xG accumulation chart
- [ ] Key stats comparison table
**Difficulty:** Medium–Hard (visualizations are the hard part here)

### TICKET-205: Auto-generated match summary text
**Tasks:**
- [ ] Write template-based text generation pulling out standout numbers
- [ ] (Optional stretch) explore an LLM-based summary generator later
**Difficulty:** Easy–Medium

---

## PHASE 3 — Player Performance Profiles

### TICKET-301: Build player season aggregates + positional benchmarks
**Tasks:**
- [ ] Create `player_season_stats`, `positional_benchmarks` tables
- [ ] Write aggregation logic (per-90 stats by position)
**Difficulty:** Medium

### TICKET-302: Design and compute the tactical-fit proxy score
**Tasks:**
- [ ] Define observable traits per manager era (defensive line height, tempo, etc. — start simple)
- [ ] Write scoring logic comparing player profile to era traits
- [ ] Document clearly that this is an approximation, not a validated metric
**You'll learn:** designing a scoring/feature model from scratch, an important portfolio skill
**Difficulty:** Hard (this is the most "research-y" ticket in the whole project)

### TICKET-303: Build player profile endpoints + page
**Tasks:**
- [ ] Endpoints: player profile, radar comparison, form trend, heatmap, discipline
- [ ] Frontend: player profile page with radar chart, trend chart, heatmap
**Difficulty:** Medium

---

## PHASE 4 — Season & Comparative Trends

### TICKET-401: Build league xG table and multi-season views
**Tasks:**
- [ ] Aggregate xG table vs actual table
- [ ] Multi-season progression queries
- [ ] Head-to-head history queries
**Difficulty:** Easy–Medium (mostly reuses existing data)

### TICKET-402: Build Season Trends dashboard page
**Tasks:**
- [ ] xG table vs actual table visualization
- [ ] Multi-season progression chart
- [ ] Head-to-head comparison view
**Difficulty:** Easy–Medium

---

## PHASE 5 — Squad Depth

### TICKET-501: Build formation/minutes/contract tables and views
**Tasks:**
- [ ] Formation usage aggregation
- [ ] Minutes distribution aggregation
- [ ] Age/contract-length matrix
**Difficulty:** Easy

### TICKET-502: Build Squad Depth dashboard page
**Tasks:**
- [ ] Formation usage chart
- [ ] Minutes distribution visualization
- [ ] Age/contract matrix view
**Difficulty:** Easy

---

## PHASE 6 — Transfer Window Intelligence Center (centerpiece)

### TICKET-601: Build transfer records and window reference data
**Tasks:**
- [ ] `transfer_windows`, `transfer_records` tables
- [ ] Ingest transfer history (likely scraped — verify source terms)
**Difficulty:** Medium

### TICKET-602: Build pre/post-signing stat snapshots
**Tasks:**
- [ ] Snapshot player stats at old club before signing
- [ ] Snapshot player stats at United after signing
- [ ] Build "signing scorecard" comparison logic
**Difficulty:** Medium–Hard

### TICKET-603: Build squad needs analysis engine
**Tasks:**
- [ ] Define "need" quantitatively (position depth gaps, age-curve gaps, minutes concentration)
- [ ] Output a ranked list of squad weaknesses per window
**Difficulty:** Hard

### TICKET-604: Build keep/sell composite scoring engine
**Tasks:**
- [ ] Define weighting logic (performance trend, age, contract, market value, tactical fit)
- [ ] Compute and store per-player scores per window
- [ ] Document your weighting decisions — you'll be asked to defend them
**Difficulty:** Hard

### TICKET-605: Build similar-player finder / alternatives-considered engine
**Tasks:**
- [ ] Build a statistical-similarity function (e.g. distance on a normalized stat vector)
- [ ] Curate a rumor/alternatives dataset per major signing (manual curation likely needed early on)
- [ ] Combine into an "alternatives considered" output per signing
**Difficulty:** Hard

### TICKET-606: Build backtesting engine
**Tasks:**
- [ ] Enforce a strict forward-looking data split (no using post-window data to "predict" that window)
- [ ] Generate historical recommendations per past window
- [ ] Compare against actual outcomes
**You'll learn:** avoiding data leakage/hindsight bias — a core data science skill
**Difficulty:** Hard

### TICKET-607: Build Transfer Window Intelligence dashboard page
**Tasks:**
- [ ] Window selector
- [ ] Window summary panel
- [ ] Signing scorecard component
- [ ] Alternatives-considered panel
- [ ] Needs-vs-addressed map
- [ ] Keep/sell leaderboard
- [ ] Multi-window efficiency trend
- [ ] Backtest validation view
- [ ] Clear "approximation, not ground truth" labeling throughout
**Difficulty:** Hard (largest single frontend ticket in the project)

---

## PHASE 7 — Deployment, Polish & Documentation

### TICKET-701: CI pipeline
- [ ] GitHub Actions: run tests/linting on push
**Difficulty:** Easy–Medium

### TICKET-702: CD pipeline
- [ ] Auto-deploy on merge to main
**Difficulty:** Medium

### TICKET-703: Monitoring for pipeline failures
- [ ] Alert (even just an email/Slack webhook) if a scheduled ingestion job fails
**Difficulty:** Medium

### TICKET-704: Full documentation pass
- [ ] Architecture doc, data dictionary, "known limitations" doc (based on `specs.md` Section 5)
- [ ] Record a short demo video/walkthrough for your application
**Difficulty:** Easy, but don't skip it — this is often what actually gets you the interview
