# **Intermediate SQL**

# 1.We'll take the CASE

**</> Basic CASE statements**

What is your favorite team?

The European Soccer Database contains data about 12,800 matches from 11 countries played between 2011-2015! Throughout this course, you will be shown filtered versions of the tables in this database in order to better explore their contents.

In this exercise, you will identify matches played between FC Schalke 04 and FC Bayern Munich. There are 2 teams identified in each match in the hometeam_id and awayteam_id columns, available to you in the filtered matches_germany table. ID can join to the team_api_id column in the teams_germany table, but you cannot perform a join on both at the same time.

However, you can perform this operation using a CASE statement once you've identified the team_api_id associated with each team!


- Select the team's long name and API id from the teams_germany table.

- Filter the query for FC Schalke 04 and FC Bayern Munich using IN, giving you the team_api_IDs needed for the next step.

```sql 
SELECT
	-- Select the team long name and team API id
	team_long_name,
	team_api_id
FROM teams_germany
-- Only include FC Schalke 04 and FC Bayern Munich
WHERE team_long_name IN ('FC Schalke 04', 'FC Bayern Munich');
```

- Create a CASE statement that identifies whether a match in Germany included FC Bayern Munich, FC Schalke 04, or neither as the home team.

- Group the query by the CASE statement alias, home_team.

```sql 
-- Identify the home team as Bayern Munich, Schalke 04, or neither
SELECT 
	CASE WHEN hometeam_id = 10189 THEN 'FC Schalke 04'
        WHEN hometeam_id = 9823 THEN 'FC Bayern Munich'
         ELSE 'Other' END AS home_team,
	COUNT(id) AS total_matches
FROM matches_germany
-- Group by the CASE statement alias
GROUP BY home_team;
```

**</> CASE statements comparing column values**

Barcelona is considered one of the strongest teams in Spain's soccer league.

In this exercise, you will be creating a list of matches in the 2011/2012 season where Barcelona was the home team. You will do this using a CASE statement that compares the values of two columns to create a new group -- wins, losses, and ties.

In 3 steps, you will build a query that identifies a match's winner, identifies the identity of the opponent, and finally filters for Barcelona as the home team. Completing a query in this order will allow you to watch your results take shape with each new piece of information.

The matches_spain table currently contains Barcelona's matches from the 2011/2012 season, and has two key columns, hometeam_id and awayteam_id, that can be joined with the teams_spain table. However, you can only join teams_spain to one column at a time.

- Select the date of the match and create a CASE statement to identify matches as home wins, home losses, or ties.

```sql 
SELECT 
	-- Select the date of the match
	date,
	-- Identify home wins, losses, or ties
	CASE WHEN home_goal > away_goal THEN 'Home win!'
        WHEN home_goal < away_goal THEN 'Home loss :(' 
        ELSE 'Tie' END AS outcome
FROM matches_spain;
```

- Left join the teams_spain table team_api_id column to the matches_spain table awayteam_id. This allows us to retrieve the away team's identity.

- Select team_long_name from teams_spain as opponent and complete the CASE statement from Step 1.

```sql 
SELECT 
	m.date,
	--Select the team long name column and call it 'opponent'
	t.team_long_name AS opponent, 
	-- Complete the CASE statement with an alias
	CASE WHEN m.home_goal >  m.away_goal THEN 'Home win!'
        WHEN m.home_goal < m.away_goal THEN 'Home loss :('
        ELSE 'Tie' END AS outcome
FROM matches_spain AS m
-- Left join teams_spain onto matches_spain
LEFT JOIN teams_spain AS t
ON m.awayteam_id = t.team_api_id;
```

- Complete the same CASE statement as the previous steps.

- Filter for matches where the home team is FC Barcelona (id = 8634).

```sql 
SELECT 
	m.date,
	t.team_long_name AS opponent,
    -- Complete the CASE statement with an alias
	CASE WHEN m.home_goal > m.away_goal THEN 'Barcelona win!'
        WHEN m.home_goal < m.away_goal THEN 'Barcelona loss :(' 
        ELSE 'Tie' END AS outcome 
FROM matches_spain AS m
LEFT JOIN teams_spain AS t 
ON m.awayteam_id = t.team_api_id
-- Filter for Barcelona as the home team
WHERE m.hometeam_id = 8634; 
```

