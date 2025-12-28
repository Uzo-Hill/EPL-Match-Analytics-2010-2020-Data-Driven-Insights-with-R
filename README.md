# EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R
A data science project in R analyzing 10 seasons of English Premier League match data to explore team performance trends, discipline patterns, and referee tendencies through modern football analytics.

---
## Project Overview
This project analyzes 10 seasons of English Premier League match data (2010/11–2019/20) to uncover patterns in team performance, home vs away team performance, goal efficiency, and referee behavior.

Using R and modern football analytics techniques, raw match-level data was transformed into structured metrics and visual insights that support tactical evaluation, performance benchmarking, and evidence-based football decision-making.

---
## Objectives
- Explore and clean multi-season EPL match data

- Evaluate team performance using points, goals, and consistency metrics

- Quantify home advantage vs away performance

- Analyze referee strictness and disciplinary trends

- Produce publication-quality football analytics visualizations

---
## Dataset
- Source: Kaggle Dataset - 
  English Premier League Data for 10 Seasons
  
  [epl_match_stats](https://www.kaggle.com/datasets/taranguyen/english-premier-league-data-for-10-seasons?resource=download)
  
- Dimensions: 3,800 matches × 23 variables

- Period: 2010/11 – 2019/20

- Variables: Goals, shots, cards, corners, fouls, referee, venue, result, etc.

---
## Tools & Libraries
- Language: R

- Core Packages:

```r
# Core data science
library(tidyverse)      # Data manipulation & visualization
library(lubridate)      # Date handling

# Visualization
library(ggplot2)        # Graphics
library(viridis)        # Color palettes
library(patchwork)      # Combining plots

# Tables & Reporting
library(DT)             # Interactive tables
library(knitr)          # Report generation
```

---
## Data Preparation
Key preprocessing steps included:
- Date parsing and feature extraction (season, month, year)
- Creation of advanced match metrics:
  - Shot accuracy & conversion rates
  - Goal difference & competitiveness index
  - Disciplinary points
- Restructuring data into team-centric home and away datasets

```r
# Convert Date to proper format
epl_data$Date <- as.Date(epl_data$Date)

# Extract additional features
epl_data <- epl_data %>%
  mutate(
    # Extract month and year
    Month = month(Date, label = TRUE),
    Year = year(Date),
    
    # Create match result categories
    Result = factor(FullTime, levels = c("HomeWin", "Draw", "AwayWin")),
    
    # Calculate additional metrics
    HomeConversionRate = ifelse(HomeShots > 0, (HomeGoals / HomeShots) * 100, 0),
    AwayConversionRate = ifelse(AwayShots > 0, (AwayGoals / AwayShots) * 100, 0),
    HomeShotAccuracy = ifelse(HomeShots > 0, (HomeShotsOnTarget / HomeShots) * 100, 0),
    AwayShotAccuracy = ifelse(AwayShots > 0, (AwayShotsOnTarget / AwayShots) * 100, 0),
    
    # Calculate disciplinary metrics
    HomeDisciplinaryPoints = HomeYellowCards + (HomeRedCards * 2),
    AwayDisciplinaryPoints = AwayYellowCards + (AwayRedCards * 2),
    
    # Game intensity metrics
    TotalShots = HomeShots + AwayShots,
    TotalGoals = HomeGoals + AwayGoals,
    TotalCards = HomeYellowCards + AwayYellowCards + HomeRedCards + AwayRedCards,
    
    # Goal difference
    GoalDifference = HomeGoals - AwayGoals,
    
    # Match competitiveness (closer games have lower values)
    CompetitivenessIndex = abs(HomeGoals - AwayGoals)
  )
```

---
## Key Analyses & Visualizations





















