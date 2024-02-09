 

 --SELECT *
 -- FROM PortfolioProject.dbo.CovidDeaths
 -- WHERE continent IS NOT NULL
 --  ORDER BY 3,4


  --  SELECT *
  --FROM PortfolioProject.dbo.CovidVaccinations
  -- ORDER BY 3,4


  -- SELECT Data that we are going to be using

  SELECT Location, date, total_cases, new_cases, total_deaths, population
   FROM PortfolioProject.dbo.CovidDeaths
     ORDER BY 1,2


-- Total Cases vs Total Deaths

SELECT Location, date, total_cases, total_deaths, (total_deaths/total_cases) * 100 as DeathPercentage
 FROM CovidDeaths
 WHERE Location LIKE '%rom%'
 AND (total_deaths/total_cases) is NOT NULL
  ORDER BY 1,2

  -- ALTER TABLE CovidDeaths
  -- ALTER COLUMN total_deaths float


  -- Total Cases vs Total Population

  SELECT Location, date, Population, total_cases,  CONCAT(ROUND((total_cases/population) * 100, 2), ' ', '%') as TotalCasesPercentage
 FROM CovidDeaths
 WHERE Location LIKE '%rom%'
 AND (total_deaths/total_cases) is NOT NULL
  ORDER BY 1,2


  -- Countries with the highest infection rate compared to Population

  SELECT Location, Population, MAX(total_cases) as HighestInfectionCount,  MAX((total_cases/population)) * 100 as TotalCasesPercentage
 FROM CovidDeaths
 --WHERE date < '2021-04-30' 
 GROUP BY Location, Population
  ORDER BY TotalCasesPercentage DESC


  -- Countries with the highest death rate compared to Population

  SELECT Location, MAX(total_deaths) as TotalDeathCount
 FROM CovidDeaths
 --WHERE date < '2021-04-30'
 WHERE continent IS NOT NULL
 GROUP BY Location
  ORDER BY TotalDeathCount DESC


  -- Breaking values by Continent

  SELECT continent, MAX(total_deaths) as TotalDeathCount
 FROM CovidDeaths
 WHERE date < '2020-04-30'
 AND continent IS NOT NULL
 GROUP BY continent
  ORDER BY TotalDeathCount DESC


  -- Showing Continent with the highest death count per Population

  SELECT TOP (1) continent, MAX(total_deaths) as TotalDeathCount
 FROM CovidDeaths
 WHERE date < '2020-04-30'
 AND continent IS NOT NULL
 GROUP BY continent
  ORDER BY TotalDeathCount DESC


  -- Global numbers 
   
 SELECT SUM(new_cases) as NewCases, SUM(new_deaths) as NewDeaths, CONCAT(ROUND(SUM(new_deaths) / SUM(new_cases) * 100,2), ' ', '%') as DeathPercentage
  FROM CovidDeaths
   --WHERE Location LIKE '%rom%'
   WHERE continent IS NOT NULL
	--GROUP BY date
    ORDER BY 1,2


	-- Record by Month-Year
	
	SELECT CONCAT(MONTH(date), '-', YEAR(date)) as Month_Year,  SUM(new_cases) as NewCases, SUM(new_deaths) as NewDeaths, CONCAT(ROUND(SUM(new_deaths) / SUM(new_cases) * 100,2), ' ', '%') as DeathPercentage
  FROM CovidDeaths
   --WHERE Location LIKE '%rom%'
   WHERE continent IS NOT NULL
	GROUP BY CONCAT(MONTH(date), '-', YEAR(date))
    ORDER BY 1,2


	-- Total Population vs Vaccination

	SELECT CD.continent, CD.location, CD.date, CD.population, CV.new_vaccinations,
	 SUM(cast(CV.new_vaccinations as float)) OVER ( PARTITION BY CD.Location ORDER BY CD.Location, CD.date) as RollingPeopleVaccinated
	 FROM PortfolioProject..CovidDeaths CD
	  JOIN PortfolioProject..CovidVaccinations CV
	   ON CD.location = CV.location
	   AND CD.date = CV.date
	 WHERE CD.continent IS NOT NULL
	 ORDER BY 2,3


	-- CTE example

	WITH PopvsVac ( Continent, Location, Date, Population, New_vaccinations, RollingPeopleVaccinated) 
	as
	(
	SELECT CD.continent, CD.location, CD.date, CD.population, CV.new_vaccinations,
	 SUM(cast(CV.new_vaccinations as float)) OVER ( PARTITION BY CD.Location ORDER BY CD.Location, CD.date) as RollingPeopleVaccinated
	 FROM PortfolioProject..CovidDeaths CD
	  JOIN PortfolioProject..CovidVaccinations CV
	   ON CD.location = CV.location
	   AND CD.date = CV.date
	 WHERE CD.continent IS NOT NULL
	 )
	 SELECT *, (RollingPeopleVaccinated/ Population) * 100
	  FROM PopvsVac
	

	-- TEMPT table

	DROP TABLE IF EXISTS #PercentPopulationVaccinated
	CREATE TABLE #PercentPopulationVaccinated
	(
	Continent nvarchar(255),
	Location nvarchar(255),
	Date Datetime,
	Population numeric,
	New_vaccinations numeric,
	RollingPeoplevaccinated numeric
	)


	INSERT INTO #PercentPopulationVaccinated
	SELECT CD.continent, CD.location, CD.date, CD.population, CV.new_vaccinations,
	 SUM(cast(CV.new_vaccinations as float)) OVER ( PARTITION BY CD.Location ORDER BY CD.Location, CD.date) as RollingPeopleVaccinated
	 FROM PortfolioProject..CovidDeaths CD
	  JOIN PortfolioProject..CovidVaccinations CV
	   ON CD.location = CV.location
	   AND CD.date = CV.date
	 WHERE CD.continent IS NOT NULL
	 SELECT *, (RollingPeopleVaccinated/ Population) * 100
	  FROM #PercentPopulationVaccinated


	  -- Creating view to store data for data visualizations

	  CREATE VIEW PercentPeopleVaccinated as
	  (
	  SELECT CD.continent, CD.location, CD.date, CD.population, CV.new_vaccinations,
	 SUM(CONVERT(float,CV.new_vaccinations)) OVER ( PARTITION BY CD.Location ORDER BY CD.Location, CD.date) as RollingPeopleVaccinated
	 FROM PortfolioProject..CovidDeaths CD
	  JOIN PortfolioProject..CovidVaccinations CV
	   ON CD.location = CV.location
	   AND CD.date = CV.date
	 WHERE CD.continent IS NOT NULL
	 )
	 
	 SELECT *
	  FROM PercentPeopleVaccinated