**</> CASE statements comparing two column values part 2**

Similar to the previous exercise, you will construct a query to determine the outcome of Barcelona's matches where they played as the away team. You will learn how to combine these two queries in chapters 2 and 3.

Did their performance differ from the matches where they were the home team?

- Complete the CASE statement to identify Barcelona's away team games (id = 8634) as wins, losses, or ties.

- Left join the teams_spain table team_api_id column on the matches_spain table hometeam_id column. This retrieves the identity of the home team opponent.

- Filter the query to only include matches where Barcelona was the away team.

- Select matches where Barcelona was the away team

```sql
SELECT  
	m.date,
	t.team_long_name AS opponent,
	CASE WHEN m.home_goal < m.away_goal THEN 'Barcelona win!'
        WHEN m.home_goal > m.away_goal THEN 'Barcelona loss :(' 
        ELSE 'Tie' END AS outcome
FROM matches_spain AS m
-- Join teams_spain to matches_spain
LEFT JOIN teams_spain AS t 
ON m.hometeam_id = t.team_api_id
WHERE m.awayteam_id = 8634;
```

**</> In CASE of rivalry**

In this exercise, you will retrieve information about matches played between Barcelona (id = 8634) and Real Madrid (id = 8633). Note that the query you are provided with already identifies the Clásico matches using a filter in the WHERE clause.

- Complete the first CASE statement, identifying Barcelona or Real Madrid as the home team using the hometeam_id column.

- Complete the second CASE statement in the same way, using awayteam_id.

```sql
SELECT 
	date,
	-- Identify the home team as Barcelona or Real Madrid
	CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
        ELSE 'Real Madrid CF' END AS home,
    -- Identify the away team as Barcelona or Real Madrid
	CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
        ELSE 'Real Madrid CF' END AS away
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
      AND (awayteam_id = 8633 OR hometeam_id = 8633);
```

- Construct the final CASE statement identifying who won each match. Note there are 3 possible outcomes, but 5 conditions that you need to identify.
- Fill in the logical operators to identify Barcelona or Real Madrid as the winner.

```sql
SELECT 
	date,
	CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END as home,
	CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END as away,
	-- Identify all possible match outcomes
	CASE WHEN home_goal > away_goal AND hometeam_id = 8634 THEN 'Barcelona win!'
        WHEN home_goal > away_goal AND hometeam_id = 8633 THEN 'Real Madrid win!'
        WHEN home_goal < away_goal AND awayteam_id = 8634 THEN 'Barcelona win!'
        WHEN home_goal < away_goal AND awayteam_id = 8633 THEN 'Real Madrid win!'
        ELSE 'Tie!' END AS outcome
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
      AND (awayteam_id = 8633 OR hometeam_id = 8633);
```

**</> Filtering your CASE statement**

- Identify Bologna's team ID listed in the teams_italy table by selecting the team_long_name and team_api_id.

```sql
-- Select team_long_name and team_api_id from team
SELECT
	team_long_name,
	team_api_id
FROM teams_italy
-- Filter for team long name
WHERE team_long_name = 'Bologna';
```

- Select the season and date that a match was played.
- Complete the CASE statement so that only Bologna's home and away wins are identified.

```sql
-- Select the season and date columns
SELECT 
	season,
	date,
    -- Identify when Bologna won a match
	CASE WHEN hometeam_id = 9857 
        AND home_goal > away_goal 
        THEN 'Bologna Win'
		WHEN awayteam_id = 9857 
        AND away_goal > home_goal 
        THEN 'Bologna Win' 
		END AS outcome
FROM matches_italy;
```

- Select the home_goal and away_goal for each match.
- Use the CASE statement in the WHERE clause to filter all NULL values generated by the statement in the previous step.

