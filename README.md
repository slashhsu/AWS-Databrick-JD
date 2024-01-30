# AWS-Databrick-JD

1.	DESCRIPTION OF ANY SETUP REQUIRED
     In the first stage, I decided to use Databrick, which is hosted on Amazon web services, to perform this task. First of all, I created a Databricks workspace and create an account for the community edition, which serves as a centralized hub for managing and collaborating on data engineering tasks. In the workspace, you can create notebooks, clusters, and jobs to execute data preprocessing pipelines. Databricks provides an intuitive web-based interface to create and manage these resources. Secondly, we need to create a notebook which is the primary tool for building and executing data preprocessing pipelines. Create a notebook in the Databricks workspace and define the required code cells for our preprocessing tasks. Thirdly, configure a Databricks cluster, which is a set of virtual machines responsible for performing computations. Here, we are using 12.2 LTS (Scala 2.12, Spark3.3.2), and due to the community edition, the cluster will be automatically terminated after an idle period of 2 hours; after that, we have to create a new cluster. 
2.	DATA PRE-PROCESSING 
  2.1 IMPORT LIBRARIES
Before connecting to a data source, we need to ensure that the necessary libraries are installed in the Databricks environment. These libraries might include popular data manipulation libraries such as Pandas, NumPy, or Databricks-specific libraries like PySpark or SparkSQL. Here we import SparkSQL as F and use Python language to perform this task. (See Figure 1)

![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/c6dc4e4e-549f-441b-8b6a-676d3b31294f)

Figure.1 Import Spark.sql
2.2 READ DATA
 The next step is to connect data resources. Databricks supports a wide range of connectors for accessing data stored in various file formats and databases such as CSV, JSON, Parquet, Azure Blob Storage, AWS S3, and more. Establishing connections to the relevant data sources will allow us to retrieve the data for preprocessing. In this task, we upload the data from the local file, which has two types of files; one is ‘job_skill_50k.csv’, and the other is ‘technology skill.txt’. That indicates we have to deal with these files in a different way. First of all, we click the file function, which is just right top of the page and explore the function ‘upload to database’. Secondly, drag and drop the file into the box and generate a set of APIs so we can read the file later. Finally, we use ‘Spark.read’ command to read the file as shown in the figure.2 and Figure 3. 
     In figure.2, the job is the variable name representing the file ‘job_skill-50k’. ‘Tech_skill’ represents the file ‘technology skill’. ‘spark’ refers to the Spark Session object, which is the entry point to interact with Spark and perform operations on distributed data. ‘format’ specifies the format of the data source we want to read, in the figure 2 case, it's a CSV file. ‘Option ("header", "false")’ sets an option for the reader. In this case, due to there being no header in the job file, so we set it to false to specify that this CSV file doesn't have a header row. ‘load("  “)’ specifies the path of the CSV file to load. In the URL, dbfs stands for Databricks File System. The specific path may vary based on the Databricks environment and the file location. 
     On the other hand, the other file is in Txt format, so it is slightly different from the CSV format. ‘Option ("header", "true")’ set an option for the reader because it has a header.  The last command, “text( )” is indicated that this is txt format as well as specifies the path of the text file to load.

     
 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/6108eef1-9809-4559-86a1-ca14c5ba5d3a)

Figure.2 Read job data
 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/2f77607a-44da-4f7d-b518-fde9afed83a6)

Figure.3 Read technology skill data
2.3 REMOVE DUPLICATE ROW
     Figure.4 is the first step for cleaning data which is to remove duplicate rows. ‘distinct()’ is a Data Frame transformation that returns a new Data Frame with duplicate rows removed. It ensures that each row in the Dataframe is unique. ‘.columns’ is an attribute of a Data Frame that returns the list of column names in the Data Frame. It provides an array-like representation of the column names. It can help us to select the column that we need in the following task. ‘select()’ is a Dataframe transformation that selects specific columns from the Dataframe or applies transformations to the existing columns. ‘F.array(job_column)’ creates an array column that contains all the column values from ‘job_distinct’. ‘.alias("job_list")’ assigns the name "job_list" to the newly created array column.
     Figure.5 is the process to split the value, ‘F.split("value", "\t")’ splits the values in the "value" column based on the delimiter specified as "\t", which represents a tab character, where F is an alias for pyspark.sql.functions.

 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/cbeaa66f-4aee-46e0-bd98-ea50566bba1c)

