# Covid_19_Project

--Checking for Total of Cases
SELECT date,SUM(new_cases)Total_Cases_Per_Day 
FROM my-data-353523.CovidAnalysis.covid_deaths
WHERE Continent is not Null
GROUP BY date
ORDER BY date;


--Checking for Total of Deaths
SELECT location,SUM(new_cases)Total_Cases_Per_Day 
FROM my-data-353523.CovidAnalysis.covid_deaths
WHERE Continent is not Null
GROUP BY location
ORDER BY location;


--Checking for Death Percentage Across Different Countries
SELECT location,SUM(Cast( new_cases as Int))Total_Cases_Per_Day,SUM(Cast(new_deaths AS Int )) AS Total_Deaths_Per_Day , ((SUM(new_deaths)/SUM(new_cases))*100) AS Percentage_Covid_Death
FROM my-data-353523.CovidAnalysis.covid_deaths
WHERE Continent IS NOT Null AND new_cases !=0 AND new_deaths !=0
GROUP BY location
ORDER BY location;


--Checking for Death Percentage Across in the world across Different Days
SELECT date, SUM(Cast( new_cases as Int))Total_Cases_Per_Day,SUM(Cast(new_deaths AS Int )) AS Total_Deaths_Per_Day , SUM(new_deaths)/SUM(new_cases)*100 AS Percentage_Covid_Death
FROM my-data-353523.CovidAnalysis.covid_deaths
WHERE Continent IS NOT Null --AND new_cases !=0 AND new_deaths !=0
GROUP BY date
ORDER BY date;


--Checking for Death Percentage Across in the world
SELECT SUM(Cast( new_cases as Int))Total_Cases_Per_Day,SUM(Cast(new_deaths AS Int )) AS Total_Deaths_Per_Day , SUM(new_deaths)/SUM(new_cases)*100 AS Percentage_Covid_Death
FROM my-data-353523.CovidAnalysis.covid_deaths
WHERE Continent IS NOT Null AND new_cases !=0 AND new_deaths !=0;


--Checking for Cases Against Population--
SELECT continent,location,population,MAX(Cast(total_cases AS Int )) AS Total_Cases, MAX(Cast(total_cases AS Int ))/population*100 AS Percentage_Covid_Death
FROM my-data-353523.CovidAnalysis.covid_deaths
WHERE Continent IS NOT Null AND new_cases !=0 AND new_deaths !=0
GROUP BY continent,location,population
ORDER BY continent,location;


--Checking for Covid Vaccination
SELECT dea.continent,vac.location,dea.date,vac.new_vaccinations, FROM my-data-353523.CovidAnalysis.covid_deaths dea
JOIN my-data-353523.CovidAnalysis.covid_data vac
ON dea.location = vac.location
AND dea.date = vac.date
Where dea.Continent is not Null
ORDER BY 2,3;


--Checking for Covid Total new Vaccination count per country
SELECT dea.continent,vac.location,dea.date,vac.new_vaccinations,
SUM(vac.new_vaccinations) OVER (PARTITION by vac.location ORDER BY vac.location,dea.date) AS Vaccination_Rolling_Count
FROM my-data-353523.CovidAnalysis.covid_deaths dea
JOIN my-data-353523.CovidAnalysis.covid_data vac
ON dea.location = vac.location
AND dea.date = vac.date
Where dea.Continent is not Null
ORDER BY 2,3;


--USING COMMON TABLE EXPRESSION TO CREATE PERCENTAGE ROLLING COUNT FOR VACCINATION
WITH PopulationVSVaccination AS
(SELECT dea.continent, vac.location, dea.date,vac.population, vac.new_vaccinations,
    SUM(vac.new_vaccinations) OVER (PARTITION BY vac.location ORDER BY vac.location, dea.date) AS Vaccination_Rolling_Count
  FROM `my-data-353523.CovidAnalysis.covid_deaths` AS dea
  JOIN `my-data-353523.CovidAnalysis.covid_data` AS vac
    ON dea.location = vac.location
    AND dea.date = vac.date
  WHERE dea.Continent IS NOT NULL)

SELECT*, (Vaccination_Rolling_Count/PopulationVSVaccination.population)*100 AS Vaccination_Rolling_Count_Percent
FROM PopulationVSVaccination;



--USING TEMP TABLE
--DROP TABLE IF EXISTS
--CREATE TABLE my-data-353523.CovidAnalysis.PercentpeopleVaccinated
--(continent nvarchar(255),
--location nvarchar(255),
--date date,
--new_vaccinations numeric,
--Vaccination_Rolling_Count numeric)

--INSERT INTO CovidAnalysis.PercentpeopleVaccinated

--(SELECT dea.continent,vac.location,dea.date,vac.new_vaccinations,
--SUM(vac.new_vaccinations) OVER (PARTITION by vac.location ORDER BY vac.location,dea.date) AS --Vaccination_Rolling_Count
--FROM my-data-353523.CovidAnalysis.covid_deaths dea
--JOIN my-data-353523.CovidAnalysis.covid_data vac
--ON dea.location = vac.location
--AND dea.date = vac.date
--Where dea.Continent is not Null)
