# HR Analytics
## Project Overview
This Power BI report provides a comprehensive analysis of HR-related metrics to help organizations monitor and optimize their human resource management processes. It aims to uncover insights related to employee demographics, attrition, performance, satisfaction, and organizational health.
## Report Structure
The report consists of multiple pages covering different aspects of HR Analytics, including:

- **Employee Overview:** Snapshot of employee distribution by department, gender, age group, and education level.

- **Attrition Analysis:** Deep dive into employee turnover, highlighting key trends and factors influencing attrition (like job satisfaction, department, role, etc.).

- **Performance Insights:** Analysis of employee performance ratings by department and job role.

- **Employee Satisfaction:** Examination of job satisfaction scores across various demographics.

- **Compensation Analysis:** Salary comparisons across departments, roles, and experience levels.

- **Tenure and Promotion Analysis:** Trends in years at company, years in current role, and promotion rates.
## Data Model Overview
The data model follows a star schema structure to optimize analysis and reporting.

![image](https://github.com/user-attachments/assets/ab5d59e7-2cd0-4ade-9fae-b0777d8a1927)

The HR Analytics data model follows a star schema design with one Fact table and several Dimension tables.

| Table Name | Type | Description |
|:-----------|:-----|:------------|
| FactPerformanceRating | Fact | Stores employee-related data including environment satisfaction, job satisfaction, relationship satisfaction, manager ratings, salary hike percentages, performance reviews, and work-life balance scores. |
| DimEmployee | Dimension | Contains employee demographics and job attributes such as Age, Gender, Department, Business Unit, and Education details. |
| DimEducationLevel | Dimension | Maps EducationLevelID to readable education levels like Bachelor, Master, or PhD. |
| DimRatingLevel | Dimension | Maps RatingID to descriptive performance rating levels such as Excellent, Good, and Average. |
| DimSatisfiedLevel | Dimension | Maps SatisfactionLevelID to job satisfaction levels such as Very Satisfied, Satisfied, Neutral, and Dissatisfied. |
| DimDate | Dimension | A date table that supports time-based analysis including Day, Month, Quarter, and Year hierarchies. |

### ➡️ Relationships Overview
- **FactPerformanceRating** is connected to:
  - **DimEmployee** via `EmployeeID`
  - **DimEducationLevel**, **DimRatingLevel**, and **DimSatisfiedLevel** via respective ID columns
  - **DimDate** via `Date` fields
- All relationships are **one-to-many** from dimension to fact table.
## Key Visuals and Metrics
- Attrition Rate (%)

- Average Monthly Income

- Job Satisfaction Score

- Years at Company vs Attrition

- Employee Count by Department and Gender

- Top Factors Influencing Turnover

- Performance Distribution

Visualizations used include:

- Bar Charts

- Donut Charts

- KPI Cards

- Slicers for dynamic filtering

- Line and Area Charts
## Key Insights
- The attrition rate stands at 16.12%, with the highest attrition seen in the Sales and Research & Development departments.

- Job satisfaction averages 2.72 out of 4, with lower scores noticeably tied to higher attrition rates.

- The average monthly income across employees is $6,502, but there's a significant gap across job roles — Sales Executives and Laboratory Technicians earning less compared to Managers and Research Directors.

- Average tenure is 7.01 years, with employees having more than 5 years of service showing lower attrition.

- The promotion rate over the past 5 years is 8.5%, with promotions being more frequent among employees with a Performance Rating of 4.

- Gender diversity indicates a 61% male and 39% female split, with slightly higher attrition among female employees.

- Age distribution shows a concentration between 30-40 years, with younger employees (20-30 years) showing a higher likelihood of switching jobs.
## Target Audience
- HR Managers

- Business Analysts

- Leadership and Executive Teams

- Talent Acquisition and Retention Teams
## Report Screenshots
![Screenshot (73)](https://github.com/user-attachments/assets/1d7ebd5f-232d-4cf1-824e-46ebdd5cc99f)

![Screenshot (74)](https://github.com/user-attachments/assets/50bf46c9-197a-47d5-981a-9ebd745cffbb)

![Screenshot (75)](https://github.com/user-attachments/assets/7f2db78c-d3dd-4fe0-a7f3-eb34623aa06b)

![Screenshot (76)](https://github.com/user-attachments/assets/abcc6804-c0df-4409-930c-d9db00d40dbe)




  