Figre.4 Transform into columns and split the value of ‘job_skill-50k’ dataset
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/e466f32a-cc01-4f23-805a-cbfa061b2c54)

 Figure.5 Split the value of ‘technology skill’ dataset
 2.4 REMOVE ‘NONE’ VALUE
     In programming, the term "None" typically refers to a special value that represents the absence of a value or the lack of any specific object. It is often used to indicate the absence of a valid or meaningful value. Here we need to remove none value of the dataset to avoid misleading results. Figure.6 is the process for removing None value. ‘.withColumn()’ is a Data Frame transformation that adds a new column to the Data Frame. ‘F.array(F.lit(None))’ creates an array column with a single element None for each row. 
     In the second line code, ‘F.array_except( )’ creates an array column that contains all the elements from Job_skill_col_null.job_list except for the None values in the Job_skill_col_null.null column. This operation essentially removes the null values from the job_list column. This is useful for identifying and handling null or missing values. For “Tech_skill” dataset, we use the same way to remove the None value and name it ‘Job_skill_null’ which is shown in figure.7. To simplify it, we add a new row with the value of None to compare with the original value and take out the column that does not contain None.

 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/bd333eea-9594-4caf-a400-035180edfb87)

Figure.6 Remove ‘None’ value of job_list dataset
 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/bf2838b1-6909-4bc2-b18e-bcab8e998225)

Figure.7 Remove ‘None’ value of Tech_skill dataset
In conclusion, the provided code performs several operations between two datasets. It removes duplicate rows, splits the value, extracts the column names, creates an array column containing the job list, adds a column with None values for null handling, and finally creates a new column that excludes the null values from the job list.

3. PROBLEM ANSWER (6 QUESTIONS)
3.1. CONFIRM THE DISTINCT NUMBER OF JOB DESCRIPTIONS IN 50K DATASET
•	Assumption: 
The data should be a Dataframe, and there should be no missing values or duplicate values.

•	Implementation outline:
To confirm the distinct number of job descriptions in the 50k dataset, we have to preprocess the dataset to endure there is no missing value and duplicate row, which we have already done before. Finally, we can use the ‘.count()’ function to get the number of non-null rows in the Dataframe.

•	Result of the question:
The result of the distinct number in the 50k dataset is 50,000 
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/970dd0c3-0498-489b-868c-6bd78294f5cf)



•	Discussion:
For data analysis, it is important to understand the characteristics of the data to ensure that you understand the data set and better extract the information from it. Take this question, for example, the size of the dataset may be an issue because it may influence the flexibility or cost. If your analysis involves complex aggregations or filtering conditions, leveraging operations like filter(), groupBy(), and other aggregation functions can provide the necessary flexibility. Therefore, depending on the dataset size and available system resources, different operations may have varying execution times as well as costs.

3.2. PRESENT FREQUENCIES WITH WHICH DISTINCT SKILLS ARE MENTIONED IN JOB DESCRIPTIONS, AND PRESENT THE TOP 10
•	Assumption:
1.	We assume the 50k dataset contains a column that consists of a job description.
2.	Data needs to be converted to a countable format.
3.	There is no duplicate row in the dataset
4.	There are more than 10 skills in the job description

•	Implementation outline:
To obtain the frequencies of distinct job skills, as picture shown below, we extract the skills mentioned in the job descriptions by splitting the text into individual skills using the ‘explode()’ function. The ‘explode ()’ function splits the array column "not_null" into multiple rows, with each row containing a single skill. The exploded skills are stored in a new column named "skill". In the second line, we group the DataFrame ‘job_skill_split’ by the "skill" column and compute the count of occurrences for each distinct skill using the count() function. The resulting DataFrame ‘skill_freq’ contains the distinct skills and their corresponding frequencies. In the third line, Cause the question needs us to present the top 10 job descriptions. Therefore, we sort the DataFrame skill_freq in descending order of the "count" column, which represents the frequencies of the skills. The last line presents the skills and their frequencies. Limiting the output to 10 rows, it specifically shows the top 10 skills with the highest frequencies.
 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/2ca926d0-64f7-4f16-9e03-b63ca158f466)

 


