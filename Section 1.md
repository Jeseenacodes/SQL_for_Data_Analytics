
Question 1. This is the table: 

| TEAM_1 | TEAM_2 | WINNER |
|--------|--------|--------|
| INDIA  | SL     | INDIA  |
| SL     | AUS    | AUS    |
| SA     | ENG    | ENG    |
| ENG    | NZ     | NZ     |
| AUS    | INDIA  | INDIA  |

Write SQL to create like points table below.

<img width="888" height="265" alt="image" src="https://github.com/user-attachments/assets/e09da63c-edbc-4b83-b0c9-eb93e1657052" />

```sql
# Using Subquery & UnionAll
SELECT Team_name, COUNT(*) AS Number_of_Matches_played,
SUM(wins) AS Number_of_matches_Won,
COUNT(*) - SUM(wins) AS Number_of_matches_lost
FROM 
(
SELECT Team_1 AS Team_name,
CASE WHEN Team_1 = Winner THEN 1 ELSE 0 END AS Wins FROM ICC_WORLD_CUP 
UNION ALL 
SELECT Team_2 AS Team_name,
CASE WHEN Team_2 = Winner THEN 1 ELSE 0 END AS Wins FROM ICC_WORLD_CUP 
) A

GROUP BY Team_name 
ORDER BY Number_of_matches_Won DESC; 
```

```sql
# Using CTEs
# Wins with list of teams & winner
WITH wins AS 
(
SELECT TEAM_1 AS TEAM_NAME, WINNER FROM ICC_WORLD_CUP
UNION ALL 
SELECT TEAM_2 AS TEAM_NAME, WINNER FROM ICC_WORLD_CUP ),

# teams table with matches played & won
teams AS 
(
SELECT TEAM_NAME, COUNT(TEAM_NAME) AS Number_of_Matches_played, 
SUM(CASE WHEN WINNER = TEAM_NAME THEN 1 ELSE 0 END) AS Number_of_Matches_won FROM wins 
GROUP BY TEAM_NAME )

# matches lost & details
SELECT * , (Number_of_Matches_played - Number_of_Matches_won) AS Number_of_Matches_lost FROM teams 
ORDER BY Number_of_Matches_won DESC, Number_of_Matches_lost 
```

Output:
| Team_name | Number_of_Matches_played | Number_of_matches_Won | Number_of_matches_lost |
|-----------|---------------------------|-------------------------|--------------------------|
| INDIA     | 2                         | 2                       | 0                        |
| ENG       | 2                         | 1                       | 1                        |
| AUS       | 2                         | 1                       | 1                        |
| NZ        | 1                         | 1                       | 0                        |
| SL        | 2                         | 0                       | 2                        |
| SA        | 1                         | 0                       | 1                        |




