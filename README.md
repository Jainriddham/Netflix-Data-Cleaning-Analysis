# 🎬 Netflix Data Cleaning & Analysis using SQL Server & Python

## 📊 Project Overview

This project demonstrates a complete **ELT (Extract → Load → Transform)** workflow to clean, model, and analyze the **Netflix Movies and TV Shows dataset** using **SQL Server** and **Python**.
The goal was to ingest raw Netflix data, perform in-database transformations using SQL, and generate business insights through analytical querying.

---

## 🎯 Objectives

* Implement an **ELT pipeline** where data is extracted and loaded into SQL Server, followed by in-database transformations.
* Clean and structure the dataset to make it analytics-ready.
* Create **dimension tables** for genre, director, and country to achieve a relational schema.
* Execute advanced **SQL queries** using CTEs, window functions, and aggregations to extract valuable insights.

---

## 🧩 Dataset

The dataset contains metadata for Netflix Movies and TV Shows such as:

* `show_id`, `type`, `title`, `director`, `cast`, `country`, `date_added`, `release_year`, `rating`, `duration`, and `listed_in`.
* Source: [Netflix Movies and TV Shows Dataset on Kaggle](https://www.kaggle.com/datasets/shivamb/netflix-shows)

---

## 🛠️ Tech Stack

* **Programming Language:** Python
* **Database:** SQL Server
* **Libraries:** Pandas, PyODBC
* **Concepts Used:** ELT Workflow, Data Cleaning, Joins, Window Functions, CTEs, Data Modeling, String Manipulation

---

## ⚙️ ELT Workflow Overview

### 1️⃣ Extract

* Used **Python (Pandas + PyODBC)** to read the raw Netflix dataset (`netflix_titles.csv`).
* Verified schema and handled data type mismatches before loading.

### 2️⃣ Load

* Loaded the raw dataset into a SQL Server table `netflix_raw` using the **bulk insert** process.
* Conducted initial data verification and column mapping.

### 3️⃣ Transform

All transformations were performed **inside SQL Server** to leverage its computation power.

* **Data Cleaning:**

  * Removed duplicates using `ROW_NUMBER()` in a **CTE**.
  * Standardized `date_added` using `CAST()` and corrected date formats.
  * Fixed **foreign character encoding** and handled **NULL** values.
  * Replaced missing `country` and `duration` values using lookup and conditional logic.

* **Data Modeling:**

  * Created new tables:

    * `netflix` – Cleaned master table
    * `netflix_genre` – Split multivalued `listed_in` field using `STRING_SPLIT()`
    * `netflix_country` – Country mappings
    * `netflix_directors` – Director-level mapping

* **Data Transformation:**

  * Built normalized dimension tables for relational querying.
  * Applied **CASE** statements, **string functions**, and **type conversions** for consistency.

---

## 🧮 Analytical SQL Queries

### 🎥 1. Directors with both Movies and TV Shows

```sql
SELECT nd.director,
       COUNT(DISTINCT CASE WHEN n.type='Movie' THEN n.show_id END) AS no_of_movies,
       COUNT(DISTINCT CASE WHEN n.type='TV Show' THEN n.show_id END) AS no_of_tvshows
FROM netflix n
INNER JOIN netflix_directors nd ON n.show_id = nd.show_id
GROUP BY nd.director
HAVING COUNT(DISTINCT n.type) > 1;
```

### 🌍 2. Country with Highest Number of Comedy Movies

```sql
SELECT TOP 1 nc.country, COUNT(DISTINCT ng.show_id) AS no_of_movies
FROM netflix_genre ng
INNER JOIN netflix_country nc ON ng.show_id = nc.show_id
INNER JOIN netflix n ON ng.show_id = n.show_id
WHERE ng.genre='Comedies' AND n.type='Movie'
GROUP BY nc.country
ORDER BY no_of_movies DESC;
```

### 📅 3. Director with Maximum Movies Added Each Year

```sql
WITH cte AS (
    SELECT nd.director, YEAR(date_added) AS year_added, COUNT(n.show_id) AS total_movies
    FROM netflix n
    INNER JOIN netflix_directors nd ON n.show_id = nd.show_id
    WHERE type = 'Movie'
    GROUP BY nd.director, YEAR(date_added)
),
ranked AS (
    SELECT *, ROW_NUMBER() OVER(PARTITION BY year_added ORDER BY total_movies DESC, director) AS rn
    FROM cte
)
SELECT * FROM ranked WHERE rn = 1;
```

### ⏱️ 4. Average Movie Duration by Genre

```sql
SELECT ng.genre, AVG(CAST(REPLACE(duration,' min','') AS INT)) AS avg_duration
FROM netflix n
INNER JOIN netflix_genre ng ON n.show_id = ng.show_id
WHERE type='Movie'
GROUP BY ng.genre;
```

### 🎭 5. Directors Who Created Both Comedy and Horror Movies

```sql
SELECT nd.director,
       COUNT(DISTINCT CASE WHEN ng.genre='Comedies' THEN n.show_id END) AS no_of_comedy,
       COUNT(DISTINCT CASE WHEN ng.genre='Horror Movies' THEN n.show_id END) AS no_of_horror
FROM netflix n
INNER JOIN netflix_genre ng ON n.show_id = ng.show_id
INNER JOIN netflix_directors nd ON n.show_id = nd.show_id
WHERE type='Movie' AND ng.genre IN ('Comedies','Horror Movies')
GROUP BY nd.director
HAVING COUNT(DISTINCT ng.genre)=2;
```

---

## 📈 Key Insights

* The **United States** produces the highest number of **Comedy Movies** on Netflix.
* Directors like **Steve Brill** work across multiple genres.
* Year-wise trends reveal top-performing directors for new releases.
* The **average movie duration** across genres ranges between **90–110 minutes**.

---

## 💡 Key Learnings

* Practical experience implementing a real-world **ELT pipeline** in SQL Server.
* Applied **CTEs**, **window functions**, and **string operations** for transformation.
* Strengthened understanding of **data modeling** and **relational structuring**.
* Enhanced analytical reasoning through **SQL-based insights**.

---

## 📁 Folder Structure

```
Netflix-Data-Analysis/
│
├── data/
│   └── netflix_titles.csv
│
├── sql/
│   ├── data_cleaning.sql
│   ├── transformations.sql
│   └── analysis_queries.sql
│
├── notebooks/
│   └── data_loading_python.ipynb
│
├── README.md
└── requirements.txt
```

---

## 🚀 Future Enhancements

* Automate the ELT pipeline using **Airflow** or **SSIS**.
* Integrate with **Power BI** for visual analytics dashboards.
* Extend analysis with **genre-wise viewership trends**.

---

## 🔗 Repository Link

**GitHub:** [https://github.com/Jainriddham/Netflix-Data-Analysis](https://github.com/Jainriddham/Netflix-Data-Cleaning-Analysis/blob/main/README.md)

---

## 👨‍💻 Author

**Riddham Jain**
📍 Data Analyst | B.Tech – Mechanical Engineering, MIT Manipal
📫 [LinkedIn](https://www.linkedin.com/in/ridjain29/) | [Portfolio](https://codebasics.io/portfolio/Riddham-Jain)

---


Would you like me to generate a **short description + pinned repo tags (topics)** next (e.g., `#SQL #ELT #DataCleaning #PowerBI`), to boost GitHub discoverability?
