# Lab - SQL Basketball Analytics Challenge: Sample Solutions

Dataset: `bigquery-public-data.ncaa_basketball`

These sample queries align with the prompts in `lab-sql-basketball.md`. They use the same tables/columns demonstrated in your modules, primarily `mbb_games_sr` and `mbb_teams` (plus a few others already referenced there).

## 1. Understanding the Landscape of College Basketball

### 1.1 Understanding the basic landscape

- List all teams and their venues (`SELECT`, `FROM`) 

```sql
SELECT
  name,
  venue_name
FROM
  `bigquery-public-data.ncaa_basketball.mbb_teams`;
```

- Find unique conferences and divisions (`DISTINCT`) 

```sql
-- Unique conferences
SELECT DISTINCT conf_name
FROM `bigquery-public-data.ncaa_basketball.mbb_teams`
```

```sql
-- Unique divisions
SELECT DISTINCT division_name 
FROM `bigquery-public-data.ncaa_basketball.mbb_teams`
```

```sql
--both in one query
SELECT
  DISTINCT conf_name,division_name 
FROM
  `bigquery-public-data`.`ncaa_basketball`.`mbb_teams`
```


- View top 10 teams by venue capacity (`LIMIT`) 

```sql
SELECT 
  name AS team_name,
  venue_capacity
FROM `bigquery-public-data.ncaa_basketball.mbb_teams`
ORDER BY venue_capacity DESC
LIMIT 10;
```

### 1.2 Getting familiar with attendance numbers

- List games by attendance numbers (`ORDER BY`) 

```sql
SELECT 
  scheduled_date,
  h_name AS home_team,
  a_name AS away_team,
  attendance
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
WHERE attendance >0
ORDER BY attendance;
```

- Calculate average attendance in the league (Basic aggregations) 

```sql
-- Approximate venue by the home team that hosts the game
SELECT 
  AVG(attendance) AS avg_attendance
FROM  `bigquery-public-data.ncaa_basketball.mbb_games_sr`
WHERE attendance > 0
```

- Find 15 highest attended games (`ORDER BY`, `LIMIT`) 

```sql
SELECT 
  scheduled_date,
  h_name AS home_team,
  a_name AS away_team,
  attendance
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
ORDER BY attendance DESC
LIMIT 15;
```

### 1.3 Basic game statistics

- Query: Calculate total points scored by teams when playing at home (`SUM`, `GROUPBY`) 
  
   ```sql
   SELECT 
     h_name AS home_team,
     SUM(h_points) AS total_home_points
   FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
   GROUP BY home_team
   ```
- Query: Find average points per game when playing away (`AVG`,`GROUPBY`) 

```sql
SELECT 
  a_name AS away_team,
  AVG(a_points) AS avg_away_points
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
GROUP BY away_team
```

- Query: Find the total number of 'away' games played per team (`COUNT`) 

```sql
SELECT 
  a_name AS away_team,
  COUNT(*) AS total_away_games
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
GROUP BY away_team;
```

## 2. Deep Diving into Performance

### 2.1 Analyzing team performance patterns

- Find all games where the home team won by at least 20 point difference. (`WHERE`) 

```sql
SELECT 
  scheduled_date,
  h_name AS home_team,
  a_name AS away_team,
  h_points,
  a_points,
  h_points - a_points AS point_diff
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
WHERE h_points - a_points >= 20;
```

- Find teams that consistently score a lot at home — average more than 80 points per home game. (`GROUP BY`, `HAVING`) 

```sql
SELECT 
  h_name AS home_team,
  avg(h_points) as avg_home_points
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
GROUP BY home_team
HAVING avg_home_points > 80
ORDER BY avg(h_points) DESC
```

- Stretch: Compare home vs away performance for each team (`JOIN`, `GROUP BY`) 

