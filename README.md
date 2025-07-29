# 📊 HR Analytics Dashboard in Power BI

[![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-blue?logo=microsoftpowerbi&logoColor=white)]
[![DAX](https://img.shields.io/badge/DAX-Data%20Modeling-yellow)]
[![Power Query](https://img.shields.io/badge/Power%20Query-ETL-purple?logo=microsoftpowerquery&logoColor=white)]
[![Python](https://img.shields.io/badge/Python-Data%20Analysis-3776AB?logo=python&logoColor=white)]
[![SQL](https://img.shields.io/badge/SQL-Querying-005CBA?logo=mysql&logoColor=white)]
[![RLS](https://img.shields.io/badge/RLS-Row%20Level%20Security-red?logo=powerbi&logoColor=white)]
[![MIT License](https://img.shields.io/badge/License-MIT-green)]

---

## 🎯 Goal

Centralize and visualize HR data in an interactive Power BI dashboard, enabling HR leaders to make proactive, data-driven decisions on talent management, performance optimization, and retention strategies.

---

## 🧩 Objectives

- Consolidate employee demographics, performance ratings, and attrition records into a unified model.  
- Implement dynamic time-intelligence for hire, review, and termination dates.  
- Apply Row-Level Security (RLS) to filter data by department and role.  
- Surface key metrics—headcount trends, satisfaction scores, attrition rates—in intuitive visuals.  
- Enable drill-throughs and slicers for granular department- and time-based insights.

---

## ❓ Problem Statement

HR teams often struggle with fragmented data sources and static reports that lack real-time interactivity. Common pain points include:

- Inability to track hiring and termination trends over time.  
- Difficulty comparing employee satisfaction and performance across departments.  
- Lack of early warning indicators for rising attrition.  
- Limited self-service analytics for HR managers and executives.

This leads to reactive decision-making and missed opportunities to intervene before critical talent gaps emerge.

---

## 🛠️ Approach

### 1. Data Ingestion & Preparation

- Source tables:  
  - **Employee** (EmployeeID, HireDate, TerminationDate, Department, Education, Salary)  
  - **PerformanceRating** (EmployeeID, ReviewDate, EnvironmentScore, JobScore, RelationshipScore, WorkLifeScore, SelfScore, ManagerScore)  
- Power Query transformations:  
  - Standardize date formats and remove duplicates  
  - Derive `Age` and `Tenure` columns  
  - Create a calendar table (`DimDate`) with year/quarter/month hierarchies  

### 2. Data Modeling & Relationships

- Star schema with two fact tables and four dimensions:  
  - Fact: `Employee`, `PerformanceRating`  
  - Dimensions: `DimDate`, `EducationLevel`, `RatingLevel`, `SatisfiedLevel`  

| From                        | To                          | Type         | Active | Notes                                            |
|-----------------------------|-----------------------------|--------------|--------|--------------------------------------------------|
| Employee[Education]         | EducationLevel[EducationLevelID] | Many→One   | ✔      | Maps education lookup                            |
| Employee[HireDate]          | DimDate[Date]               | Many→One     | ✖      | Activated via `USERELATIONSHIP()` for hire date  |
| Employee[TerminationDate]   | DimDate[Date]               | Many→One     | ✖      | Activated via `USERELATIONSHIP()` for term date  |
| PerformanceRating[EmployeeID]   | Employee[EmployeeID]      | Many→One     | ✔      | Links each review to employee record             |
| PerformanceRating[ReviewDate]   | DimDate[Date]            | Many→One     | ✔      | Drives time-intel on performance                 |
| PerformanceRating[EnvironmentSatisfaction] | SatisfiedLevel[SatisfactionID] | Many→One | ✔ | Environment satisfaction lookup                |
| PerformanceRating[JobSatisfaction]         | SatisfiedLevel[SatisfactionID] | Many→One | ✔ | Job satisfaction lookup                        |
| PerformanceRating[RelationshipSatisfaction]| SatisfiedLevel[SatisfactionID] | Many→One | ✔ | Relationship satisfaction lookup               |
| PerformanceRating[WorkLifeBalance]         | SatisfiedLevel[SatisfactionID] | Many→One | ✔ | Work–life balance lookup                       |
| PerformanceRating[SelfRating]              | RatingLevel[RatingID]      | Many→One     | ✔      | Self-assessment rating lookup                    |
| PerformanceRating[ManagerRating]           | RatingLevel[RatingID]      | Many→One     | ✔      | Manager’s rating lookup                          |

### 3. DAX & RLS Implementation

- **Dynamic hire-date context**  
  ```DAX
  Hires_by_Month =
    CALCULATE(
      COUNTROWS(Employee),
      USERELATIONSHIP(Employee[HireDate], DimDate[Date])
    )
-  **Attrition Rate by Date DAX:**
  DAX
AttritionRateDate = 
CALCULATE(
    [%AttritionRate],
    USERELATIONSHIP(Employee[TerminationDate], DimDate[Date])
  )

---

-🔐 **Row-Level Security DAX**
 
[DeptFilter] = 
LOOKUPVALUE(
    Users[Department],
    Users[UPN],
    USERPRINCIPALNAME()
)
## 🔍 Key Insights

- 60% of the workforce is aged 25–35, with peak hiring in Q1.  
- Departments A & B show high environment and job satisfaction (>4.0/5).  
- Performance ratings plateau after two years of tenure—indicating need for upskilling.  
- Voluntary attrition spikes in Q2, correlated with low relationship satisfaction.

---

## ✅ Solution

An end-to-end Power BI dashboard that delivers:

- **Overview**: Total headcount, average salary, overall attrition rate  
- **Demographics**: Age, education, gender, department breakdowns  
- **Performance Tracker**: Self vs. manager ratings trendlines, satisfaction heatmaps  
- **Attrition Analysis**: Drill-through by department, tenure, and satisfaction

This solution transforms raw HR data into actionable intelligence, allowing HR leadership to:

- 📊 Forecast staffing needs  
- 🚨 Identify at-risk employees  
- 🎯 Implement targeted retention initiatives




