<h1>
  <span class="prefix">Data Science Fundamentals</span>
  <span class="headline">Lab - SQL Basketball Analytics Challenge</span>
</h1>

# Scenario: "Basketball Analytics Challenge"

You've just been hired as a Data Analyst by the new owners of a college basketball team. They're passionate about using data to drive decisions (inspired by **Moneyball**) and want to modernize their approach.

To do this, they want to better understand the landscape of college basketball, and then deep dive into the performance of teams, in venues and in conferences.

# What you need to do
Write queries to answer all the 18 questions in parts 1 and 2 below, **Understanding the Landscape of College Basketball** and **Deep Diving into Performance**.  Save these into a `.sql` file, including the questions.

If you want to, you can also attempt the last 9 questions.  These are stretch questions and are optional.

As a reminder, here is the **Query Order**
1. `SELECT` - What columns do you want?
2. `FROM` - Which table?
   - `JOIN` or `UNION`
3. `WHERE` - What conditions must be met? 
4. `GROUP BY` - What groups do you want to see?
5. `HAVING` - What conditions must be met for groups? 
6. `ORDER BY` - How do you want to order the results?
7. `LIMIT` - How many rows do you want to return?


## 1. Understanding the Landscape of College Basketball
1. Understanding the basic landscape
   - Query: List all teams and their venues (`SELECT`, `FROM`)
   - Query: Find unique conferences and divisions (`DISTINCT`)
   - Query: View top 10 teams by venue capacity (`LIMIT`)

2. Getting familiar with attendance numbers
   - Query: List games by attendance numbers (`ORDER BY`)
   - Query: Calculate average attendance in the league (Basic aggregations)
   - Query: Find 15 highest attended games (`ORDER BY`, `LIMIT`)

3. Basic game statistics
   - Query: Calculate total points scored by teams when playing at home (`SUM`, `GROUPBY`)
   - Query: Find average points per game when playing away (`AVG`,`GROUPBY`)
   - Query: Find the total number of 'away' games played per team (`COUNT`)


## 2. Deep Diving into Performance
1. Analyzing team performance patterns
   - Query: Find all games where the home team won by at least 20 point difference. (`WHERE`)
   - Query: Find teams that consistently score a lot at home — average more than 80 points per home game. (`GROUP BY`, `HAVING`)
   - Stretch Query: Compare home vs away performance for each team (`JOIN`, `GROUP BY`)

2. Exploring venue size and attendance patterns
   - Query: Find games with NULL attendance records in your dataset (`NULL` handling)
   - Query: Calculate average venue fill percentage for all dataset, (HINT: filter where attendance is not zero or null)
   - Query: Examine whether teams with larger venues tend to attract higher home attendance (`JOIN`). For example you could:
     - Explore at the team-level: venue capacity vs average home attendance
     - Find the correlation between capacity and average home attendance

3. Conference analysis
   - Query: Which Conferences have highest avg attendance? (try USING `ROUND`)
   - Query: Compare the **home** win rate for the **'Eagles' team** across different conferences.
   - Query: Calculate several conference-wide stats by season in a single query (include average points scored (home_points), average points conceded (away_points), average point difference, and average attendance)



## Stretch (optional): Advanced Queries
1. Creating sophisticated performance metrics
   - Query: Calculate each team's overall win rate (home and away combined) and classify them as 'Elite', 'Strong', 'Average', or 'Underperforming' based on their win percentage (`CASE` statements)
   - Query: Use a window function to calculate each team's rolling average points over their last 5 games (within the same season) (Window functions, CTEs)
   - Query: Compare team performance to conference average by season (`Subqueries`)

2. Building team profiles
   - Query: Rank teams within conferences by average attendance at home (Window Functions)
   - Query: Identify consistent top performers: Analyse each team's scoring consistency by calculating their average points, scoring volatility (using `STDDEV_POP`), and games played, then label teams as 'Consistent Top Performer', 'High Scoring', or 'Other' based on these metrics (using `CASE` statements).

3. Attendance analysis
   - Query: Calculate year-over-year attendance growth (Window Functions): Calculate each team's year-over-year growth in average home attendance and show only seasons where growth can be computed (excluding the first recorded season).
   - Query: For each conference, compare the average venue fill rate on weekdays vs weekends and calculate the weekend impact as the percentage point difference.
   - Query: "For the team 'Eagles', calculate their average home attendance in each conference and classify it into categories from Very High to Very Low Attendance."


# Query Order Reminder for the stretch questions
1. `WITH` (CTEs) - optional
2. `SELECT` - What columns do you want?
3. `FROM` - Which table?
4. `JOIN` - Combine data from multiple tables
5. `WHERE` - What conditions must be met? 
6. `GROUP BY` - What groups do you want to see?
7. `HAVING` - What conditions must be met for groups?
8. `ORDER BY` - How do you want to order the results?
9. `LIMIT` - How many rows do you want to return?