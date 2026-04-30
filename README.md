# 📚 EdTech Performance & Revenue Analytics

> An end-to-end data analysis project identifying root causes of student drop-offs, low completion rates, and revenue inefficiencies on an EdTech platform.

---

## 🧩 Business Problem

An EdTech company was experiencing high student drop-off rates and low course completion rates, directly impacting revenue and long-term retention.

**Goals:**
- Identify why students drop off before completing courses
- Find high-performing courses and categories by revenue
- Analyse engagement vs. completion patterns
- Develop data-driven recommendations to improve the revenue strategy

---

## 📊 Key Metrics (Dashboard)

| Metric | Value |
|---|---|
| Total Revenue | ₹3,451M |
| Total Students | 30,000 |
| Avg Completion Rate | 49.5% |
| Top Category | AI |
| Top Country | Canada |

---

## 🗂️ Dataset Structure

Five CSV files were used, forming a relational schema:

```
students.csv       → Student demographics, signup dates, age, country
courses.csv        → Course catalog, category, price, duration_weeks
enrollments.csv    → Enrollment records, completion_status, progress_percent
payments.csv       → Transaction records, amount_paid per course enrollment
```

### Entity Relationships
```
students ──< enrollments >── courses
students ──< payments    >── courses
```

---

## 🐍 Python Analysis (Pandas)

**File:** `Ed_tech_project.ipynb`

### Steps Performed

**1. Data Loading**
```python
import pandas as pd
students    = pd.read_csv("students.csv")
payments    = pd.read_csv("payments.csv")
enrollments = pd.read_csv("enrollments.csv")
courses     = pd.read_csv("courses.csv")
```

**2. Data Cleaning**
- `students.age` — null values filled with median
- `students.country` — null values filled with `'unknown'`
- `enrollments.progress_percent` — null values filled with `0`
- Date columns parsed to `datetime`

**3. Feature Engineering**

| Feature | Logic |
|---|---|
| `age_category` | 18–25 / 25–35 / 35–50 based on age |
| `price_per_week` | `price / duration_weeks` |
| `is_completed` | `1` if completion_status == 'Completed' else `0` |
| `revenue` | Aliased from `amount_paid` |

**4. Table Merging**
```python
analysis_df = enrollments
    .merge(students,  on='student_id',  how='left')
    .merge(courses,   on='course_id',   how='left')
    .merge(payments_agg, on=['student_id','course_id'], how='left')
```

**5. Pivot Table Analysis**
- Revenue by category (sorted descending)
- Completion rate by category (mean of `is_completed`)
- Revenue by country (geographic breakdown)

**6. Visualisations**
- Bar chart: Revenue by Category
- Bar chart: Completion Rate by Category

**7. Export to MySQL**
```python
from sqlalchemy import create_engine
engine = create_engine("mysql+pymysql://root:password@localhost:3306/edtech_project")
students.to_sql('students', engine, if_exists='replace', index=False)
courses.to_sql('courses', engine, if_exists='replace', index=False)
enrollments.to_sql('enrollments', engine, if_exists='replace', index=False)
payments.to_sql('payments', engine, if_exists='replace', index=False)
```

---

## 🗄️ SQL Analysis (MySQL)

**File:** `edtech_project.sql`

### Queries Overview

| # | Query | Technique |
|---|---|---|
| 1 | Total revenue by course category | `GROUP BY`, `JOIN`, `ORDER BY` |
| 2 | Countries with revenue > ₹5,000,000 | `HAVING`, aggregation |
| 3 | Courses above overall completion rate | Correlated subquery |
| 4 | Top 5 courses by revenue | CTE + `DENSE_RANK()` |
| 5 | Top 3 courses per category by revenue | CTE + `DENSE_RANK() OVER (PARTITION BY)` |
| 6 | Monthly revenue trend | `DATE_FORMAT`, `GROUP BY` |
| 7 | Category revenue vs. overall average | CTE + `CASE WHEN` |
| 8 | Running total of revenue | `SUM() OVER (ORDER BY)` |
| 9 | Month-over-month revenue difference | `LAG()` window function |
| 10 | Student spending segmentation | CTE + `CASE WHEN` classification |

### Student Spending Segments
```sql
CASE
  WHEN total_spent > 20000  THEN 'High Spender'
  WHEN total_spent BETWEEN 1000 AND 2000 THEN 'Medium Spender'
  ELSE 'Low Spender'
END
```

---

## 📈 Power BI Dashboard

**File:** Dashboard screenshot included in project report.

### Components
- **KPI Cards** — Revenue, Students, Completion Rate, Top Category, Top Country
- **Monthly Revenue Trend** — Line chart (mid-2024 → early 2026)
- **Student Segmentation by Age** — Donut chart (35–50 dominates)
- **Revenue by Category** — Bar chart (AI leads)
- **Best Performing Courses** — Table with revenue + completion rate
- **Filters** — Category slicer (AI / Business / Data Science) + Year slicer (2024 / 2025 / 2026)

---

## 💡 Key Findings

- **AI** is the top-revenue category by a significant margin
- **Canada** is the highest-revenue country
- **49.5%** average completion rate — 1 in 2 students does not finish
- The **35–50 age group** is the largest student segment
- A large proportion of students are **Low Spenders** — a major upsell opportunity
- Some high-revenue courses have **below-average completion rates**, indicating content quality gaps

---

## ✅ Recommendations

| Area | Action |
|---|---|
| Drop-off Reduction | Milestone nudges at 30%, 50%, 70% progress |
| Content Quality | Audit low-completion courses; split long content into modules |
| Revenue Growth | Bundle packages to upsell Low Spenders |
| Retention | Loyalty program for High Spenders |
| Geographic | Localised pricing for low-revenue, high-student countries |
| Age Targeting | Weekend-friendly formats for 35–50 segment |

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Python 3 (Pandas) | Data loading, cleaning, EDA, pivot analysis |
| Matplotlib | visualisations |
| MySQL | Structured querying and business analytics |
| SQLAlchemy | DataFrame-to-MySQL export |
| Power BI | Interactive dashboard |

---

## 📁 Project Structure

```
EdTech_Project/
│
├── Ed_tech_project.ipynb       # Python EDA notebook
├── edtech_project.sql          # MySQL queries
├── students.csv                # Student data
├── courses.csv                 # Course catalog
├── enrollments.csv             # Enrollment & completion records
├── payments.csv                # Payment transactions
└── EdTech_Project_Report.docx  # Full project report
```

---


