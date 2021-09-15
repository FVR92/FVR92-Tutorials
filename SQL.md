
# FVR92-Tutorials
# MySQL Tutorials for DataScience 

SHOW GLOBAL VARIABLES LIKE 'local_infile';
SELECT * FROM sakila.country_population;
SET GLOBAL local_infile = true;
LOAD DATA LOCAL INFILE "C:/Users/fvail/Downloads/Data for mysql/projected-population-by-country.csv"
INTO TABLE sakila.country_population
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(Entity, Code, Year, Population)


SELECT * 
FROM sakila.country_population
WHERE Year >= 1990 AND Year <=2016

CREATE TABLE sakila.`LeftJoin1`
SELECT sakila.`total-cancer-deaths-by-type`.*, sakila.`population`.Density
FROM sakila.`total-cancer-deaths-by-type`
LEFT JOIN sakila.`population`
ON sakila.`total-cancer-deaths-by-type`.Entity = `population`.Entity AND sakila.`total-cancer-deaths-by-type`.Year = `population`.Year

SELECT `Entity`,`Year`, `Deaths - Liver cancer`, `Density`,
SUM(`Deaths - Liver cancer`) OVER (PARTITION BY`Entity` ORDER BY `Year`) AS `Cumulative_Death_Count`
FROM sakila.`LeftJoin1`
WHERE `Entity`= "Italy" 
ORDER BY `Year` DESC

CREATE TABLE sakila.`Cancer_Full`
SELECT sakila.`leftjoin1`.*, sakila.`country_population`.Population
FROM sakila.`leftjoin1`
LEFT JOIN sakila.`country_population`
ON sakila.`leftjoin1`.Entity = sakila.`country_population`.Entity AND sakila.`leftjoin1`.Year = sakila.`country_population`.Year 


SELECT `Entity`,`Year`, `Deaths - Liver cancer`, `Density`, `Population`, MAX(CAST(`Deaths - Liver cancer` AS SIGNED)) AS `TotalDeathCount` ,
(`Deaths - Liver cancer`/`Population`)*100 AS `Death_liver_Percentage`,
SUM(`Deaths - Liver cancer`) OVER (PARTITION BY`Entity` ORDER BY `Year`) AS `Cumulative_Death_Count`
FROM sakila.`Cancer_Full`
WHERE `Entity`= "Italy"
GROUP BY `Entity`
ORDER BY `Death_Liver_Percentage` DESC



SELECT `Entity`,`Deaths - Liver cancer`
FROM sakila.`Cancer_Full`
WHERE `Entity` <> "World" AND `Year`= 2016
GROUP BY `Entity`
ORDER BY  `Deaths - Liver cancer` DESC

SELECT `Entity`,`Year`, `Population`, 
MAX(`Deaths - Liver cancer`) AS `HighestLiverDeathCount`, 
MAX((`Deaths - Liver cancer`/ `Population`))*100 AS `PercentPopulationDeathLiverCancer` 
FROM sakila.`Cancer_Full`
GROUP BY `Entity`, `Population`
ORDER BY `PercentPopulationDeathLiverCancer` DESC

SELECT `Entity`, 
MAX(CAST(`Deaths - Liver cancer` AS SIGNED)) AS `TotalDeathCount`
FROM sakila.`Cancer_Full`
WHERE `Year`=2016 AND `Entity` <> "World"
GROUP BY `Entity`
ORDER BY `TotalDeathCount` DESC
