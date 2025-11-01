# 🎬 Netflix Data Cleaning & Analysis using SQL Server & Python

### 📊 Project Overview  
This project demonstrates a complete **ELT (Extract → Load → Transform)** workflow to clean, model, and analyze the **Netflix Movies and TV Shows dataset** using **SQL Server** and **Python**.  
The goal was to ingest raw Netflix data, perform in-database transformations using SQL, and generate business insights through analytical querying.

---

### 🎯 Objectives
- Implement an **ELT pipeline** where data is extracted and loaded into SQL Server, followed by in-database transformations.  
- Clean and structure the dataset to make it analytics-ready.  
- Create **dimension tables** for genre, director, and country to achieve a relational schema.  
- Execute advanced **SQL queries** using CTEs, window functions, and aggregations to extract valuable insights.

---

### 🧩 Dataset
The dataset contains metadata for Netflix Movies and TV Shows such as:  
- `show_id`, `type`, `title`, `director`, `cast`, `country`, `date_added`, `release_year`, `rating`, `duration`, and `listed_in`.  
- Source: [Netflix Movies and TV Shows Dataset on Kaggle](https://www.kaggle.com/datasets/shivamb/netflix-shows)

---

### 🛠️ Tech Stack
- **Programming Language:** Python  
- **Database:** SQL Server  
- **Libraries:** Pandas, PyODBC  
- **Concepts Used:** ELT Workflow, Data Cleaning, Joins, Window Functions, CTEs, Data Modeling, String Manipulation  

---

### ⚙️ ELT Workflow Overview
#### **1️⃣ Extract**
- Used **Python (Pandas + PyODBC)** to read the raw Netflix dataset (`netflix_titles.csv`).  
- Verified schema and handled data type mismatches before loading.

#### **2️⃣ Load**
- Loaded the raw dataset into a SQL Server table `netflix_raw` using the **bulk insert** process.  
- Conducted initial data verification and column mapping.

#### **3️⃣ Transform**
All transformations were done **inside SQL Server** to leverage its computation power.

- **Data Cleaning:**
  - Removed duplicates using `ROW_NUMBER()` in a **CTE**.
  - Standardized `date_added` column using `CAST()` and corrected date formats.
  - Fixed **foreign character encoding** and handled **NULL** values.
  - Replaced missing values in `country` and `duration` using lookup and conditional logic.  

- **Data Modeling:**
  - Created new tables:
    - `netflix` – Cleaned master table.  
    - `netflix_genre` – Split multivalued `listed_in` field using `STRING_SPLIT()`.  
    - `netflix_country` – Country mappings for each title.  
    - `netflix_directors` – Director-level mapping.  

- **Data Transformation:**
  - Built normalized dimension tables for relational querying.
  - Applied **CASE** statements, **string functions**, and **type conversions** to ensure consistency.

---

### 🧮 Analytical SQL Queries
Some of the key SQL analyses performed include:

#### 🎥 1. Directors with both Movies and TV Shows
```sql
SELECT nd.director,
       COUNT(DISTINCT CASE WHEN n.type='Movie' THEN n.show_id END) AS no_of_movies,
       COUNT(DISTINCT CASE WHEN n.type='TV Show' THEN n.show_id END) AS no_of_tvshows
FROM netflix n
INNER JOIN netflix_directors nd ON n.show_id = nd.show_id
GROUP BY nd.director
HAVING COUNT(DISTINCT n.type) > 1;
####🌍 2. Country with Highest Number of Comedy Movies
```sql
