# Exam 70-768: Developing SQL Data Models

This is the first part of certification: **MCSA: SQL 2016 BI Development**. It could be a good way to apprrove your ability to write advanced, highly optimized SQL queries to retrieve large volume data. Comparing to it's precedemt exam **70-461: Querying Microsoft SQL Server 2012** , the exam 70-768 has cutted a large part of the content that is too complext and unlikely to be used by most DA or BA. Therefore, pass exam 70-768 will be relatively easier.

I'm creating this repository to record all the notes of Exam Ref for 70-761, beganing at 02/25/2019. Also I will record the daily process of studying.

Here is some details of this exam:

- Number of questions: 54

- Duration: 3 hours

- Passing Score: 700 / 1000 (38 questions correct at least)

- Question types: multiple choice, sequence, code snippets

Certification Link:
https://www.microsoft.com/en-us/learning/mcsa-sql2016-database-development-certification.aspx

Exam Link:
https://www.microsoft.com/en-us/learning/exam-70-761.aspx

Exam Ref Link: 
https://1drv.ms/b/s!AgkjWRgK41qpySTEOKGs--ShxBVr

----------------------------------------------------------------------------------------------------------------------------------------
# Updating History

- 02/25/2019:

   - [X] Preface Part
   
   - [X] Skill 1.1
   
   - [X] Skill 1.2




----------------------------------------------------------------------------------------------------------------------------------------
# 0. Some Usefull Links



----------------------------------------------------------------------------------------------------------------------------------------
# 1. Manage Data with Tansact-SQL
## 1.1 Create Transact-SQL SELECT Queries
### 1.1.1 Some Methdologies and Theories

a. What is SQL?
   - **SQL** (Structured Query Language) is a domain-specific **language** used in programming and designed for **managing data held in a relational database**
   
   - **SQL** is also a **standard** of both the **International Organization for Standards (IOS)** and the **American National Standards Institute (ANSI)**. In the other words, these two standards will not create conflicts or errors with SQL.
   
   - All leading database vendors, e.g. Microsoft and Oracle, implement a dialect of SQL as the main language to manage and manipulate data in the database platforms. Therefore, the core language elementsare the same (Trandard SQL).
   
b. What is T-SQL?
   - **T-SQL** (Transact-SQL) is the main SQL language used to manage and manipulate data in the Microsoft relational database management systems (RDBMSs) and Azure SQL Database (the cloud platform)
   - **T-SQL** is a dialect of standard SQL. In the other words, T-SQL has all the functions that Standard SQL has, and also it has some extended functions. E.g.:
   
     - T-SQL supports **<>** and **!=**; Standard SQL only supports **<>**.
     - T-SQL supports functions **CONVERT** and **CAST**, Standard SQL only support **CAST**.
     
   - In most situation, please write queries follow Standard SQL to avoid many potential problems.

c. What is Relational Database?
   - **Relational Database** is a digital database based on **Relational Model**, which organizes data into tables (relations) of columns (attributes) and rows (tuples or records)
   
   - A **Relation (Table)** has a heading and a body:
   
     - Heading: a set of attributes (columns), which present the type of this column and the name of this column
     - Body: a set of tuples (rows), which represent instances of that type of entity (the contents)
     - Each tuple's heading is the heading of the relation.
     
d. Some Implying Theories of Relational Database
   - **Set** is a collection M into a **whole** of definite, of **distinct** objects m
   
     - Whole: we do not interact with the individual elements of the set (single column or row), rather with the set as a whole
     - Distinct:a set has no duplicates (there are no two same columns or rows)
     - There is no relevance to the order of elements in a set
     
   - **Predicate Logic**: an expression that when attributed to some object, make proposition either TRUE or FALSE.
   
     - Logic test is available on SQL using ** =, <>, <, >, <= and >=**
     - we are often use predicates (logic test) to;
       - Enforce Data integrity
       - Filter Data
       - Define the data model properties
       
e. Some Places That T-SQL Dose Not Follow These Theories
   - T-SQL is based more on **Multiset Thoery** and can return duplicates. E.g.:
       ```
       SELECT COUNNTRY FROM HR.Employees;
       ```
       | Country |
       |---------|
       | USA |
       | USA |
       | USA |
       | ... |
       
  - There is no relevance to the order of elements in a set
  
    - rows in table have no particular order (even though there is the order that row physically stored)
    - If we use **ORDER BY** to sort the selected rows, it's called **cursor** in Standard SQL
    - We can explicitly referring to ordinal positions of columns from the result using:
      ```
      SELECT empid, lastname
      FROM HR.Employees
      ORDER BY 1;
      ```
  - T-SQL can return culomns with no name but not same names:
    ```
    # No Column Name:
    
    SELECT  empid, firstname + ' ' + lastname
    FROM HR.Employees;
    ```
    
    ```
    # Change Column Name:
    
    SELECT T1.a AS a1, T2.a as a2 ...
    ```

### 1.1.2 Understanding Logical Query Processing

- **Query Clauses Writing Order** is different to **Logical Query Processing Order**
 
- Query Clauses Writing Order: 

  - SELECT
  - FROM
  - WHERE
  - GROUP BY
  - HAVING
  - ORDER BY
     
- Logical Query Processing:

  - FROM
    - After get access to a database, query need to decide which tables should be readed
  - WHERE
    - We don't need all the records. Using some conditions to filter out part of them.
  - GROUP BY
    - And then do some aggregations, based on one or more fields
  - HAVING
    - Filter again, cut part of the records
    - **REMEMBER!** any filter after the GROUP BY shall use HAVING rather than WHERE
  - SELECT
    - And cut part of the fields
  - ORDER BY
    - We already get all the records we want, just change their order if we want.
    - **REMEMBER!** ORDER BY is the only clause that all refer to the aliases (new names after AS) that defined in the SELECT clause, because it's the only clause that run after SELECT.
    
### 1.1.3 Some Details of Each Clause
a. FROM
   - We can define aliases for tables in FROM clause
   
   ```
   SELECT a
   FROM A AS B;
   ```
  
b. SELECT
   - We can define aliases for fields in SELECT clause
   
   ```
   SELECT a AS b
   FROM A;
   ```
   
   - We can add simple calculation expression in SELECT clause
   
   ```
   SELECT empid, firstname + N' ' + lastname as a
   FROM A;
   ```
   
   - We can assign no name to a field
   
   ```
   SELECT empid, firstname + N' ' + lastname
   FROM A;
   ```
   
   - We can SELECT without FROM (dosen't work on Standard SQL)
   
   ```
   SELECT 10 AS a, 'ABC' as b ;
   ```
   
   - **REMEMBER!** the rules of delimiting identifiers could be complex. If we find any error on aliase, just use [] to include the aliase to solve the error.
   
  





