```sql
SELECT
  h.team,
  h.avg_home_points AS avg_home_points,
  a.avg_away_points AS avg_away_points,
  h.avg_home_points - a.avg_away_points AS home_vs_away_diff
FROM (
  SELECT
    h_name AS team,
    AVG(h_points) AS avg_home_points
  FROM
    `bigquery-public-data`.`ncaa_basketball`.`mbb_games_sr`
  GROUP BY
    team ) AS h
INNER JOIN (
  SELECT
    a_name AS team,
    AVG(a_points) AS avg_away_points
  FROM
    `bigquery-public-data`.`ncaa_basketball`.`mbb_games_sr`
  GROUP BY
    team ) AS a
ON
  h.team = a.team

ORDER BY home_vs_away_diff DESC;

```

### 2.2 Exploring Venue Size and Attendance Patterns

- Find games with NULL attendance records in your dataset (`NULL` handling) 

```sql
SELECT 
  scheduled_date,
  h_name AS home_team,
  a_name AS away_team,
  attendance
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
WHERE attendance IS NULL
ORDER BY scheduled_date DESC;
```

- Calculate average venue fill percentage for all dataset, (HINT: filter where attendance is not zero or null)
``` sql
SELECT
  AVG(games.attendance / games.venue_capacity) AS average_venue_fill_percentage
FROM
  `bigquery-public-data`.`ncaa_basketball`.`mbb_games_sr` AS games
WHERE
  games.attendance IS NOT NULL
  AND games.attendance > 0
```

- Examine whether teams with larger venues tend to attract higher home attendance (`JOIN`). For example you could:
  - Explore at the team-level: venue capacity vs average home attendance 
  - Find the correlation between capacity and average home attendance 

```sql
-- Team-level: venue capacity vs average home attendance
SELECT
  t.name AS team,
  t.venue_capacity AS capacity,
  ROUND(AVG(g.attendance), 1) AS avg_home_attendance
FROM `bigquery-public-data.ncaa_basketball.mbb_teams` AS t
JOIN `bigquery-public-data.ncaa_basketball.mbb_games_sr` AS g
  ON g.h_id = t.id
WHERE g.attendance > 0
  AND t.venue_capacity IS NOT NULL
GROUP BY team, capacity
ORDER BY capacity DESC;
```

```sql

-- One-number summary: correlation between capacity and average home attendance
SELECT
  CORR(capacity, avg_attendance) AS capacity_attendance_corr
FROM (
  SELECT
    t.id,
    t.venue_capacity AS capacity,
    AVG(g.attendance) AS avg_attendance
  FROM `bigquery-public-data.ncaa_basketball.mbb_teams` t
  JOIN `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
    ON g.h_id = t.id
  WHERE g.attendance > 0
    AND t.venue_capacity IS NOT NULL
  GROUP BY t.id, capacity
);
```

### 2.3 Conference analysis
- Which Conferences have highest avg attendance? (trying using `ROUND`)

``` sql
SELECT 
  t.conf_name AS conference,
  ROUND(AVG(g.attendance), 0) AS avg_attendance
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t
  ON g.h_id = t.id
WHERE g.attendance > 0 AND g.attendance IS NOT NULL
GROUP BY conference
ORDER BY avg_attendance DESC;
```

- Compare the **home** win rate for the **'Eagles' team** across different conferences.

```sql 
SELECT 
  t.name AS team,
  t.conf_name AS conference,
  ROUND(100 * COUNTIF(g.h_points > g.a_points) / COUNT(*), 1) AS home_win_pct
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t 
  ON g.h_id = t.id
WHERE t.name = 'Eagles'
GROUP BY team, conference
ORDER BY home_win_pct DESC;
```


- Calculate severak conference-wide stats by season in a single query (include average points scored (home_points), average points conceded (away_points), average point difference, and average attendance)(`UNION ALL`)

```sql
WITH team_games AS (
  -- Home games: points scored at home and points conceded at home
  SELECT 
    t.conf_name, 
    g.season, 
    g.attendance, 
    g.h_points AS home_points, 
    g.a_points AS away_points
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
  JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t
    ON g.h_id = t.id
  
  UNION ALL
  
  -- Away games: points scored away and points conceded away
  SELECT 
    t.conf_name, 
    g.season, 
    g.attendance, 
    g.a_points AS home_points, 
    g.h_points AS away_points
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
  JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t
    ON g.a_id = t.id
)
SELECT 
  conf_name,
  season,
  AVG(home_points) AS avg_points_home_team,
  AVG(away_points) AS avg_points_away_team,
  AVG(home_points - away_points) AS avg_point_diff,
  AVG(attendance) AS avg_attendance
