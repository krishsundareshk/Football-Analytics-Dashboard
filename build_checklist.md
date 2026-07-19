# Manchester United Analytics Dashboard — Build Checklist

This is an inventory of everything that needs to exist for each module — data, database
objects, backend/API components, frontend components, and analytics artifacts. It lists
**what** needs to be built, not **how**. Organized by module, matching `specs.md`.

---

## 0. Global / Cross-Cutting (shared across all modules)

### Data
- [ ] Raw data landing zone (unprocessed ingested data)
- [ ] Cleaned/staging data layer
- [ ] Core analytics database (final, query-ready layer)
- [ ] Manager-era reference table (era name, start date, end date, manager)
- [ ] Data dictionary / schema documentation

### Backend
- [ ] Scheduled ingestion jobs (one per data source)
- [ ] Data validation/quality checks (missing values, duplicate matches, etc.)
- [ ] Core API layer serving all modules
- [ ] Logging for ingestion/pipeline runs

### Frontend
- [ ] Global dashboard shell (navigation, module routing)
- [ ] Global filters (season, manager-era, competition)
- [ ] Design system / shared UI components (cards, charts, tables)
- [ ] Loading/error states for all pages

### Deployment & Ops
- [ ] Containerized services
- [ ] CI pipeline (build/test on push)
- [ ] CD pipeline (deploy on merge)
- [ ] Hosted environment (dashboard + API + database)
- [ ] Basic monitoring/alerting for pipeline failures
- [ ] README + architecture documentation

---

## 1. Team Overview

### Data / Database
- [ ] Teams table
- [ ] Matches/results table (season-level)
- [ ] League standings table
- [ ] Squad availability (injuries/suspensions) table

### Backend
- [ ] Endpoint: season snapshot (position, points, GD, form)
- [ ] Endpoint: xG vs actual goals trend (season-level)
- [ ] Endpoint: squad availability summary

### Frontend
- [ ] Season snapshot widget
- [ ] Form strip (last 5 results) component
- [ ] xG vs actual goals trend chart
- [ ] Squad availability widget
- [ ] Manager-era badge/tag component (reused across modules)

---

## 2. Post-Match Performance Reports

### Data / Database
- [ ] Match events table (shots, passes, cards, subs)
- [ ] Shot-level table (location, xG, outcome)
- [ ] Player match-stats table (per player, per match)
- [ ] Feature-engineered per-player match metrics table

### Backend
- [ ] Endpoint: single-match summary stats
- [ ] Endpoint: shot map data for a match
- [ ] Endpoint: pass network data for a match
- [ ] Endpoint: xG accumulation timeline for a match
- [ ] Auto-generated match summary text generator

### Frontend
- [ ] Match selector component
- [ ] Shot map visualization
- [ ] Pass network visualization
- [ ] xG accumulation chart
- [ ] Key stats comparison table (team vs opponent)
- [ ] Match summary text panel

---

## 3. Player Performance Profiles

### Data / Database
- [ ] Player master table (bio, position, contract info)
- [ ] Player season aggregates table (per-90 stats)
- [ ] Player positional-average benchmark table
- [ ] Tactical-fit proxy score table (per player, per era)

### Backend
- [ ] Endpoint: player profile (season + per-90 stats)
- [ ] Endpoint: player vs positional-average comparison
- [ ] Endpoint: rolling form trend
- [ ] Endpoint: touches/positioning heatmap data
- [ ] Endpoint: discipline record
- [ ] Tactical-fit proxy score calculation artifact

### Frontend
- [ ] Player profile page
- [ ] Radar/spider chart component (player vs positional average)
- [ ] Rolling form trend chart
- [ ] Positioning heatmap visualization
- [ ] Discipline record widget
- [ ] Tactical-fit score display (clearly labeled as a proxy/approximation)

---

## 4. Transfer Window Intelligence Center *(centerpiece)*

### Data / Database
- [ ] Transfer windows reference table (window dates, per season)
- [ ] Transfer records table (player, direction, fee, from/to club)
- [ ] Pre-signing player stat snapshot table (stats at previous club)
- [ ] Post-signing player stat snapshot table (stats at United)
- [ ] Transfer rumor/alternatives-considered table
- [ ] Squad needs/gap analysis output table
- [ ] Keep/Sell composite score table
- [ ] Backtested recommendation log table

### Backend
- [ ] Endpoint: transfer window summary (ins/outs/fees/net spend)
- [ ] Endpoint: signing scorecard (pre vs post performance)
- [ ] Endpoint: alternatives-considered comparison
- [ ] Endpoint: squad needs analysis output
- [ ] Endpoint: keep/sell scores per player
- [ ] Endpoint: needs-vs-addressed map
- [ ] Endpoint: multi-window efficiency trend
- [ ] Endpoint: backtested historical recommendations
- [ ] Similar-player finder component
- [ ] Squad needs analysis engine artifact
- [ ] Keep/sell scoring engine artifact
- [ ] Backtesting engine artifact (strict forward-looking split)

### Frontend
- [ ] Transfer window selector
- [ ] Window summary panel
- [ ] Signing scorecard component
- [ ] Alternatives-considered comparison panel
- [ ] Needs-vs-addressed map visualization
- [ ] Keep/sell score leaderboard/table
- [ ] Multi-window efficiency trend chart
- [ ] Historical backtest validation view
- [ ] Disclaimers/labels clarifying proxy/approximate nature of scores

---

## 5. Season & Comparative Trends

### Data / Database
- [ ] League table with xG overlay table
- [ ] Multi-season player/team progression table
- [ ] Head-to-head history table

### Backend
- [ ] Endpoint: xG table vs actual table
- [ ] Endpoint: multi-season progression data
- [ ] Endpoint: head-to-head history

### Frontend
- [ ] xG table vs actual table visualization
- [ ] Multi-season progression chart
- [ ] Head-to-head comparison view

---

## 6. Squad Depth

### Data / Database
- [ ] Formation usage table (per match/season)
- [ ] Minutes distribution table (per player, per season)
- [ ] Age/contract-length reference table

### Backend
- [ ] Endpoint: formation usage breakdown
- [ ] Endpoint: minutes distribution
- [ ] Endpoint: age/contract matrix

### Frontend
- [ ] Formation usage chart
- [ ] Minutes distribution visualization
- [ ] Age/contract-length matrix view

---

## Notes
- Items are components/artifacts to build, not build order — see the phased roadmap discussed
  earlier (Team Overview → Post-Match → Player Profiles → Season Trends → Squad Depth → Transfer
  Window Intelligence) for sequencing.
- Any item involving the tactical-fit proxy or backtesting should carry a visible "approximation,
  not validated ground truth" label wherever it's surfaced — flagged in `specs.md` Section 5.
