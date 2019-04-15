# Exam 70-761: Querying Data with Transact-SQL

This is the first part of certification: **MCSA: SQL 2016 BI Development**. It could be a good way to apprrove your ability to write advanced, highly optimized SQL queries to retrieve large volume data. Comparing to it's precedemt exam **70-461: Querying Microsoft SQL Server 2012** , the exam 70-768 has cutted a large part of the content that is too complext and unlikely to be used by most DA or BA. Therefore, pass exam 70-768 will be relatively easier.

I'm creating this repository to record all the notes of Exam Ref for 70-761, beganing at 02/25/2019. Also I will record the daily process of studying.

Here is some details of this exam:

- Number of questions: 54

- Duration: 3 hours

- Passing Score: 700 / 1000 (38 questions correct at least)

- Question types: multiple choice, sequence, code snippets

[Certification Link](https://www.microsoft.com/en-us/learning/mcsa-sql2016-database-development-certification.aspx)

[Exam Link](https://www.microsoft.com/en-us/learning/exam-70-761.aspx)

[Exam Ref Link](https://1drv.ms/b/s!AgkjWRgK41qpySTEOKGs--ShxBVr)

----------------------------------------------------------------------------------------------------------------------------------------
# Content

- [ ] 1. SQL Query Basic

       - SELECT组成部分及运行顺序。
 
       - 每部分细节

- [ ] 2. JOIN的使用

- [ ] 3. Set运行符的使用

- [ ] 4. 常见functions

- [ ] 5. DML常见语句

      - RETURN的使用

- [ ] 6 Subquery使用

- [ ] 7. APPLY使用

- [ ] 8. Derived Tablea & CTEs

- [ ] 9. Aggregating & Grouping Sets

- [ ] 10. Pivot & Unpivot

- [ ] 11. Window Functions

- [ ] 12. System Versioned Temporal Table

- [x] 13. XML

- [x] 14. JSON

- [ ] 15. Views

- [ ] 16. UDF

- [ ] 17. Stored Procedures

- [ ] 18. Transactions

- [ ] 19. Try-Catch结构

- [ ] 20. Data Types & Nulls

----------------------------------------------------------------------------------------------------------------------------------------
# 1. SQL Query Basic

- SELECT语句书写顺序：

  1. SELECT
  
  2. FROM
  
  3. WHERE
  
  4. GROUP BY
  
  5. HAVING
  
  6. ORDER BY
  
- SELECT语句书写顺序：

  **FROM - WHERE - GROUP BY - HAVING - SELECT - ORDER BY**

  1. FROM: 从哪几个表中拉取全部数据。
  
     - 可以对表进行暂时命名：
  
     ```
     SELECT E.empid, firstname, lastname, country
     FROM HR.Employees AS E;
     ```
  
  2. WHERE：根据条件筛去一部分数据。
  
  3. GROUP BY：运行aggregating
  
  4. HAVING：在aggregation的结果上，再次根据条件筛去一部分数据。
  
     - HAVING部分只可以写aggregate function的条件
  
  5. SELECT：进行column层面的运算，并改名。
  
     - 可以直接使用 + 连接strings

     ```
     SELECT empid, firstname + N' ' + lastname
     FROM HR.Employees;
     ```
  
     - 注意：如果中间任意一段是Null，得到的结果将是Null
    
 - 可以不写FROM，直接SELECT （T-SQL特殊功能）
 
    ```
    SELECT 10 AS col1, 'ABC' AS col2;
    ```
  
  6. ORDER BY：排序，然后输出
  
    - 因为ORDER BY在SELECT后面运行，所以可以使用SELECT中更改的aliases.



----------------------------------------------------------------------------------------------------------------------------------------
# 13.XML

主要考点：记住特点，记住主要options。

- XML特点：

  - Use custom (mxied) metadata.
  
  - Allow mixed content type
  
  - Output data by tree-like structure (nested elements)

- XML主要用在select语句中，将结果返回为XML。主要有以下选项：
  
  - FOR XML RAW

  例1：

  ```
  SELECT Customer.custid, Customer.companyname,
  [Order].orderid, [Order].orderdate
  FROM Sales.Customers AS Customer
  INNER JOIN Sales.Orders AS [Order]
  ON Customer.custid = [Order].custid
  WHERE Customer.custid <= 2
  AND [Order].orderid %2 = 0
  ORDER BY Customer.custid, [Order].orderid
  FOR XML RAW;
  ```

  将会返回：

  ```
  <row custid="1" companyname="Customer NRZBB" orderid="10692" orderdate="2015-10-03" />
  <row custid="1" companyname="Customer NRZBB" orderid="10702" orderdate="2015-10-13" />
  <row custid="1" companyname="Customer NRZBB" orderid="10952" orderdate="2016-03-16" />
  <row custid="2" companyname="Customer MLTDN" orderid="10308" orderdate="2014-09-18" />
  <row custid="2" companyname="Customer MLTDN" orderid="10926" orderdate="2016-03-04" />
  ```

  例2：

  ```
  SELECT OrderId, OrderDate, Amount, Name, Country
  FROM Orders INNER JOIN Customers ON Orders.CustomerId = Customers.CustomerId
  WHERE Customers.CustomerId = 1
  FOR XML RAW
  ```

  将会返回：

  ```
  <row OrderId="1"
  OrderDate="2000-01-01T00:00 :00" Amount="3400.00" Name="Customer A"
  Country="Australia" /><row OrderId="2"
  OrderDate="2001-01-01T00:00 :00" Amount="4300.00" Name="Customer A"
  Country="Australia" />
  ```

  每一个record将会放入一个<>里面，并且平行摆放。**注意！每一行只会有一个<>.**

  
  - FOR XML AUTO
  
  [XML Auto 更多细节](https://www.tutorialgateway.org/sql-for-xml-auto/)
  
  特点为将会自动将XML变为“Nested element”。相同表中的内容会放在一个node<>中，并且会在每一个record结尾加上一个end node。
  
  例如：

  ```
  USE [SQLTEST]
  GO
  SELECT  [EmpID]
    FROM [NewEmployees] AS Employee
      Inner Join [Department] AS Depart
      ON Employee.DeptID = Depart.DeptID
    FOR XML AUTO;
  ```

  将会返回：

  ```
  <NewEmployees EmpID"1">
    <Depart DepartmentName = "HR" />
  </Employee>
  ```
  
  **注意！如果只从单一表中拉数据，而且不适用除了auto外的任何选项的话，结果将会和RAW相同。
  
  Auto会将多个表Brecords连在一个表Arecord下面：
  
  例如：

  ```
  SELECT Name, Country, OrderId, OrderDate, Amount
  FROM Orders INNER JOIN Customers ON Orders.CustomerId= Customers.CustomerId
  WHERE Customers.CustomerId= 1
  FOR XML AUTO
  ```

  将会返回：

  ```
  <CUSTOMERS Name="Customer A" Country="Australia">
   <ORDERS OrderID="1" OrderDate="2001-01-01" Amount="3400.00" />
   <ORDERS OrderID="2" OrderDate="2002-01-01" Amount="4300.00" />
  </CUSTOMERS>
  ```
  
  
  有几个重要的选项：
  
    - Element：会将column value放入不同的nodes中 （element-centric XML)。可指明以改变头尾node值。
    
    - Root: 会在文件头上加一个root node（比如例子里面的CustomerOrder，可在其中指明值。
  
      例如：

  ```
    WITH XMLNAMESPACES('ER70761-CustomersOrders' AS co)
    SELECT [co:Customer].custid AS [co:custid],
    [co:Customer].companyname AS [co:companyname],
    [co:Order].orderid AS [co:orderid],
    [co:Order].orderdate AS [co:orderdate]
    FROM Sales.Customers AS [co:Customer]
    INNER JOIN Sales.Orders AS [co:Order]
    ON [co:Customer].custid = [co:Order].custid
    WHERE [co:Customer].custid <= 2
    AND [co:Order].orderid %2 = 0
    ORDER BY [co:Customer].custid, [co:Order].orderid
    FOR XML AUTO, ELEMENTS, ROOT('CustomersOrders');
  ```

  将会返回：

  ```
  CustomersOrders xmlns:co="ER70761-CustomersOrders">
  <co:Customer>
  <co:custid>1</co:custid>
  <co:companyname>Customer NRZBB</co:companyname>
  <co:Order>
  <co:orderid>10692</co:orderid>
  ```
  
  
  - FOR XML PATH (ELEMENTS, ROOT）
  
  [XML Path 更多细节](https://www.tutorialgateway.org/sql-for-xml-path/)
  
  将会将XML分入不同elements nodes里面。Path中指明的表，所有列将会放在一个node中。
  
  例如：

  ```
  USE [SQLTEST]
  GO
  SELECT Employee.[EmpID]
        ,Employee.[FirstName]
        ,Employee.[LastName]
        ,Employee.[Education]
        ,Employee.[YearlyIncome]
        ,Employee.[Sales]
        ,Depart.[DepartmentName]
    FROM [NewEmployees] AS Employee
    INNER JOIN [Department] AS Depart 
    ON Employee.DeptID = Depart.DeptID
    FOR XML PATH
  ```

  将会返回：

  ```
  <row><EmpID>1</EmpID><FirstName>John</FirstName>...
  ```
----------------------------------------------------------------------------------------------------------------------------------------

# 14.JSON

特点：

- Used to exchange data between web pages and web server by using AJAX calls, using REST endpoint.

- In data exchange format

- Text based and lightweighted.

For [JSON Auto](https://database.guide/sql-server-for-json-auto-examples-t-sql/) 和 [For JSON Path](https://database.guide/sql-server-for-json-path-examples-t-sql/)区别不大。


  例1：单表

  ```
USE Music;
SELECT TOP 3 AlbumName, ReleaseDate
FROM Albums
FOR JSON AUTO;
  ```

  将会返回：

  ```
[
    {
        "AlbumName": "Powerslave",
        "ReleaseDate": "1984-09-03"
    },
    {
        "AlbumName": "Powerage",
        "ReleaseDate": "1978-05-05"
    },
    {
        "AlbumName": "Singing Down the Lane",
        "ReleaseDate": "1956-01-01"
    }
]
  ```
  
  例2：多表

  ```
USE Music;
SELECT TOP 2 ArtistName,
    (SELECT AlbumName 
        FROM Albums
        WHERE Artists.ArtistId = Albums.ArtistId
        FOR JSON PATH) AS Albums
FROM Artists
ORDER BY ArtistName
FOR JSON PATH;
  ```

  将会返回：

  ```
[
    {
        "ArtistName": "AC/DC",
        "Albums": [
            {
                "AlbumName": "Powerage"
            }
        ]
    },
    {
        "ArtistName": "Allan Holdsworth",
        "Albums": [
            {
                "AlbumName": "All Night Wrong"
            },
            {
                "AlbumName": "The Sixteen Men of Tain"
            }
        ]
    }
]
  ```

几个选项：

- ROOT:在最上面加上一个标题 （top Level member)

  例3：

  ```
  USE Music;
  SELECT TOP 3
    ArtistName, 
    AlbumName
  FROM Artists
    INNER JOIN Albums
      ON Artists.ArtistId = Albums.ArtistId
  ORDER BY ArtistName
  FOR JSON AUTO, ROOT('Music');
  ```

  将会返回：

  ```
  {
      "Music": [
          {
              "ArtistName": "AC/DC",
              "Albums": [
                  {
                      "AlbumName": "Powerage"
                  }
              ]
          },
          {
              "ArtistName": "Allan Holdsworth",
              "Albums": [
                  {
                      "AlbumName": "All Night Wrong"
                  },
                  {
                      "AlbumName": "The Sixteen Men of Tain"
                  }
              ]
          }
      ]
  }
  ```

- INCLUDE_NULL_VALUES: 在结果中包含NULLs.

  例4:

  ```
  SELECT c.custid AS [Customer.Id],
  c.companyname AS [Customer.Name],
  o.orderid AS [Customer.Order.Id],
  o.orderdate AS [Customer.Order.Date],
  NULL AS [Customer.Order.Delivery]
  FROM Sales.Customers AS c
  INNER JOIN Sales.Orders AS o
  ON c.custid = o.custid
  WHERE c.custid = 1
  AND o.orderid = 10692
  ORDER BY c.custid, o.orderid
  FOR JSON PATH,
  WITHOUT_ARRAY_WRAPPER,
  INCLUDE_NULL_VALUES;
  ```

  将会返回：

  ```
  {
   "Customer":{
    "Id":1,
    "Name":"Customer NRZBB",
    "Order":{
      "Id":10692,
      "Date":"2015-10-03",
      "Delivery":null
      }
    }
  }
  ```

- WITHOUT_ARRAY_WRAPPER: 将会把[]去掉。

  例5:

  ```
  USE Music;
  SELECT TOP 1 
    AlbumName, 
    ReleaseDate
  FROM Albums
  FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER;
  ```

  将会返回：

  ```
  {
      "AlbumName": "Powerslave",
      "ReleaseDate": "1984-09-03"
  }
  ```

----------------------------------------------------------------------------------------------------------------------------------------