```sql
-- Select the season, date, home_goal, and away_goal columns
SELECT 
	season,
    date,
	home_goal,
	away_goal
FROM matches_italy
WHERE 
-- Exclude games not won by Bologna
	CASE WHEN hometeam_id = 9857 AND home_goal > away_goal THEN 'Bologna Win'
		WHEN awayteam_id = 9857 AND away_goal > home_goal THEN 'Bologna Win' 
		END IS NOT NULL;
```

**</> COUNT using CASE WHEN**

- Create a CASE statement that identifies the id of matches played in the 2012/2013 season. Specify that you want ELSE values to be NULL.
- Wrap the CASE statement in a COUNT function and group the query by the country alias.

```sql
SELECT 
	c.name AS country,
    -- Count games from the 2012/2013 season
	COUNT(CASE WHEN m.season = '2012/2013' 
        	THEN m.id ELSE NULL END) AS matches_2012_2013
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```

- Create 3 CASE WHEN statements counting the matches played in each country across the 3 seasons.
- END your CASE statement without an ELSE clause.

```sql
SELECT 
	c.name AS country,
    -- Count matches in each of the 3 seasons
	COUNT(CASE WHEN m.season = '2012/2013' THEN m.id ELSE NULL END) AS matches_2012_2013,
	COUNT(CASE WHEN m.season = '2013/2014' THEN m.id ELSE NULL END) AS matches_2013_2014,
	COUNT(CASE WHEN m.season = '2014/2015' THEN m.id ELSE NULL END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```

**</> COUNT and CASE WHEN with multiple conditions**

- Create 3 CASE statements to "count" matches in the '2012/2013', '2013/2014', and '2014/2015' seasons, respectively.
- Have each CASE statement return a 1 for every match you want to include, and a 0 for every match to exclude.
- Wrap the CASE statement in a SUM to return the total matches played in each season.
- Group the query by the country name alias.

```sql
SELECT 
	c.name AS country,
    -- Sum the total records in each season where the home team won
	SUM(CASE WHEN m.season = '2012/2013' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2012_2013,
 	SUM(CASE WHEN m.season = '2013/2014' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2013_2014,
	SUM(CASE WHEN m.season = '2014/2015' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```

**</> Calculating percent with CASE and AVG**

Your task is to examine the number of wins, losses, and ties in each country. The matches table is filtered to include all matches from the 2013/2014 and 2014/2015 seasons.

- Create 3 CASE statements to COUNT the total number of home team wins, away team wins, and ties, which will allow you to examine the total number of records.

```sql
SELECT 
    c.name AS country,
    -- Count the home wins, away wins, and ties in each country
	COUNT(CASE WHEN m.home_goal > m.away_goal THEN m.id 
        END) AS home_wins,
	COUNT(CASE WHEN m.home_goal < m.away_goal THEN m.id 
        END) AS away_wins,
	COUNT(CASE WHEN m.home_goal = m.away_goal THEN m.id 
        END) AS ties
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```

- Calculate the percentage of matches tied using a CASE statement inside AVG.
- Fill in the logical operators for each statement. Alias your columns as ties_2013_2014 and ties_2014_2015, respectively.

```sql
SELECT 
	c.name AS country,
    -- Calculate the percentage of tied games in each season
	AVG(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
			WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
			END) AS ties_2013_2014,
	AVG(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
			WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
			END) AS ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```

- The previous "ties" columns returned values with 14 decimal points, which is not easy to interpret. Use the ROUND function to round to 2 decimal points.

```sql
SELECT 
	c.name AS country,
    -- Round the percentage of tied games to 2 decimal points
	ROUND(AVG(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
			 WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
			 END),2) AS pct_ties_2013_2014,
	ROUND(AVG(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
			 WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
			 END),2) AS pct_ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```

# 2.Short and Simple Subqueries

**</> Filtering using scalar subqueries**

In this exercise, you will generate a list of matches where the total goals scored (for both teams in total) is more than 3 times the average for games in the matches_2013_2014 table, which includes all games played in the 2013/2014 season.

- Calculate triple the average home + away goals scored across all matches. This will become your subquery in the next step. Note that this column does not have an alias, so it will be called ?column? in your results.

