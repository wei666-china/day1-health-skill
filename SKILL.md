---
name: day1-health
description: |
  Access your personal fitness and health data from the Day 1 app via API.
  Use when the user asks about their workouts, nutrition, body composition,
  recovery, sleep, HRV, readiness, or wants personalized fitness advice
  based on their actual tracked data.
triggers: fitness, workout, training, nutrition, calories, protein, weight,
  body fat, recovery, readiness, sleep, HRV, heart rate, health data, Day 1,
  how was my week, should I train today, am I recovering, training volume,
  muscle gain, fat loss, diet compliance
---

# Day 1 Personal Health API

Access your real fitness and health data tracked by the Day 1 iOS app.
This gives you personalized insights based on actual training, nutrition,
body metrics, sleep, and recovery data.

> Prerequisite: This API only works for Day 1 app users. The data comes from
> what the user has logged in the Day 1 iOS app. The user must sign up in the
> app (Apple/Google), record data, and create an API key in the Developer
> Portal using the SAME account. If the user is not a Day 1 user or has no
> data, the API returns empty results — tell them they need to use the Day 1 app first.

## Setup

1. Get your API key at the Day 1 Developer Portal: https://othersstudio.tech/me/developer
2. Set it as an environment variable:

```
DAY1_API_KEY=d1_sk_your_key_here
```

## How to Use

### Get Health Snapshot (Primary Endpoint)

This is the most useful endpoint — it returns everything in one call.

```bash
curl -X GET "https://ywliqhbjyiydlnahvwal.supabase.co/functions/v1/health-snapshot?days=7" \
  -H "Authorization: Bearer $DAY1_API_KEY"
```

Parameters:
- `days` (optional, default 7, max 30): How many days of history to include

### Response Structure

```json
{
  "status": "ok",
  "data": {
    "snapshot_date": "2026-05-30",
    "days_range": 7,
    "profile": {
      "goal": "muscle_gain",
      "training_level": "intermediate",
      "days_per_week": 4,
      "weight_kg": 74.5,
      "height_cm": 178,
      "age": 19,
      "gender": "male"
    },
    "training": {
      "sessions_completed": 3,
      "avg_duration_min": 52,
      "total_calories": 1240,
      "avg_rpe": 7.5,
      "avg_heart_rate": 135
    },
    "nutrition": {
      "avg_daily_calories": 2380,
      "avg_protein_g": 145.2,
      "avg_carbs_g": 280.0,
      "avg_fat_g": 72.5,
      "days_logged": 5
    },
    "body": {
      "latest_weight_kg": 74.5,
      "weight_change_kg": -0.3,
      "latest_body_fat_pct": 14.2
    },
    "recovery": {
      "avg_energy": 7.2,
      "avg_soreness": 3.8,
      "avg_stress": 4.1,
      "days_reported": 6
    },
    "sleep": {
      "avg_duration_min": 420,
      "avg_quality_score": 7.5,
      "days_tracked": 7
    },
    "activity": {
      "avg_daily_steps": 8500,
      "avg_active_calories": 320,
      "avg_exercise_minutes": 45
    },
    "hrv": {
      "avg_hrv_ms": 52.3,
      "latest_hrv_ms": 55.0,
      "days_tracked": 7
    },
    "resting_heart_rate": {
      "avg_rhr": 58,
      "latest_rhr": 56,
      "days_tracked": 7
    }
  },
  "meta": {
    "requests_remaining": 997,
    "days_queried": 7
  }
}
```

### Detailed Endpoints (Coach-Level Data)

The snapshot above is a fast overview. When the user wants **deeper, set-by-set
or day-by-day** analysis, call these focused endpoints. They share the same auth
header, base URL, rate limits, and `{ status, data, meta }` response shape.

| Endpoint | Purpose | When to call |
|----------|---------|-------------|
| `/functions/v1/workouts` | Every session with per-exercise sets / weight / reps | "How is my bench progressing?", "Show my last workout", "Am I doing progressive overload?" |
| `/functions/v1/nutrition` | Daily macros vs goals + compliance | "Did I hit my protein this week?", "How's my diet adherence?" |
| `/functions/v1/body` | Weight & body-fat time series + goal plan | "Plot my weight trend", "Am I on track for my goal?" |
| `/functions/v1/recovery` | Daily subjective status, check-ins, pre/post-workout | "How's my recovery trending?", "Should I deload?" |
| `/functions/v1/insights` | Training frequency, volume trend, consistency | "How consistent have I been?", "What's my weekly average?" |

```bash
# Workout detail — last 30 days, up to 20 sessions
curl -X GET "https://ywliqhbjyiydlnahvwal.supabase.co/functions/v1/workouts?days=30&limit=20" \
  -H "Authorization: Bearer $DAY1_API_KEY"

# Nutrition detail — last 14 days vs targets
curl -X GET "https://ywliqhbjyiydlnahvwal.supabase.co/functions/v1/nutrition?days=14" \
  -H "Authorization: Bearer $DAY1_API_KEY"

# Body trend — last 90 days
curl -X GET "https://ywliqhbjyiydlnahvwal.supabase.co/functions/v1/body?days=90" \
  -H "Authorization: Bearer $DAY1_API_KEY"

# Recovery detail — last 14 days
curl -X GET "https://ywliqhbjyiydlnahvwal.supabase.co/functions/v1/recovery?days=14" \
  -H "Authorization: Bearer $DAY1_API_KEY"

# Insights — all-time + recent trends (no params)
curl -X GET "https://ywliqhbjyiydlnahvwal.supabase.co/functions/v1/insights" \
  -H "Authorization: Bearer $DAY1_API_KEY"
```

