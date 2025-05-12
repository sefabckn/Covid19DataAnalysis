# 🦠 COVID-19 Data Exploration with SQL

This project involves deep exploration of global COVID-19 data using **SQL** in Microsoft SQL Server. The goal is to derive insights into the pandemic's impact on different countries and continents by analyzing infection rates, death rates, vaccination progress, and trends over time.

## 📁 Dataset

The data used comes from [Our World in Data](https://ourworldindata.org/covid-deaths), consisting of two key tables:
- `CovidDeaths`
- `CovidVaccinations`

Both were cleaned and imported into a SQL Server database named `PortfolioProject`.

## 🛠️ Skills & Concepts Used

- Joins
- Common Table Expressions (CTEs)
- Temp Tables
- Window Functions
- Aggregate Functions
- Data Type Conversion
- Views Creation
- Basic Data Cleaning & Filtering

## 📊 Project Breakdown

### 1. 📈 Total Cases vs Total Deaths
Insight: Likelihood of dying if infected with COVID-19 in a specific country.

```sql
SELECT location, date, total_cases, total_deaths,
       (total_deaths/total_cases)*100 AS DeathPercentage
FROM PortfolioProject..CovidDeaths
WHERE location LIKE '%states%'
```
### 2. 🧮 Total Cases vs Population
Insight: Shows what percentage of a country's population got infected over time.

```sql
SELECT location, date, population, total_cases,
       (total_cases/population)*100 AS PercentPopulationInfected
FROM PortfolioProject..CovidDeaths
```
### 3. 🌍 Countries with Highest Infection Rates
Insight: Identify countries with the highest infection penetration.
```sql
SELECT location, population, MAX(total_cases) AS HighestInfectionCount,
       MAX((total_cases/population))*100 AS PercentPopulationInfected
FROM PortfolioProject..CovidDeaths
GROUP BY location, population
ORDER BY PercentPopulationInfected DESC
```
### 4. ⚰️ Countries with Highest Death Count
Insight: Which countries experienced the highest death tolls.
```sql
SELECT location, MAX(CAST(total_deaths AS INT)) AS TotalDeathCount
FROM PortfolioProject..CovidDeaths
WHERE continent IS NOT NULL
GROUP BY location
ORDER BY TotalDeathCount DESC
```
### 5. 🌐 Global Trends
Insight: A global overview of the pandemic’s fatality rate.
```sql
SELECT SUM(new_cases) AS total_cases, 
       SUM(CAST(new_deaths AS INT)) AS total_deaths, 
       SUM(CAST(new_deaths AS INT))/SUM(new_cases)*100 AS DeathPercentage
FROM PortfolioProject..CovidDeaths
WHERE continent IS NOT NULL
```
### 6. 💉 Vaccination Analysis
Insight: Rolling number of vaccinated people by country and continent.
```sql
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
       SUM(CONVERT(INT, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.date) AS RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths dea
JOIN PortfolioProject..CovidVaccinations vac
  ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
```
## 🔁 CTEs & Temp Tables

Used CTEs and Temp Tables to simplify partition-based calculations and reuse logic. This improved query readability and performance for complex aggregations.

```sql
WITH PopvsVac AS (...)
SELECT *, (RollingPeopleVaccinated/Population)*100
FROM PopvsVac
```
### 👁️ Creating Views for Visualization
This view was created to easily feed into visualization tools like Tableau or Power BI.

```sql
CREATE VIEW PercentPopulationVaccinated AS
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
       SUM(CONVERT(INT, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.date) AS RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths dea
JOIN PortfolioProject..CovidVaccinations vac
  ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
```

## 📚 What I Learned
 - How to clean and analyze large public datasets with SQL

 - Techniques like Window Functions and CTEs to simplify analytical queries

 - Using Temp Tables for intermediate data transformation

 - Importance of breaking down complex queries for better understanding

 - Gained experience in crafting insights from raw data for real-world storytelling
   
## 📂 Project Structure
```pgsql
📦 Covid19-Data-Exploration
┣ 📁 images/
┃ ┣ 📄 total_cases_vs_deaths.png
┃ ┗ 📄 vaccination_progress.png
┣ 📄 CovidExploration.sql
┗ 📄 README.md
```

## 🚀 Future Work
 - Incorporate more demographic breakdowns (age, gender)

 - Cross-reference with mobility or lockdown data

 - Automate ETL and refresh schedules

## 🔗 Resources

- 📊 **COVID-19 Dataset**  
  [Our World in Data - COVID-19](https://ourworldindata.org/covid-deaths)  
  Source of global COVID-19 case, death, and vaccination data.

- 🗃️ **SQL Server Documentation**  
  [Microsoft SQL Server Docs](https://learn.microsoft.com/en-us/sql/sql-server/)  
  Official documentation for working with SQL Server, including T-SQL syntax and functions.

- 🧪 **SQL Practice & Tutorials**  
  [Mode SQL Tutorial](https://mode.com/sql-tutorial/)  
  Learn SQL from scratch with interactive examples.

- 🔍 **Window Functions Guide**  
  [Mode: SQL Window Functions](https://mode.com/sql-tutorial/sql-window-functions/)  
  A beginner-friendly guide to understanding window functions.

- 📚 **W3Schools SQL Reference**  
  [W3Schools - SQL](https://www.w3schools.com/sql/)  
  Quick SQL reference and syntax lookup.

- 🧰 **Data Cleaning with SQL**  
  [DataCamp - Cleaning Data in SQL](https://www.datacamp.com/courses/data-cleaning-in-sql-server-databases)  
  Learn how to handle messy datasets in SQL.

- 📊 **Data Visualization Tools**  
  - [Tableau Public](https://public.tableau.com/)
  - [Microsoft Power BI](https://powerbi.microsoft.com/)

- 📂 **Git & GitHub Guides**  
  [GitHub Docs - Getting Started](https://docs.github.com/en/get-started)  
  Perfect for uploading your project and README file.