```sql
-- Select the average of home + away goals, multiplied by 3
SELECT 
	3 * AVG(home_goal + away_goal)
FROM matches_2013_2014;
```

- Select the date, home goals, and away goals in the main query.
- Filter the main query for matches where the total goals scored exceed the value in the subquery.

```sql
SELECT 
	-- Select the date, home goals, and away goals scored
    date,
	home_goal,
	away_goal
FROM  matches_2013_2014
-- Filter for matches where total goals exceeds 3x the average
WHERE (home_goal + away_goal) > 
       (SELECT 3 * AVG(home_goal + away_goal)
        FROM matches_2013_2014); 
```

**</> Filtering using a subquery with a list**

Your goal in this exercise is to generate a list of teams that never played a game in their home city. Using a subquery, you will generate a list of unique hometeam_ID values from the unfiltered match table to exclude in the team table's team_api_ID column.

```sql
SELECT 
	-- Select the team long and short names
	team_long_name,
	team_short_name
FROM team 
-- Exclude all values from the subquery
WHERE team_api_id NOT IN
     (SELECT DISTINCT hometeam_id  FROM match);
```

**</> Filtering with more complex subquery conditions**

Let's do some further exploration in this database by creating a list of teams that scored 8 or more goals in a home match.

In order to do this, you will construct a subquery in the WHERE statement with its own filtering condition.

- Create a subquery in WHERE clause that retrieves all hometeam_ID values from match with a home_goal score greater than or equal to 8.
- Select the team_long_name and team_short_name from the team table. Include all values from the subquery in the main query.

```sql
SELECT
	-- Select the team long and short names
	team_long_name,
	team_short_name
FROM team
-- Filter for teams with 8 or more home goals
WHERE team_api_id IN
	  (SELECT hometeam_id 
       FROM match
       WHERE home_goal >= 8);
```

**</> Joining Subqueries in FROM**

Your goal in this exercise is to generate a subquery using the match table, and then join that subquery to the country table to calculate information about matches with 10 or more goals in total!

- Create the subquery to be used in the next step, which selects the country ID and match ID (id) from the match table.
- Filter the query for matches with greater than or equal to 10 goals.

```sql
SELECT 
	-- Select the country ID and match ID
	country_id, 
    id 
FROM match
-- Filter for matches with 10 or more goals in total
WHERE (home_goal + away_goal) >= 10;
```

- Construct a subquery that selects only matches with 10 or more total goals.
- Inner join the subquery onto country in the main query.
- Select name from country and count the id column from match.

```sql
SELECT
	-- Select country name and the count match IDs
    c.name AS country_name,
    COUNT(c.id) AS matches
FROM country AS c
-- Inner join the subquery onto country
-- Select the country id and match id columns
INNER JOIN (SELECT country_id, id 
           FROM match
           -- Filter the subquery by matches with 10+ goals
           WHERE (home_goal + away_goal) >=10) AS sub
ON c.id = sub.country_id
GROUP BY country_name;
```

**</> Building on Subqueries in FROM**

Let's find out some more details about those matches -- when they were played, during which seasons, and how many of the goals were home vs. away goals.

- Complete the subquery inside the FROM clause. Select the country name from the country table, along with the date, the home goal, the away goal, and the total goals columns from the match table.
- Create a column in the subquery that adds home and away goals, called total_goals. This will be used to filter the main query.
- Select the country, date, home goals, and away goals in the main query.
- Filter the main query for games with 10 or more total goals.

```sql
SELECT
	-- Select country, date, home, and away goals from the subquery
    country,
    date,
    home_goal,
    away_goal
FROM 
	-- Select country name, date, home_goal, away_goal, and total goals in the subquery
	(SELECT c.name AS country, 
     	    m.date, 
     		m.home_goal, 
     		m.away_goal,
           (m.home_goal + m.away_goal) AS total_goals
    FROM match AS m
    LEFT JOIN country AS c
    ON m.country_id = c.id) AS subq
-- Filter by total goals scored in the main query
WHERE total_goals >= 10;
```

