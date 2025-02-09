# AWS Databricks Job Description Analysis

## Overview
This project focuses on processing **job description datasets** using **Databricks** on **Amazon Web Services (AWS)**. The analysis includes **data preprocessing, cleaning, and transformation**, along with answering key analytical questions about job descriptions and required skills. The workflow involves setting up a Databricks environment, handling datasets with **PySpark**, and extracting meaningful insights.

---

## Objectives
✅ **Set up Databricks on AWS for data engineering tasks**  
✅ **Preprocess and clean job description data**  
✅ **Identify distinct job descriptions and analyze skill frequencies**  
✅ **Extract top in-demand skills and compare with external datasets (O*NET)**  
✅ **Visualize key insights for decision-making**  

---

## Setup Requirements
### 1. **Databricks Environment Setup**
- Create a **Databricks workspace** in AWS.
- Use **Databricks Community Edition** for notebook and cluster management.
- Set up a **12.2 LTS (Scala 2.12, Spark 3.3.2)** cluster for execution.
- Cluster auto-termination is enabled after 2 hours of inactivity.

### 2. **Notebook Configuration**
- Create a new **Databricks notebook** for scripting.
- Use **Python (PySpark)** for data analysis.
- Import necessary libraries like **PySpark, SparkSQL, Pandas, NumPy**.

---

## Data Preprocessing
### **1. Import Required Libraries**
Before connecting to the dataset, install and import necessary libraries.
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, split, count, explode, lower, array_except
```
![Import SparkSQL](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/c6dc4e4e-549f-441b-8b6a-676d3b31294f)

### **2. Load Datasets**
The project uses two datasets:
- **`job_skill_50k.csv`** – Contains job descriptions with skills.
- **`technology_skill.txt`** – Contains a list of technical skills.

#### **Load CSV and TXT files into Databricks**
```python
job_df = spark.read.format("csv").option("header", "false").load("dbfs:/path/job_skill_50k.csv")
tech_df = spark.read.format("text").option("header", "true").load("dbfs:/path/technology_skill.txt")
```
![Read Job Data](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/6108eef1-9809-4559-86a1-ca14c5ba5d3a)

---

## Data Cleaning & Transformation
### **1. Remove Duplicate Rows**
```python
job_df = job_df.distinct()
```
![Remove Duplicates](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/cbeaa66f-4aee-46e0-bd98-ea50566bba1c)

### **2. Split Skills from Job Descriptions**
```python
job_df = job_df.withColumn("skills", split(col("value"), "\t"))
```
![Split Skills](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/e466f32a-cc01-4f23-805a-cbfa061b2c54)

### **3. Remove 'None' Values**
```python
job_df = job_df.withColumn("skills", array_except(col("skills"), array(lit(None))))
```
![Remove None Values](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/bd333eea-9594-4caf-a400-035180edfb87)

---

## Key Analytical Questions & Insights
### **1. Distinct Number of Job Descriptions**
```python
num_distinct_jobs = job_df.count()
```
🔹 **Result:** 50,000 unique job descriptions.

### **2. Top 10 Most Frequently Mentioned Skills**
```python
job_df = job_df.withColumn("skill", explode(col("skills")))
skill_counts = job_df.groupBy("skill").count().orderBy("count", ascending=False).limit(10)
skill_counts.show()
```
![Top Skills](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/f810ed66-d245-4901-89af-556d8c373cf5)
🔹 **Key Findings:** Java, JavaScript, and Sales are among the most in-demand skills.

### **3. Most Frequent Number of Skills per Job Description**
```python
job_df = job_df.withColumn("num_skills", count(col("skills")))
num_skills_dist = job_df.groupBy("num_skills").count().orderBy("count", ascending=False).limit(5)
num_skills_dist.show()
```
![Skill Distribution](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/c54bac3b-dadc-41be-a449-73a9bf348108)
🔹 **Key Findings:** Most job descriptions require **10 skills**.

### **4. Frequency Distribution of Skills in Lowercase**
```python
job_df = job_df.withColumn("skill", lower(col("skill")))
skill_counts_lower = job_df.groupBy("skill").count().orderBy("count", ascending=False).limit(10)
skill_counts_lower.show()
```
![Lowercase Skills](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/322ffb0b-781c-4f3c-a53e-a7cc292688ba)
🔹 **Key Findings:** Standardizing skill names provides a clearer distribution of key skills.

### **5. Impact of Joining O*NET Data**
```python
combined_df = job_df.join(tech_df, job_df["skill"] == tech_df["skill"], "inner")
num_skills_before = job_df.count()
num_skills_after = combined_df.count()
```
🔹 **Result:** The number of skills increased after joining with **O*NET dataset**.

### **6. Top 10 Most Frequent 'Commodity Titles' Across Job Descriptions**
```python
commodity_counts = combined_df.groupBy("commodity_title").count().orderBy("count", ascending=False).limit(10)
commodity_counts.show()
```
🔹 **Key Findings:** Highlights the most common job categories based on skills.
