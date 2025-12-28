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
## Data Preparation and Transformation
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

home_team_data <- epl_data %>%
  select(Season, Date, Team = HomeTeam, Opponent = AwayTeam, 
         Goals = HomeGoals, GoalsConceded = AwayGoals,
         Shots = HomeShots, ShotsOnTarget = HomeShotsOnTarget,
         Corners = HomeCorners, Fouls = HomeFouls,
         YellowCards = HomeYellowCards, RedCards = HomeRedCards,
         ConversionRate = HomeConversionRate,
         ShotAccuracy = HomeShotAccuracy,
         Result = FullTime) %>%
  mutate(Venue = "Home")


away_team_data <- epl_data %>%
  select(Season, Date, Team = AwayTeam, Opponent = HomeTeam,
         Goals = AwayGoals, GoalsConceded = HomeGoals,
         Shots = AwayShots, ShotsOnTarget = AwayShotsOnTarget,
         Corners = AwayCorners, Fouls = AwayFouls,
         YellowCards = AwayYellowCards, RedCards = AwayRedCards,
         ConversionRate = AwayConversionRate,
         ShotAccuracy = AwayShotAccuracy,
         Result = FullTime) %>%
  mutate(Venue = "Away")


# Team performance analysis
team_performance <- bind_rows(home_team_data, away_team_data) %>%
  mutate(
    Points = case_when(
      (Venue == "Home" & Result == "HomeWin") ~ 3,
      (Venue == "Away" & Result == "AwayWin") ~ 3,
      Result == "Draw" ~ 1,
      TRUE ~ 0
    ),
    Win = ifelse(Points == 3, 1, 0),
    Draw = ifelse(Points == 1, 1, 0),
    Loss = ifelse(Points == 0, 1, 0)
  )
```

---
## Key Analyses & Visualizations

### Team Performance
- Top teams ranked by total points (10 seasons combined)

- Goal scored vs conceded analysis

- Goal difference as a performance separator

*![Games points by Team](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/PointsByTeam.PNG)*


*![Goals Analysis](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/GoalsPerformance.PNG)*



*![Goal Dfference](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/GoalsDifference.PNG)*

---
### Match Outcome Distribution
- Home wins, away wins, and draws visualized using donut chart
- Clear evidence of sustained home advantage

*![Match Outcome Distribution](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/DistributionMatchResults.PNG)*

---

### Seasonal Trends
- Points progression for top 5 teams across seasons
- Identifies consistency, dominance, and performance dips.

*![Match Outcome Distribution](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/SeasonTrend.PNG)*

---
### Home vs Away Performance
- Away win rates and away dominance comparison
- Highlights why elite teams succeed beyond home advantage

*![Most Away Win](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/MostAwayWins.PNG)*


*![Least Away Win](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/LeastAwayWins.PNG)*

---
### Referee Analytics
- Referee strictness based on average cards per match
- Matches officiated by referee
- Team-level disciplinary behavior across seasons

*![Referee Average Cards Per Match](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/RefereeCardsPerMatch.PNG)*


*![Referee Matches Officiated--](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/RefereeMatchesOfficiated.PNG)*


*![Team Card Per Season--](https://github.com/Uzo-Hill/EPL-Match-Analytics-2010-2020-Data-Driven-Insights-with-R/blob/main/project%20images/TeamCardsPerSeason.PNG)*

---
## Key Insights
- Home advantage remains significant, accounting for ~46% of match wins compared to just 28.7% away wins.
- Manchester City dominated the decade in points, goals, home and away wins.
- Goal difference strongly separates elite and mid-table teams, goal differences exceeding +300 over the decade for the top 5, with Manchester City leading at +522.
- Referee strictness varies significantly, with some averaging 3.5+ cards per match.

---
## Project Summary
This project demonstrates how data science and R can be applied to football analytics by transforming raw EPL match data into actionable insights. Through structured analysis and clear visualization, the project bridges match statistics and strategic football intelligence.

---
## Author
- Uzoh C. Hillary
- Data Scientist | Sports Analytics Enthusiast
- LinkedIn: [LinkedIn](http://www.linkedin.com/in/hillaryuzoh)