**</> Add a subquery to the SELECT clause**

In the following exercise, you will construct a query that calculates the average number of goals per match in each country's league.

- In the subquery, select the average total goals by adding home_goal and away_goal.
- Filter the results so that only the average of goals in the 2013/2014 season is calculated.
- In the main query, select the average total goals by adding home_goal and away_goal. This calculates the average goals for each league.
- Filter the results in the main query the same way you filtered the subquery. Group the query by the league name.

```sql
SELECT 
	l.name AS league,
    -- Select and round the league's total goals
    ROUND(AVG(m.home_goal + m.away_goal), 2) AS avg_goals,
    -- Select & round the average total goals for the season
    (SELECT ROUND(AVG(home_goal + away_goal), 2) 
     FROM match
     WHERE season = '2013/2014') AS overall_avg
FROM league AS l
LEFT JOIN match AS m
ON l.country_id = m.country_id
-- Filter for the 2013/2014 season
WHERE season = '2013/2014'
GROUP BY league;
```
Which leagues tend to score higher than average? Switzerland Super League & Netherlands Eredivisie.

**</> Subqueries in Select for Calculations**

In the previous exercise, you created a column to compare each league's average total goals to the overall average goals in the 2013/2014 season. In this exercise, you will add a column that directly compares these values by subtracting the overall average from the subquery.

- Select the average goals scored in a match for each league in the main query.
- Select the average goals scored in a match overall for the 2013/2014 season in the subquery.
- Subtract the subquery from the average number of goals calculated for each league.
- Filter the main query so that only games from the 2013/2014 season are included.

```sql
SELECT
	-- Select the league name and average goals scored
	l.name AS league,
	ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
    -- Subtract the overall average from the league average
	ROUND(AVG(m.home_goal + m.away_goal) - 
		(SELECT AVG(home_goal + away_goal)
		 FROM match 
         WHERE season = '2013/2014'),2) AS diff
FROM league AS l
LEFT JOIN match AS m
ON l.country_id = m.country_id
-- Only include 2013/2014 results
WHERE season = '2013/2014'
GROUP BY l.name;
```

**</> ALL the subqueries EVERYWHERE**

In this lesson, you will build a final query across 3 exercises that will contain three subqueries -- one in the SELECT clause, one in the FROM clause, and one in the WHERE clause. In the final exercise, your query will extract data examining the average goals scored in each stage of a match. Does the average number of goals scored change as the stakes get higher from one stage to the next?

- Extract the average number of home and away team goals in two SELECT subqueries.
- Calculate the average home and away goals for the specific stage in the main query.
- Filter both subqueries and the main query so that only data from the 2012/2013 season is included.
- Group the query by the m.stage column.

```sql
SELECT 
	-- Select the stage and average goals for each stage
	m.stage,
    ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
    -- Select the average overall goals for the 2012/2013 season
    ROUND((SELECT AVG(home_goal + away_goal) 
           FROM match 
           WHERE season = '2012/2013'),2) AS overall
FROM match AS m
-- Filter for the 2012/2013 season
WHERE season = '2012/2013'
-- Group by stage
GROUP BY m.stage;
```

**</> Add a subquery in FROM**

In this next step, you will turn the main query into a subquery to extract a list of stages where the average home goals in a stage is higher than the overall average for home goals in a match.

- Calculate the average home goals and average away goals from the match table for each stage in the FROM clause subquery.
- Add a subquery to the WHERE clause that calculates the overall average home goals.
- Filter the main query for stages where the average home goals is higher than the overall average.
- Select the stage and avg_goals columns from the s subquery into the main query.

```sql
SELECT 
	-- Select the stage and average goals from the subquery
	stage,
	ROUND(s.avg_goals,2) AS avg_goals
FROM 
	-- Select the stage and average goals in 2012/2013
	(SELECT
		 stage,
         AVG(home_goal + away_goal) AS avg_goals
	 FROM match
	 WHERE season = '2012/2013'
	 GROUP BY stage) AS s
WHERE 
	-- Filter the main query using the subquery
	s.avg_goals > (SELECT AVG(home_goal + away_goal) 
                    FROM match WHERE season = '2012/2013');
```

