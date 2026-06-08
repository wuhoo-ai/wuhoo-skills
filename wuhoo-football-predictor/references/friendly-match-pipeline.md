# Friendly Match Data Pipeline — Methodology & Pitfalls

## Data Flow

```
web_search / manual input
        ↓
friendly_matches.json  (raw match results)
        ↓
fetch_friendlies.py --compute-form
        ↓
friendly_form_adjustments.json  (team-level ELO adjustments)
        ↓
wc2026_predict.py  (Layer 4.5 — Friendly Match Form)
```

## Adjustment Algorithm

For each completed match, the script:
1. Computes ELO-expected win probability for team_a via `1/(1+10^(-diff/400))`
2. Compares expected win prob vs actual result (win/draw/loss + goal diff)
3. Derives surprise factor: `surprise = expected_win - (1 if won else 0)`  
4. Maps to ELO adjustment: `base = round(surprise * 40)`, capped at ±40
5. Applies time decay: `weight = 0.9 ^ (days_ago / 7)`
6. Blends weighted average (70%) with raw average (30%) for final adjustment

Scale reference:
- Expected win + won by 1 goal: +5 to +10 (routine)
- Expected win + draw: -10 to -15 (disappointing)
- Expected win + lost: -20 to -40 (major upset)  
- Expected loss + won: +20 to +40 (major positive surprise)
- Expected draw + draw: ±0 to ±5 (neutral)

## Web Scraping Limitations

### JS-Rendered Score Pages (CANNOT extract)
These sites load scores via JavaScript — `web_extract` only gets static HTML:
- **ESPN** (espn.com/soccer/match/...) — scores are JS-injected, only sees nav/ads
- **Flashscore** (flashscore.com) — fully JS-rendered, blank content
- **AiScore** (aiscore.com) — Nuxt.js SPA, no static score
- **FOX Sports** (foxsports.com) — boxscores are JS components
- **365Scores** (365scores.com) — React SPA

### Partially Usable Sources
- **The Guardian** (theguardian.com/football/friendlies/results) — lists match dates but scores hidden behind image crests
- **FIFA.com** (fifa.com/.../pre-tournament-warm-up-results) — content-heavy pages, scores embedded in paragraphs but truncated by web_extract's 5K char limit
- **YouTube** (video descriptions) — sometimes has final scores in description text

### Viable Data Sources (confirmed working)
- **Facebook groups** — user-posted score images/FT posts (found France 1-2 CIV here)
- **BolaVIP** (bolavip.com) — live blog text includes final scores inline
- **BBC Sport** (bbc.com/sport/football/live/...) — live text feed, scores in page title
- **Twitter/X** — @FOXSoccer, @ESPNFC post final scores with hashtags

### Recommended Collection Strategy
1. Start with FIFA.com warm-up page for the schedule
2. Cross-reference with BBC Sport / BolaVIP for text-accessible scores
3. Fall back to web_search with specific "FT: TeamA Score TeamB" queries
4. Manual entry via `fetch_friendlies.py --add` when all else fails

## CLI Reference

```bash
# Add a match result
python3.11 scripts/fetch_friendlies.py --add \
  '{"team_a":"France","team_b":"Ivory Coast","score_a":1,"score_b":2,"date":"2026-06-04","venue":"Stade de la Beaujoire","neutral":false}'

# List all recorded matches
python3.11 scripts/fetch_friendlies.py --list

# Show matches with TBD results
python3.11 scripts/fetch_friendlies.py --pending

# Recompute form adjustments (after adding new results)
python3.11 scripts/fetch_friendlies.py --compute-form
```

## Current Known Gap (June 5, 2026)
Two June 4 matches remain unconfirmed due to JS rendering barriers:
- **Mexico vs Serbia** (Estadio Nemesio Diez, Toluca)
- **Czech Republic vs Guatemala** (Sports Illustrated Stadium, Harrison NJ)

These were the last pre-WC warm-up matches for these teams. Their results are on ESPN/Flashscore but inaccessible via static extraction.