•	Result of the question:
It shows the Top 10 skills frequencies of 50k dataset.
<img width="154" alt="image" src="https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/f810ed66-d245-4901-89af-556d8c373cf5">



 
•	Discussion:
     According to the results, the presence of Java and Javascript as the top two skills reflects the continued importance of programming languages in the job market. These skills are highly sought after in various technology-driven industries and play a significant role in software development and web-related projects. In the Top 3 and 4, sales and business development have a strong emphasis in the job description, which indicate the demand for professionals who can drive revenue generation, build client relationships, and contribute to business growth. These results show us that some of the most popular work techniques, and it is possible to know whether software and business insights are still widely used and needed
     However, it's important to note that the interpretation of these results should be done in the context of the specific dataset and industry. Different datasets and industries may exhibit variations in the demand for skills, and additional analysis and domain knowledge may be required for a comprehensive understanding of the skill landscape.
3.3 Find the 5 most frequent numbers of skills in JDs across the dataset.
•	Assumption:
Here are some assumptions about this question:
1.	The dataset contains a column or multiple columns representing job descriptions (JDs), and the skills mentioned within the JDs are stored in a structured format.
2.	Each skill is represented as a distinct entity within the dataset, and there is a clear separation between different skills.
3.	Assume that the number of skills refers to the number of occurrences of each distinct skill within the job descriptions.
4.	The frequency of each group of skills can be determined by counting the number of occurrences of each group of skill number across the dataset. 
5.	The amount of JD’s data is sufficient to present the top 5 skills
•	Implementation outline:
For the first and second assumptions, we need to remove the column that is irrelevant, which is the first line of code below. In the second line, it retrieves the column names of the DataFrame "job_nofirst" and stores them in the job_column3 variable. In the third line, it creates a new DataFrame, job_skill_col3, by selecting the columns from job_column3 and grouping them into an array column called "job_list3". Since it is an original Dataframe, we need to remove missing values again, as shown in lines 4 and 5 below.
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/07834083-d02c-4660-b2e7-f184f48a6026)

For assumptions 3 and 4, we should count the number of skills; from the first line below, this adds a new column called "num_skills" to the DataFrame job_skill_nonull3. It calculates the size (number of elements) of the "not_null" array column for each row, representing the number of skills mentioned in each job description. In the second row, it groups the DataFrame "job_num" by the "num_skills" column and calculates the count of occurrences for each distinct number of skills using the “count()” function. It then sorts the result in descending order based on the "count" column. Finally, it displays the top 5 rows of the resulting Dataframe, presenting the 5 most frequent numbers of skills and their respective frequencies.
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/5693fde8-147b-4b31-bdf8-e3ada66783cb)



•	Result of the question:
Here is the result of 5 most frequent skills in JDs:
 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/c54bac3b-dadc-41be-a449-73a9bf348108)


•	Discussion:
Technically, in assumption 1, the removal of insignificant rows can actually be done in preprocessing part to avoid redundant steps. But if not anticipating any future problems, it is best to keep the original data. In addition, according to the results, the analysis reveals that job descriptions with 10 skills are the most frequent, with a count of 10,477. This suggests that there is a significant demand for job roles requiring a broad range of skills. Moreover, by analyzing the frequency of different numbers of skills in job descriptions, businesses can identify emerging market trends. For example, a significant frequency of job descriptions with 10 skills might indicate a growing need for versatile professionals who can handle diverse responsibilities. This insight can inform businesses' strategic decision-making and resource allocation.
3.4. CHECK THE DISTRIBUTION OF THE FREQUENCIES OF DISTINCT SKILLS IN LOWERCASE.
•	Assumption:
1.	Except based on the previous question’s assumption, there is a need to convert the skills to lowercase before analyzing their frequencies. 
2.	Data needs to be converted to a computable format.
3.	The amount of both datasets is sufficient to present the number

