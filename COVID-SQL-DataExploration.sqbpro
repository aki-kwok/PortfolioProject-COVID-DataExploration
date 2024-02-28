--Covid 19 Data Exploration 
--Skills used: Joins, CTE's, Temp Tables, Windows Functions, Aggregate Functions, Creating Views, Converting Data Types

SELECT *
FROM CovidDeaths
--WHERE continent is not NULL
--WHERE location is '%income%'
ORDER BY 3,4;

-- Looking for date of last collected data (2/06/2024)
SELECT *
FROM CovidDeaths
WHERE location like '%state%'
ORDER BY 3,4 DESC;

SELECT *
FROM COVIDVaccinations
WHERE continent is not NULL
ORDER BY 3,4;

-- Select Data that we are going to be starting with
SELECT location, date, total_cases, new_cases, total_deaths, population
FROM CovidDeaths
ORDER BY 1,2;

--Looking at Total Cases vs Total Deaths (%)
-- Shows the likelihood of dying if you contract COVID in your country
SELECT location, 
	date, 
	total_cases, 
	total_deaths,
	(CAST (total_deaths AS FLOAT) / total_cases)*100 AS DeathPercentage
FROM CovidDeaths
WHERE location like '%state%'
ORDER BY 1,2;

--Looking at Total Cases vs Population (%)
--Shows what percentage of the population has gotten COVID
SELECT location, 
	date, 
	total_cases, 
	population,
	(CAST (total_cases AS FLOAT) / population)*100 AS [Percent Population Infected]
FROM CovidDeaths
WHERE location like '%state%'
ORDER BY 1,2;

--Looking at Countries with Highest Infection Rate compared to Population 
SELECT location, 
	population,
	MAX(total_cases) AS [Highest Infection Count],
	MAX (CAST (total_cases AS FLOAT) / population)*100 AS [Percent Population Infected]
FROM CovidDeaths
--WHERE location like '%state%'
GROUP BY Location, Population
ORDER BY [Percent Population Infected] DESC;

-- Showing Countries with the Highest Death Count per Population 
SELECT location, 
continent,
	MAX(CAST (total_deaths AS INT)) AS [Total Death Count]
FROM CovidDeaths
--WHERE location like '%state%'
WHERE continent is NOT NULL -- Removes non-location related groups 
	AND location NOT LIKE '%income' -- Excludes locations including categories 'Low Income', 'Lower middle income', 'Upper middle income', 'High Income'
GROUP BY Location
ORDER BY [Total Death Count] DESC;

-- Showing Continents with the Highest Death Count
SELECT continent, 
	MAX(CAST (total_deaths AS INT)) AS [Total Death Count]
FROM CovidDeaths
--WHERE location like '%state%'
WHERE continent is NOT NULL -- Includes all Country groupings 
GROUP BY continent
ORDER BY [Total Death Count] DESC;

--Global Numbers 
SELECT 
SUM(CAST (new_cases AS FLOAT)) AS [Total Cases],
SUM(CAST(new_deaths AS FLOAT)) AS [Total Deaths],
SUM(CAST(new_deaths AS FLOAT)) / SUM(new_cases) * 100 AS [Death Percentage]
FROM CovidDeaths
--WHERE location like '%state%'
--WHERE continent is NOT NULL
--GROUP BY date
ORDER BY 1,2;

--Joining COVID Deaths and Vaccinations Tables
SELECT * 
FROM COVIDDeaths dea
JOIN COVIDVaccinations  vac 
	ON dea.location = vac.location 
	and dea.date = vac.date;
	
--Looking at Total Population vs Vaccinations
SELECT dea.continent, 
	dea.location, 
	dea.date, 
	dea.population, 
	vac.new_vaccinations,
	SUM(CAST(vac.new_vaccinations AS INT)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS [Rolling Vaccinations (by dose)]-- Getting a rolling count of pvaccinations by country
FROM COVIDDeaths dea
JOIN COVIDVaccinations AS  vac 
	ON dea.location = vac.location 
	and dea.date = vac.date
WHERE dea.continent is NOT NULL
ORDER BY 2 ,3;

-- Use CTE (Common Table Expression)
WITH PopvsVac (continent, location, date, population, new_vaccinations,[Rolling Vaccinations (by dose)])
AS 
(
SELECT dea.continent, 
	dea.location, 
	dea.date, 
	dea.population, 
	vac.new_vaccinations,
	SUM(CAST(vac.new_vaccinations AS FLOAT)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS [Rolling Vaccinations (by dose)]-- Getting a rolling count of people vaccinated by country
FROM COVIDDeaths dea
JOIN COVIDVaccinations AS  vac 
	ON dea.location = vac.location 
	and dea.date = vac.date
WHERE dea.continent is NOT NULL
--ORDER BY 2 ,3
)

SELECT *, ([Rolling Vaccinations (by dose)]/population)  * 100 AS [Rolling Percentage Vaccinations (by dose)]
FROM PopvsVac;


-- Creating View to store data for later visualizations
CREATE VIEW PercentPopulationVaccinated AS
SELECT dea.continent, 
	dea.location, 
	dea.date, 
	dea.population, 
	vac.new_vaccinations,
	SUM(CAST(vac.new_vaccinations AS FLOAT)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS [Rolling Vaccinations (by dose)]-- Getting a rolling count of people vaccinated by country
FROM COVIDDeaths dea
JOIN COVIDVaccinations AS  vac 
	ON dea.location = vac.location 
	and dea.date = vac.date
WHERE dea.continent is NOT NULL
--ORDER BY 2,3;