FROM team_games
GROUP BY conf_name, season
ORDER BY season DESC, avg_point_diff DESC;
```

## Stretch (optional): Advanced Analytics

### 3.1 Creating sophisticated performance metrics

- Calculate each team’s overall win rate (home and away combined) and classify them as 'Elite', 'Strong', 'Average', or 'Underperforming' based on their win percentage (`CASE` statements)

```sql
WITH team_results AS (
  SELECT h_name AS team_name, CASE WHEN h_points > a_points THEN 1 ELSE 0 END AS win
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
  UNION ALL
  SELECT a_name AS team_name, CASE WHEN a_points > h_points THEN 1 ELSE 0 END AS win
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
),
team_winrate AS (
  SELECT team_name, AVG(win) AS win_rate
  FROM team_results
  GROUP BY team_name
)
SELECT 
  team_name,
  win_rate,
  CASE 
    WHEN win_rate >= 0.70 THEN 'Elite'
    WHEN win_rate >= 0.55 THEN 'Strong'
    WHEN win_rate >= 0.45 THEN 'Average'
    ELSE 'Underperforming'
  END AS win_loss_category
FROM team_winrate
ORDER BY win_rate DESC;
```

- Use a window function to calculate each team’s rolling average points over their last 5 games (within the same season) (Window functions, CTEs)

```sql
WITH team_games AS (
  SELECT season, scheduled_date, h_name AS team_name, h_points AS points
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
  UNION ALL
  SELECT season, scheduled_date, a_name AS team_name, a_points AS points
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
)
SELECT 
  season,
  team_name,
  scheduled_date,
  points,
  AVG(points) OVER (
    PARTITION BY season, team_name
    ORDER BY scheduled_date
    ROWS BETWEEN 4 PRECEDING AND CURRENT ROW
  ) AS rolling_avg_points_last_5
FROM team_games
WHERE team_name IS NOT NULL
      AND points IS NOT NULL OR points <=0
ORDER BY team_name, season, scheduled_date;

```

- Compare team performance to conference average by season (`Subqueries`)

```sql
WITH team_games AS (
  SELECT season, h_name AS team_name, h_points AS points FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
  UNION ALL
  SELECT season, a_name AS team_name, a_points AS points FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
)
SELECT 
  t.conf_name,
  tg.season,
  tg.team_name,
  AVG(tg.points) AS team_avg_points,
  (
    SELECT AVG(points)
    FROM team_games tg2
    JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t2
      ON t2.name = tg2.team_name
    WHERE t2.conf_name = t.conf_name
      AND tg2.season = tg.season
  ) AS conference_avg_points
FROM team_games tg
JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t
  ON t.name = tg.team_name
GROUP BY t.conf_name, tg.season, tg.team_name
ORDER BY tg.season DESC, (team_avg_points - conference_avg_points) DESC;
```

### 3.2 Building team profiles

- Rank teams within conferences by average attendance at home (Window Functions)

```sql
SELECT 
  t.conf_name,
  g.h_name AS team_name,
  AVG(g.attendance) AS avg_home_attendance,
  RANK() OVER (
    PARTITION BY t.conf_name
    ORDER BY AVG(g.attendance) DESC
  ) AS attendance_rank_in_conf
FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t
  ON g.h_name = t.name
GROUP BY t.conf_name, team_name
ORDER BY t.conf_name, attendance_rank_in_conf;
```

- Identify consistent top performers: Analyse each team’s scoring consistency by calculating their average points, scoring volatility (using `STDDEV_POP`), and games played, then label teams as 'Consistent Top Performer', 'High Scoring', or 'Other' based on these metrics (using `CASE` statements).

```sql
WITH team_points AS (
  SELECT h_name AS team_name, h_points AS points FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
  UNION ALL
  SELECT a_name AS team_name, a_points AS points FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
),
agg AS (
  SELECT 
    team_name,
    AVG(points) AS avg_points,
    STDDEV_POP(points) AS points_volatility,
    COUNT(*) AS games_played
  FROM team_points
  GROUP BY team_name
)
SELECT 
  team_name,
  avg_points,
  points_volatility,
  games_played,
  CASE 
    WHEN avg_points >= 80 AND points_volatility <= 10 AND games_played >= 20 THEN 'Consistent Top Performer'
    WHEN avg_points >= 80 THEN 'High Scoring'
    ELSE 'Other'
  END AS performance_label
