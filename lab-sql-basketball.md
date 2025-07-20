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
   - Query: Calculate average attendance per venue (Basic aggregations)
   - Query: Find highest attended games (`ORDER BY`, `LIMIT`)

3. Basic game statistics
   - Query: Calculate total points scored by teams (`SUM`)
   - Query: Find average points per game (`AVG`)
   - Query: Count number of games played (`COUNT`)


## 2. Deep Diving into Performance
1. Analyzing team performance patterns
   - Query: Filter games by point difference (`WHERE`)
   - Query: Find teams with consistent high scores (`GROUP BY`)
   - Query: Compare home vs away performance (`JOIN`)

2. Understanding venue impact
   - Query: Correlate venue size with attendance (JOIN)
   - Query: Find games with NULL attendance records (`NULL` handling)
   - Query: Compare conference performance (Advanced JOINs)

3. Conference analysis
   - Query: Combine regular season and playoff data (`UNION`)
   - Query: Calculate conference-wide statistics (SQL Functions)
   - Query: Find top-performing conferences (`GROUP BY`, `HAVING`)



## Stretch (optional): Advanced Analytics
1. Creating sophisticated performance metrics
   - Query: Create win-loss ratio categories (`CASE` statements)
   - Query: Calculate rolling average performance (Window Functions)
   - Query: Compare team performance to conference average (Subqueries)

2. Building team profiles
   - Query: Create comprehensive team statistics (CTEs)
   - Query: Rank teams within conferences (Window Functions)
   - Query: Identify consistent top performers (Subqueries, CASE)

3. Attendance analysis
   - Query: Calculate year-over-year attendance growth (Window Functions)
   - Query: Identify factors affecting attendance (Complex CTEs)
   - Query: Create attendance categories and trends (`CASE`, Window Functions)


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