**</> Add a subquery in SELECT**

In this final step, you will add a subquery in SELECT to compare the average number of goals scored in each stage to the total.

- Create a subquery in SELECT that yields the average goals scored in the 2012/2013 season. Name the new column overall_avg.
- Create a subquery in FROM that calculates the average goals scored in each stage during the 2012/2013 season.
- Filter the main query for stages where the average goals exceeds the overall average in 2012/2013.

```sql
SELECT 
	-- Select the stage and average goals from s
	stage,
    ROUND(s.avg_goals,2) AS avg_goal,
    -- Select the overall average for 2012/2013
    (SELECT AVG(home_goal + away_goal) FROM match WHERE season = '2012/2013') AS overall_avg
FROM 
	-- Select the stage and average goals in 2012/2013 from match
	(SELECT
		 stage,
         AVG(home_goal + away_goal) AS avg_goals
	 FROM match
	 WHERE season = '2012/2013'
	 GROUP BY stage) AS s
WHERE 
	-- Filter the main query using the subquery
	s.avg_goals > (SELECT AVG(home_goal + away_goal) 
                    FROM match WHERE season = '2012/2013');
```

**</> Basic Correlated Subqueries**

In this exercise, you will practice using correlated subqueries to examine matches with scores that are extreme outliers for each country -- above 3 times the average score!

- Select the country_id, date, home_goal, and away_goal columns in the main query.
- Complete the AVG value in the subquery.
- Complete the subquery column references, so that country_id is matched in the main and subquery.

```sql
SELECT 
	-- Select country ID, date, home, and away goals from match
	main.country_id,
    main.date,
    main.home_goal, 
    main.away_goal
FROM match AS main
WHERE 
	-- Filter the main query by the subquery
	(home_goal + away_goal) > 
        (SELECT AVG((sub.home_goal + sub.away_goal) * 3)
         FROM match AS sub
         -- Join the main query to the subquery in WHERE
         WHERE main.country_id = sub.country_id);
```

**</> Correlated subquery with multiple conditions**

n this exercise, you're going to add an additional column for matching to answer the question -- what was the highest scoring match for each country, in each season?

*Note: this query may take a while to load.

- Select the country_id, date, home_goal, and away_goal columns in the main query.
- Complete the subquery: Select the matches with the highest number of total goals.
- Match the subquery to the main query using country_id and season.
- Fill in the correct logical operator so that total goals equals the max goals recorded in the subquery.

```sql
SELECT 
	-- Select country ID, date, home, and away goals from match
	main.country_id,
    main.date,
    main.home_goal,
    main.away_goal
FROM match AS main
WHERE 
	-- Filter for matches with the highest number of goals scored
	(home_goal + away_goal) = 
        (SELECT MAX(sub.home_goal + sub.away_goal)
         FROM match AS sub
         WHERE main.country_id = sub.country_id
               AND main.season = sub.season);
```

**</> Nested simple subqueries**

In this exercise, you will practice creating a nested subquery to examine the highest total number of goals in each season, overall, and during July across all seasons.

- Complete the main query to select the season and the max total goals in a match for each season. Name this max_goals.
- Complete the first simple subquery to select the max total goals in a match across all seasons. Name this overall_max_goals.
- Complete the nested subquery to select the maximum total goals in a match played in July across all seasons.
- Select the maximum total goals in the outer subquery. Name this entire subquery july_max_goals.

```sql
SELECT
	-- Select the season and max goals scored in a match
	season,
    MAX(home_goal + away_goal) AS max_goals,
    -- Select the overall max goals scored in a match
   (SELECT MAX(home_goal + away_goal) FROM match) AS overall_max_goals,
   -- Select the max number of goals scored in any match in July
   (SELECT MAX(home_goal + away_goal) 
    FROM match
    WHERE id IN (
          SELECT id FROM match WHERE EXTRACT(MONTH FROM date) = 07)) AS july_max_goals
FROM match
GROUP BY season;
```