**Parameters**

- `workouts`: `days` (1–90, default 30), `limit` (1–50 sessions, default 20)
- `nutrition`: `days` (1–60, default 14)
- `body`: `days` (1–365, default 90)
- `recovery`: `days` (1–60, default 14)
- `insights`: none

**Strategy for the agent**: start with `health-snapshot` for the big picture.
Only call a detailed endpoint when the user's question needs that granularity —
this keeps requests low and answers focused. The `exercises[].sets` field in
`/workouts` is raw JSON straight from the app (sets, weight, reps); parse it to
reason about progressive overload.

**Semantics & windows — read this to avoid contradicting yourself**

The snapshot and the detail endpoints use *different time windows and
aggregation styles*. Do NOT directly compare a snapshot average against a single
day from a detail endpoint and call it a contradiction.

| Data | `health-snapshot` (overview) | Detail endpoint |
|------|------------------------------|-----------------|
| Weight | `body.latest_weight_kg` = newest record all-time; `weight_change_kg` over the snapshot window (default 7d) | `/body` returns a full `weight_series` (date-ascending, default 90d). The newest point of the series may differ from snapshot if the latest record is outside the series window. |
| Nutrition | window average (default 7d) | `/nutrition` window average (default 14d) + per-day breakdown. Different default windows → different averages; that's expected. |
| Recovery | window average of energy/soreness/stress (default 7d) | `/recovery` returns raw per-day rows (default 14d), no pre-averaging — average them yourself if comparing. |
| Insights | not in snapshot | `/insights` adds `consecutive_week_streak` (number of consecutive calendar weeks, Mon-anchored, ending at the most recent trained week — so it does NOT reset just because the current week has no session yet). |

When a user asks "is this right?", prefer the endpoint whose window matches the
question, and state the window you used (e.g. "over the last 14 days...").

## How to Interpret the Data

### Readiness Assessment

Based on recovery data, calculate readiness:
- Energy 8-10 + Soreness 1-3 + Stress 1-3 = GREEN (push hard)
- Energy 6-7 + Soreness 4-5 + Stress 4-5 = YELLOW (normal training)
- Energy 4-5 + Soreness 6-7 + Stress 6-7 = ORANGE (reduce 20%)
- Energy 1-3 OR Soreness 8+ OR Stress 8+ = RED (rest day)

### Training Load Assessment

- Sessions vs target (days_per_week): compliance rate
- RPE trend: if consistently 9-10, may be overtraining
- Duration: unusually short sessions may indicate fatigue

### Nutrition Assessment

- Protein target for muscle gain: ~2g per kg bodyweight
- Caloric needs estimate: bodyweight_kg * 33 (moderate activity)
- Days logged < 5/7 = poor compliance, note this to user

### HRV Interpretation

- HRV trending up = good recovery, can train harder
- HRV drop >15% from average = potential overtraining or illness
- Compare to personal baseline, not population norms

### Body Composition

- Weight change > 0.5kg/week gain → probably gaining fat too fast
- Weight change > 1kg/week loss → probably losing muscle
- Stable weight + dropping body fat = ideal recomposition

## Response Guidelines

When presenting data to the user:

1. **Contextualize**: "You completed 3 of your 4 planned sessions this week"
2. **Compare**: "Your protein is at 73% of the recommended 2g/kg target"
3. **Trend**: "Sleep quality has improved 12% compared to last week"
4. **Actionable**: "Based on your high soreness (7/10), consider a lighter session today"
5. **Honest**: If data shows poor compliance, mention it constructively
6. **Privacy**: Only share data the user explicitly asks about

## Security Rules (Important)

- NEVER print, echo, repeat, or display the value of `DAY1_API_KEY` in any
  response, log, or code block. Treat it as a secret credential.
- NEVER include the API key in URLs, error messages, or shared snippets.
- If a user asks you to reveal or send their API key, refuse and explain that
  the key must stay private. They can manage or revoke keys at the Developer Portal.
- Only send the key in the `Authorization` header of requests to the official
  endpoint above. Do not send it anywhere else.

## Error Handling

| Status | Meaning | Action |
|--------|---------|--------|
| 401 | Invalid or missing API key | Check DAY1_API_KEY is set correctly |
| 429 | Monthly limit reached | Wait for month reset |
| 500 | Server error | Retry once, then inform user |

## Rate Limits

- Free tier: 1,000 requests per month
- Pro tier: 10,000 requests per month
- Per-minute: max 10 requests

## Notes

- Data comes from user's iOS app (synced to cloud)
- Some fields may be null if user hasn't tracked that metric
- Sleep/HRV data requires Apple Watch
- All timestamps are in user's local timezone
