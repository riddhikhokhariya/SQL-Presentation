# SQL-Presentation

select count(*) from users u join groups g on g.uid=u.id
where u.country='CAN' AND g.group='A'

SELECT COUNT(DISTINCT uid)AS total_converted_user,
(SELECT COUNT(DISTINCT id) FROM users) As total_users,
COUNT(DISTINCT uid)*100.0 /(SELECT COUNT(DISTINCT id) FROM users) As conversionrate
FROM activity;


SELECT COUNT(DISTINCT uid) FROM groups
WHERE join_dt <= '2023-02-01'


SELECT g.group AS g_type,
CAST(COALESCE (SUM (a.spent), 0) /COUNT (DISTINCT u.id) AS NUMERIC(10,3))
FROM users AS u
INNER JOIN groups AS g
ON g.uid = u.id
LEFT JOIN activity AS a
ON a.uid = g.uid
GROUP BY g_type;

WITH AVG_SPENT_PER_USER AS
(
    SELECT u.id as USER_ID,g.group AS USER_GROUP,
CAST(COALESCE (SUM (a.spent), 0) /COUNT (DISTINCT u.id) AS NUMERIC(10,3)) AVG_SPENT
FROM users AS u
INNER JOIN groups AS g
ON g.uid = u.id
LEFT JOIN activity AS a
ON a.uid = g.uid
GROUP BY 1,2
)
 
SELECT
USER_GROUP,
AVG(AVG_SPENT) - (1.96 * STDDEV(AVG_SPENT) / SQRT(COUNT(DISTINCT USER_ID))) AS lower_bound,
       AVG(AVG_SPENT) + (1.96 * STDDEV(AVG_SPENT) / SQRT(COUNT(DISTINCT USER_ID))) AS upper_bound
from AVG_SPENT_PER_USER
GROUP BY USER_GROUP

