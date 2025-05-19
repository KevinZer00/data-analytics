# data-analytics
Data Analytics

# Gym Membership Churn Analysis

This project analyzes gym member behavior and churn using synthetic data. The goal is to identify potential trends or patterns that may help explain why some members leave while others stay active.

The data was imported into PostgreSQL and explored using SQL. The analysis focuses on engagement, visit frequency, class attendance, membership type, and satisfaction scores.

## Data Overview

Three datasets were used in this project:

### 1. members.csv

This file contains static information about each member.

| Column              | Description                                    |
|---------------------|------------------------------------------------|
| member_id           | Unique identifier for each member              |
| age                 | Age of the member                              |
| gender              | Male or female                                 |
| membership_type     | Monthly, annual, or quarterly subscription     |
| join_date           | Date the member joined                         |
| satisfaction_rating | Satisfaction score (1 to 5 scale)              |
| churn               | Yes or no, indicating if they left             |

### 2. visits.csv

This file contains individual visit logs.

| Column            | Description                            |
|-------------------|----------------------------------------|
| member_id         | Linked to members.csv                  |
| visit_date        | Date of the visit                      |
| duration_minutes  | Workout duration in minutes            |
| class_attended    | Name of class attended, if any         |

### 3. engagement.csv

This file contains weekly engagement metrics.

| Column         | Description                                 |
|----------------|---------------------------------------------|
| member_id      | Linked to members.csv                       |
| week_start     | Start date of the week                      |
| app_logins     | Number of app logins that week              |
| pt_sessions    | Number of personal training sessions        |
| class_count    | Number of group fitness classes attended    |

---

### Total number of members that churned and churn rate per membership type (monthly, quarterly, annually):

```sql
SELECT 
 membership_type, COUNT(*) AS total_members, 
 SUM(CASE WHEN churn = 'Yes' THEN 1 ELSE 0 END) AS churned_members,
 ROUND(SUM(CASE WHEN churn = 'Yes' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churned_rate
FROM members
GROUP BY membership_type
```
### Output: 

| membership_type | total_members | churned_members | churned_rate |
|-----------------|---------------|-----------------|--------------|
| Monthly         | 2505          | 1210            | 48.30        |
| Quarterly       | 1010          | 321             | 31.78        |
| Annual          | 1500          | 267             | 17.80        |

### Analysis:
The majority of the customers are monthly subscribers. This is also the group that yields the highest churn rate, with quarterly and annual as second and third, respectively. This may point to the idea that monthly subscribers have more freedom with their subscription and therefore more likely to cancel, while quarterly and annual subscribers are locked to larger time intervals. 

---

### Average engagement of churned vs retained members:

```sql
SELECT
 churn,
 ROUND(AVG(app_logins), 2) AS avg_app_logins,
 ROUND(AVG(pt_sessions), 2) AS avg_pt_sessions,
 ROUND(AVG(class_count), 2) AS avg_class_count
FROM members
JOIN engagement ON members.member_id = engagement.member_id
GROUP BY churn
```
### Output:
| churn | avg_app_logins | avg_pt_sessions | avg_class_count |
|-------|----------------|-----------------|-----------------|
| No    | 3.01           | 0.20            | 1.50            |
| Yes   | 3.01           | 0.20            | 1.51            |

### Analysis:
The average engagement metrics (app logins, personal training sessions, and group classes) are similar between churned and retained members. This may suggest that members' engagement may not be the best predictor of churn rates. 

---