FROM agg
ORDER BY performance_label DESC, avg_points DESC;
```

### 3.3 Attendance analysis

- Calculate year-over-year attendance growth (Window Functions): Calculate each team’s year-over-year growth in average home attendance and show only seasons where growth can be computed (excluding the first recorded season).

```sql
-- Attribute attendance to the home team
WITH team_season_att AS (
  SELECT 
    g.season,
    g.h_name AS team_name,
    AVG(g.attendance) AS avg_home_attendance
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
  WHERE g.attendance > 0
  GROUP BY season, team_name
),
team_yoy AS (
  SELECT 
    team_name,
    season,
    avg_home_attendance,
    LAG(avg_home_attendance) OVER (
      PARTITION BY team_name
      ORDER BY season
    ) AS prev_season_attendance,
    SAFE_DIVIDE(
      avg_home_attendance - LAG(avg_home_attendance) OVER (PARTITION BY team_name ORDER BY season),
      LAG(avg_home_attendance) OVER (PARTITION BY team_name ORDER BY season)
    ) AS yoy_growth
  FROM team_season_att
)
SELECT *
FROM team_yoy
WHERE yoy_growth IS NOT NULL
ORDER BY team_name, season;

```

- For each conference, compare the average venue fill rate on weekdays vs weekends and calculate the weekend impact as the percentage point difference.

```sql
-- Compare weekday vs weekend fill rate and the weekend impact
WITH base AS (
  SELECT
    t.conf_name AS conference,
    SAFE_DIVIDE(g.attendance, t.venue_capacity) AS fill_pct,
    EXTRACT(DAYOFWEEK FROM g.scheduled_date) IN (1, 7) AS is_weekend  -- Sun=1, Sat=7
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
  JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t
    ON g.h_id = t.id
  WHERE g.attendance > 0
    AND t.venue_capacity IS NOT NULL
),
agg AS (
  SELECT
    conference,
    AVG(IF(is_weekend, fill_pct, NULL)) AS weekend_fill,
    AVG(IF(NOT is_weekend, fill_pct, NULL)) AS weekday_fill
  FROM base
  GROUP BY conference
)
SELECT
  conference,
  ROUND(100 * weekday_fill, 1) AS weekday_fill_pct,
  ROUND(100 * weekend_fill, 1) AS weekend_fill_pct,
  ROUND(100 * (weekend_fill - weekday_fill), 1) AS weekend_impact_pct
FROM agg
ORDER BY weekend_impact_pct DESC;

```

- For the team 'Eagles', calculate their average home attendance in each conference and classify it into categories from Very High to Very Low Attendance.

```sql
-- Categorise teams by average home attendance within each conference
WITH team_attendance AS (
  SELECT
    t.conf_name AS conference,
    t.name AS team,
    AVG(g.attendance) AS avg_home_attendance
  FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr` g
  JOIN `bigquery-public-data.ncaa_basketball.mbb_teams` t
    ON g.h_id = t.id
  WHERE g.attendance > 0
  GROUP BY conference, team
)
SELECT
  team,
  conference,

  ROUND(avg_home_attendance, 0) AS avg_home_attendance,
  CASE
    WHEN avg_home_attendance >= 15000 THEN 'Very High Attendance'
    WHEN avg_home_attendance >= 10000 THEN 'High Attendance'
    WHEN avg_home_attendance >= 5000  THEN 'Moderate Attendance'
    WHEN avg_home_attendance >= 2500  THEN 'Low Attendance'
    ELSE 'Very Low Attendance'
  END AS attendance_category
FROM team_attendance
WHERE team = 'Eagles'
ORDER BY team,conference, avg_home_attendance DESC;
```

