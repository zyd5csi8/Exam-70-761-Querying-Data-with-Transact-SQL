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

- [X] 1. SQL Query Basic

  - SELECT组成部分及运行顺序。
 
  - 每部分细节

- [X] 2. JOIN的使用

- [X] 3. Set运行符的使用

- [ ] 5. DML常见语句

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

- [ ] 4. 常见functions

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
    
    - 当名字不符合命名要求的时候，用[]将名字括起来就可以了。
  
  2. WHERE：根据条件筛去一部分数据。
  
    - 二值逻辑和三值逻辑
    
      - 二值逻辑： T or F
      
      - 三值逻辑： T or F or Null
      
        - 和Null相关的一切逻辑运算都会返回Null
        
        - 三值逻辑运算需要将input消除Null，或者考虑Null存在的条件。例如：
        
        ```
        SELECT empid, firstname, lastname, country, region, city
        FROM HR.Employees
        WHERE ISNULL(region, N'WA') <> N'WA';
        ```
        
        或者：
        
        ```
        SELECT empid, firstname, lastname, country, region, city
        FROM HR.Employees
        WHERE region <> N'WA'
        OR region IS NULL;
        ```
        
    - 多个条件的运行顺序
    
      - NOT优先于AND，优先于OR
      
      - 使用()来设置运行顺序。
      
    - 对不同类型的数据进行筛选：
    
      - 可以使用CAST（失败终止）或者TRY_CAST（失败返回NULL）来更改数据类型。
    
      - STRING
      
        - N将format设置为nchar / nvarchar (unicode),有可能提高运行效率。
        
        - LIKE的使用：对目标进行pattern比较
        
        ```
        SELECT empid, firstname, lastname
        FROM HR.Employees
        WHERE lastname LIKE N'D%';
        ```
        
          - %为N个字符
          
          - _ 为1个字符
          
          - 使用ESCAPE或者[]来指代%或_
          
          ```
          LIKE '!_%' ESCAPE'!';
          
          LIKE [_%];
          ```
        
      - TIME
      
        - 常写作 “2000-10-10”格式
        
        - 写作LN格式 "20001010"可以免除不同国家的格式限制。
        
        - 可以进行逻辑运算，且date和date time可以写在一起。
        
          ```
          SELECT orderid, orderdate, empid, custid
          FROM Sales.Orders2
          WHERE orderdate BETWEEN '20160401' AND '20160430 23:59:59.999';
          ```
    - WHERE中的优化
    
      - 不使用FUNCTION比使用FUNCTION好
      
      - 别把整列放入function中
      
        - 把一个常数或者variable放进去
        
        - 使用LIKE运行符
        
      - 当NULL存在时，使用 两个逻辑比较 将会优于使用 一个带有function的逻辑比较。
      
        ```
        A优于B：
        
        A:
        SELECT orderid, shippeddate
        FROM Sales.Orders
        WHERE ISNULL(shippeddate, '99991231') = ISNULL(@dt, '99991231');
        
        
        B:
        SELECT orderid, shippeddate
        FROM Sales.Orders
        WHERE shippeddate = @dt
        OR (shippeddate IS NULL AND @dt IS NULL);
        ```
      
      - 尝试使用WHERE EXIST语句来优化query
      
  
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
    
    - ORDER 可以设置DESC或者ASC，默认ASC。
    
    - 可以根据多列来排序，并且分别设置排序顺序。
      
      ```
      SELECT empid, firstname, lastname, city, MONTH(birthdate) AS birthmonth
      FROM HR.Employees
      WHERE country = N'USA' AND region = N'WA'
      ORDER BY city ASC, empid DESC;
      ```
    
    - 可以根据列的位置序号来指定列。
    
      ```
      SELECT empid, city, firstname, lastname, MONTH(birthdate) AS birthmonth
      FROM HR.Employees
      WHERE country = N'USA' AND region = N'WA'
      ORDER BY 4, 1; - birthmonth & empid
      ```
      
    - Nulls将永远置于一列的顶端。
    
    - 无order by将返回relational result，即运行速度最快的顺序。此顺序不保证不会改变。
    
    - TOP的使用：
      
      - 不是Standard Content
    
      - 用于输出最上面的某几个records
      
      ```
      SELECT TOP (3) orderid, orderdate, custid, empid
      FROM Sales.Orders
      ORDER BY orderdate DESC;
      ```
      
      - 可以使用TOP(N) PERCENT来基于%rank选择
      
      - 可以使用TOP(N) ColumnA WITH TIRES 来选择所有rank相同的结果。（即使只选择TOP 2，若有10个目标并列也会全部输出）。
      
      - ORDER BY (SELECT NULL)来表示返回relational result （用于指明默认排序，没有实际作用）。
      
    - OFFSET-FETCH的使用
    
      - TOP的标准语句版本
      
      - 有NEXT和FIRST两个选项：消去0行用FIRST，消去N行用NEXT
      
      ```
      SELECT orderid, orderdate, custid, empid
      FROM Sales.Orders
      ORDER BY orderdate DESC, orderid DESC
      OFFSET 0 ROWS FETCH FIRST 25 ROWS ONLY;


      SELECT orderid, orderdate, custid, empid
      FROM Sales.Orders
      ORDER BY orderdate DESC, orderid DESC
      OFFSET 50 ROWS FETCH NEXT 25 ROWS ONLY;
      
      ```
      
      - **可以只写OFFSET。但写FETCH就必须要有OFFSET**。
      
