# beta-tester
WITH seven_day_volume AS

(
    SELECT  project AS "Project",                        
            SUM(usd_amount) as usd_volume                                                                             
    FROM dex."trades" t                                                                             
    WHERE block_time > now() - interval '7 days'
    AND category = 'DEX'
    GROUP BY 1

), one_day_volume AS (
    SELECT  project AS "Project",                        
            SUM(usd_amount) as usd_volume                                                                             
    FROM dex."trades" t                                                                             
    WHERE block_time > now() - interval '1 days'
    AND category = 'DEX'
    GROUP BY 1
)

SELECT  ROW_NUMBER () OVER (ORDER BY SUM(seven.usd_volume) DESC) AS "Rank",
        seven."Project",
        SUM(seven.usd_volume) AS "7 Days Volume",
        SUM(one.usd_volume) AS "24 Hours Volume"
FROM  seven_day_volume seven
LEFT JOIN one_day_volume one ON seven."Project" = one."Project"
WHERE seven.usd_volume IS NOT NULL
GROUP BY 2
ORDER BY 3 DESC;