•	Implement outline
As shown below picture, to convert data to a computable format, we use the “explode()” function to split the skills column into multiple rows, with each row representing a distinct skill and create a new column to store the exploded skills. After that, we use the “lower()” function to convert all the extracted skills into lowercase and store them in the new column. Finally, Use the “groupBy()” function to group the skills and the “count()” function to calculate the frequency of each distinct skill, sort the frequencies in descending order using the sort() function, and limit the result to 10.
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/c198d925-c0c0-4d97-84e0-8d60f3e7e7c8)

 
•	Result of the question:
Here is the result of the 10 most frequent lowercase skills:
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/322ffb0b-781c-4f3c-a53e-a7cc292688ba)

 
•	Discussion:
The lowercase version of skills shows a more standardized representation compared to the original version, which includes variations in capitalization. This allows for a more consistent analysis and comparison of skills. Comparing the frequencies of skills in the lowercase and original versions, we can see that Top4 skills remain consistently popular across both versions. However, the term “marketing” has highly increased in lowercase. It may indicate the rise in the frequency of "marketing" could indicate a growing need for marketing professionals in various industries. It suggests that companies are increasingly recognizing the importance of marketing strategies, digital marketing, or other related skills in their job requirements.
3.5. FIND THE CHANGE IN THE NUMBER OF SKILLS BEFORE AND AFTER THE JOIN THE O*NET DATASET
•	Assumption:
Here is some assumption about this question:
1.	Both datasets need to be in the same form, such as all the skills should be in lowercase.
2.	To combine the skills with the JD dataset and the O*NET dataset, there is a need to define matching criteria or rules.
3.	All the data should be transferred into a countable form.
4.	The amount of dataset is sufficient to present the number

•	Implementation outline:
In the preprocessing part, we already cleaned the dataset, including removing the duplicate row and missing values. Based on cleaning data, we first deal with the Tech_skill dataset, which should extract the skill column to match the JDs to fulfil assumption 1.  In the first step, the "skill" column is extracted from the "split" array column of the "Tech_skill_nonull" DataFrame. This is done using the F.col("split")[1] expression, which retrieves the element at index 1 of the "split" array column. Next, we need to extract the single value of the Tech_skill dataset; To prepare for Q6, the "Commodity Title" column is extracted from the "split" array column of the "Tech_example" DataFrame by using the F.col("split")[3] expression. The next step is to use the “F.lower” function to convert column “skill” into lowercase to match the JDs. 
In the next two lines of code, we use “drop ()” to drop the unnecessary columns to make the dataset look neat and tidy. Finally, we use the “join ()” function to combine two datasets using the "lower_job" column from both Dataframes and set the type to “inner”. Based on the question, the join condition is that the values in the "lower_job" column of “Tech_example_lower_comb” should match the values in the "skill" column of “job_skill_lower_comb”. Therefore, we can calculate the different number of skills before and after joining.
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/18741ae6-abf0-4832-9301-15e782c97de6)
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/1e8b4510-c4c9-4bdb-b905-4abaadb6795f)


 
 
Lastly, we use “count()” to calculate the number of skills before and after joining.
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/e3d540b1-75d2-4e1d-be57-a5e997762af6)
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/2e2365e0-eab6-4ddf-ba34-ce6fb700c117)

 
 
•	Result of the question:
Here is the results.
Before:
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/69a92f5d-1a24-4235-9e59-6b58c69dbf87)

 
After:
![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/23ed6154-b811-4bc5-b2d1-ea6d7ad2e1fa)

 
•	Discussion
During the data processing, we remove unnecessary columns to make the data neat. The answer will be the same if we omit this step and calculate directly. It can improve code readability and also enhances efficiency. In addition, we extract the column “commodity title” to prepare for Q6; this will enhance efficiency as well as reduce unnecessary codes. In conclude, this type of analysis then helps to assess the overlap and alignment between job market requirements and established industry skill frameworks. 
3.6. FIND THE 10 MOST FREQUENT “COMMODITY TITLE”S ACROSS ALL THE JOB DESCRIPTIONS.
•	Assumption:
1.	There is already a Dataframe that combines two datasets (JDs and O*NET)
2.	The Dataframe should be countable
3.	The amount of both Dataset is sufficient to present the frequency

•	Implementation outline:
Based on question 5, In here, we use “group()” function to group the data by the "Commodity Title" column and sort the grouped data based on the "count" column in descending order. Finally, the show(10) function is used to display the top 10 results of the previously sorted DataFrame.
 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/b4483d12-7e25-4d51-a007-3d15e6f2b51b)


•	Result of the question:
Here is the result:
 ![image](https://github.com/slashhsu/AWS-Databrick-JD/assets/137000188/d2e20ef3-e3ff-4307-95c9-77d002ee0214)