----------------------------------------------------------------------------------------------------------------------------------------
# 2. JOIN的使用

  - Cross JOIN
  
  N JOIN M Return N x M
  
  Self Join 同理。
  
  ```
  A (Cross) JOIN B; - 不需要写KEY.
  ```
  
  - INNER JOIN
  
    - A和B都有才有。
    
    - 最好根据一列unique key连接，不然会产生重复 (foreign key-unique key relationship).
    
    - 千万不要尝试使用WHERE的条件来代替INNER JOIN.
    
  - Outer JOIN
  
    - 未匹配成功的部分返回NULL
    
    - Left Outer JOIN：左全
    
      - 反选套路：
      
      ```
      A Left JOIN B on A.key = B.key WHERE b.key IS NULL
      ```
     
    - Right Outer JOIN: 右全
    
      - 反选套路：
      
      ```
      B Right JOIN A on B.key = A.key WHERE B.key IS NULL
      ```
      
     - Full Outter JOIN: 左右全
     
     ```
     A Full Outter JOIN B on A.key = B.key
     ```
  
 - Composite Join
 
  根据多个键的单个JOIN。
  
  例如：
  
  ```
  SELECT EL.country, EL.region, EL.city, EL.numemps, CL.numcusts
  FROM dbo.EmpLocations AS EL
  INNER JOIN dbo.CustLocations AS CL
  ON EL.country = CL.country
  AND EL.region = CL.region
  AND EL.city = CL.city;
  ```
  
  在join中如果涉及NULL，千万不可以尝试在JOIN中将NULL换掉，将会使效率降低。应该使用三值运算来处理NULLs：
  
  ```
  SELECT EL.country, EL.region, EL.city, EL.numemps, CL.numcusts
  FROM dbo.EmpLocations AS EL
  INNER JOIN dbo.CustLocations AS CL
  ON EL.country = CL.country
  AND (EL.region = CL.region OR (EL.region IS NULL AND CL.region IS NULL))
  AND EL.city = CL.city;
  ```
  
  - 使用Merge Join优化
  
    - 普通optimizer一般会选择nested loops strategy来进行join，适合小数据量的JOIN。
    
    - 可以手动设置Merge JOIN来优化性能，适合数据量大的JOIN。原理为从JOIN的两端同时开始运行。
    
      ```
      SELECT EL.country, EL.region, EL.city, EL.numemps, CL.numcusts
      FROM dbo.EmpLocations AS EL
      INNER MERGE JOIN dbo.CustLocations AS CL
      ON EL.country = CL.country
      AND (EL.region = CL.region OR (EL.region IS NULL AND CL.region IS NULL))
      AND EL.city = CL.city;
      ```
      
 - Multi-join queries 多个表JOIN在一起
 
   - 最好给每个表命名：
   
    ```
    SELECT
    S.companyname AS supplier, S.country,
    P.productid, P.productname, P.unitprice,
    C.categoryname
    FROM Production.Suppliers AS S
    LEFT OUTER JOIN Production.Products AS P
    ON S.supplierid = P.supplierid
    INNER JOIN Production.Categories AS C
    ON C.categoryid = P.categoryid
    WHERE S.country = N'Japan';
    ```
    
   - 每个JOIN都处于平行位置，将会从左到右来运算。可以使用()来改变运算顺序。
   
----------------------------------------------------------------------------------------------------------------------------------------

# 4.SET运行符的使用。

一共有四个SET运行符。要求使用时，两边的列名字一样，数据类型一样。

运行顺序为：INTERSECT优先于UNION和EXCEPT，后两者平行。

  - UNION
  
    - 将两边的数据合并在一起
    
    - 将会消除duplicate 和NULLs
  
  ```
  SELECT country, region, city
  FROM HR.Employees
  UNION
  SELECT country, region, city
  FROM Sales.Customers;
  ```
  
  - UNION ALL
  
    - 将两边的数据合并在一起
    
    - 将会保留duplicate 和NULLs
    
    - 会对每个record进行duplicate和NULLs的测试，会使性能降低。
  
  ```
  SELECT country, region, city
  FROM HR.Employees
  UNION ALL
  SELECT country, region, city
  FROM Sales.Customers;
  ```
  
  - INTERSECT
  
    - 得到A，B重合的部分
    
    - 将会比较所有列的所有records
    
    - 只会返回一遍
  
    ```
    SELECT country, region, city
    FROM HR.Employees
    INTERSECT
    SELECT country, region, city
    FROM Sales.Customers;
    ```
  
 - Except
 
   - 用于反选，得到存在于A但是不存在于B中的内容。
   
    ```
    SELECT country, region, city
    FROM HR.Employees
    INTERSECT
    SELECT country, region, city
    FROM Sales.Customers;
    ```

----------------------------------------------------------------------------------------------------------------------------------------
# 5. DML常见语句

  1). Insert
  
    - PK:
    
      - 表中的PK有的是自动产生值的（不需要手动设置constrain），有的是手动写入值，然后创建PK Constrain的。
      
      - 当需要写入PK时，因为有PK Constrain的存在，需要先将IDENTITY_INSERT打开，才可以写入。写入完成后将其关闭。
      
        ```
        ```
        
    - 



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














