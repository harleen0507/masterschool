SQL Query
SELECT
u.id,
u.country,
u.gender,
g.group,
g.device,
COALESCE(SUM(a.spent),0) AS total_spent,
CASE
WHEN SUM(a.spent) > 0 THEN 1
ELSE 0
END AS Converted
FROM groups AS g
JOIN users AS u
ON u.id = g.uid
LEFT JOIN activity AS a
ON g.uid = a.uid
GROUP BY u.id,
u.country,
u.gender,
g.group,
g.device;
Novelty Effect
WITH cte_conversion AS
 (SELECT g.uid, min(dt) AS dt, SUM(COALESCE(spent, 0)) AS spend, 
 CASE WHEN SUM(COALESCE(spent, 0)) > 0 THEN 'converted'
  ELSE 'not_converted' END AS conversion
 FROM groups g
 LEFT JOIN activity a
 ON g.uid = a.uid
 GROUP BY 1)  
SELECT c.uid, join_dt, dt, COALESCE (u.country, 'Unknown') AS country, 
COALESCE (u.gender, 'Unknown') AS gender,
COALESCE (g.device, 'Unknown') AS device, g.group, c.spend, c.conversion
FROM cte_conversion c
LEFT JOIN groups g
ON c.uid = g.uid
LEFT JOIN users u
ON c.uid = u.id
