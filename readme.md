# Exam 70-768: Developing SQL Data Models

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

1. SQL Query Basic

2. Subquery & Apply

3. Table Expression, Temporary Tables & CTE

4. Grouping Sets

5. Pivot & Unpivot

6. Temporal Data and versioning

7. XML

8. Json

8. Views, UDF & Stored Procedures

9. Transaction Handling & Error Handling

10. Data Type Transfering & Nulls




----------------------------------------------------------------------------------------------------------------------------------------
# 7.XML

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



















