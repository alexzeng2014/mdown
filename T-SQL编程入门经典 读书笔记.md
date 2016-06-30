T-SQL编程入门经典 读书笔记
====

##目录

* [1.SQL-Server 基础](#jc)
* [2.T-SQL语言](#T-SQL)
* [3.数据检索](#sjjs)
* [4.SQL函数](#sqlhs)
* [5.聚合与分组](#jhyfz)
* [6.多表查询](#dbcx)
* [7.高级查询与脚本](#gjcx)

<h2 id="jc">SQL-Server 基础</h2>

**数据库关系：** 关系一般可以分为以下三种类型：

* 1对1
* 1对多
* 多对多

使用外键和唯一约束就能定义1对1和1对多关系。但是多对多不能用两个表来定义。

**主键：**每个行必须有一个唯一的值作为标记，这个值可以是有意义的，也可以是代用值。

**外键：**强制一个表中的记录和另一个表的记录之间的关系，表中包含相关记录的列就称为是外键。

**规范化规则：** 

**1.1NF**

* 表是二维的，包括行和列，每一行必须有相同的列数
* 表中每个列都包含一个特性，列中的所有特性都必须有相同的类型。
* 每一行必须有唯一标识。

**2.2NF**

* 非键字段不可依赖于主键的一部分，依赖于键值的字段要放到另一个表中。如果一个表的主键由两个列组成，行中其它行必须同时依赖这两个键，不能只依赖其中一个。

**3.3NF**

*1NF规定：行必须要键值，以便于区分，3NF进一步规定：任何行的唯一性都必须完全依赖主键。

**4.4NF和5NF**

*第四范式和第五范式是用来解决多对多关系的：第四范式它的主键定义成两个外键的组合，每个键都和外部的、相关联的表的主键相对应。第五范式是这条规则的一种独特变化，包括额外的义务逻辑、不允许某些键的组合。

**应用规范化规则**

表的举例：一张简单的雇员表

| ID | NAME| Title | Address1 | Cityline1 | Address2 | Cityline2 | HomePhone | WorkPhone | SuperName |
|----|-----|-------|----------|-----------|----------|-----------|-----------|-----------|-----------|
|1|张小凡|总经理|广州市人民路11号|广州市|广州市人民政府|广州市|020-88881234|020-88880000|张大夫|
|2|杨怡|副经理|广州市人民路11号|广州市|广州市人民政府|广州市|020-3354444|020-88880000|张小凡|
|3|朱德里|部门经理|广州市人民路11号|广州市|广州市人民政府|广州市|020-3243343243|020-88880000|张小凡|
|4|张小以|部门父经理|广州市人民路11号|广州市|广州市人民政府|广州市|020-4436554|020-88880000|朱德里|
|5|张大夫|CEO|广州市人民路75号|广州市|广州市人民政府|广州市|020-455775254|020-88880000|张大夫|

这样的表尽管阅读起来很方便，但是很难使用。

按照上面规范化，我们分拆成以下各表：

1.**EMPLOYEES** 雇员表

| EMPLOYEESID | FIRSNAME | LASTNAME | TITLEKEY | SUPERID |
|-------------|----------|----------|----------|---------|
|1|张|小凡|1|5|
|2|杨|怡|2 |2|
|3|朱|德里|3|1|
|4|张|小以|4|3|
|5|张|大夫|5|5|

TITLE职位表， SUPER上级表，其实可以视为雇员表的ID 

2.**EMPLOYEESADDRESSES** 雇员地址表（多对多表）

| EMPLOYEESID | Addresskey | AddressTypekey |
|-------------|------------|----------------|
|1|1|1|
|2|2|1|
|3|3|2|
|4|3|2|
|5|3|1|

3.**Addresses** 地址表

| Addresskey | Addressline | city | postalcode |
|------------|-------------|------|------------|
|1|人民路|广州|11号|
|2|人民政府|广州||
|3|人民路|广州|75号|

4.**AddressTypes** 地址功能表

| AddressTypekey | AddressType |
|----------------|-------------|
|1|家庭地址|
|2|办公地址|

5.**EmployeePhones** 雇员电话表

| EMPLOYEESID | PhoneKEY |
|-------------|----------|
|1 |1 |
|2| 2|
|3 | 3|

6.**Phones** 电话表

| PhoneKEY | PhoneNum | PhoneTypekey |
|----------|----------|--------------|
|1|020-88881234|1|
|2|020-43645357|1|
|3|020-88883456|1|
|4|020-88888888|2|

7.**PhoneTypes** 电话类型表

| PhoneTypekey | PhoneType |
|--------------|-----------|
|1|办公电话|
|2|家庭电话|

8.**TITLES 职位表**

| TITLEKEY | TITLE |
|----------|-------|
|1|总经理|
|2|副经理|
|3|部门经理|
|4|部门父经理|
|5|CEO|

<h2 id="T-SQL">T-SQL语言</h2>

**T-SQL语言**是与SQL Server交流的语言，而查询表达式主要用来告诉服务器该做怎么样的查询。
查询操作分为三类：

* **数据定义语言 DDL** 用于创建、管理数据库中的对象，DDL语句可以创建、修改、删除数据库、表、索引、视图、存储过程和其他对象。
* **数据控制语言 DCL** 用于控制用户和数据对象的安全权限。
* **数据操纵语言 DML**  用于处理数据，包括数据检索、表中插入行、修改值、删除行等。

####数据操纵语言 DML

简单例子：

```SQL
SELECT TOP 10 Production.Product.Name
             ,Sales.SalesOrderDetail.LineTotal 
FROM Production.Product 
INNER JOIN Sales.SalesOrderDetail
   ON Production.Product.Productid = Sales.SalesOrderDetail.ProductID
WHERE Sales.SalesOrderDetail.SpecialOfferID = 1
ORDER BY Sales.SalesOrderDetail.LineTotal DESC
```

**基于集合的操作**

在处理SELECT时，会在内存中建立一个结构，以返回结果集。这个实际上就是一个有行有列的二维数组，称为“游标”，即是当前记录集的意思。SQL Server将数据处理成一个集合，而不是处理各个行。

**基于行的操作**

SQL Server也有将SELECT查询结果填到一个游标类型的变量中，然后遍历每一行。不过这与查询处理引擎的工作方式相反，只要有可能，就应该使用基于集合的结果。行级别的操作方式肯定有适用场合，以后再说。

**查询语法的基础**

查询语句并不单单只是SELECT，SELECT,UPDATE,DELETE,INSERT,都是查询。查询需要这些动词，下面简单的几个例子。

`SELECT * FORM PRODUCT` 这是从PRODUCT表中检索所有行所有列的值。

下面这条语句可以实现将所有产品的价格提升10%。

`UPDATE PRODUCT SET standardcost=standardcost*1.1`

T-SQL语言对语句的格式是非常宽容。一般对关键字采用大写，对表对象采用与数据库一致即可。采用格式化主题、缩进和逗号，可以让代码更容易阅读。例如：

```SQL
SELECT PC.Name AS Category
      ,PSC.Name AS SubCategory
      ,P.Name AS ProductName
      ,SOH.OrderDate
      ,SOD.OrderQty
--    ,SOD.UnitPrice
FROM   Sales.SalesOrderHeader AS SOH
    INNER JOIN Sales.SalesOrderDetail AS SOD 
       ON SOH.SalesOrderID = SOD.SalesOrderID 
    INNER JOIN Production.Product AS P 
       ON SOD.ProductID = P.ProductID
    INNER JOIN Production.ProductSubCategory AS PSC
       ON P.ProductSubCategoryID = PSC.ProductSubCategoryID 
    INNER JOIN Production.ProductCategory AS PC
       ON PSC.ProductCategoryID = PC.ProductCategoryID
WHERE SOH.OrderDate BETWEEN '1/1/2003' AND '12/31/2003'
ORDER BY PC.Name
        ,PSC.Name
        ,P.Name

```

**命名规范** 在创建对象时，应该避免使用空格或保留字作为对象名。

* **Pascal Case** 所有单词首个字母大写，直接连在一起，不用使用分隔符。(ProductName)
* **Camel Case** 除了第一个单词大写，其它字符全部小写。(Productname)
* **Hungarian Notation** 对象带有代码数据类型或范围的前缀，一边常用于程序代码。(VCHAT500Productname)
* **Lower-case，delimited**来自于不支持大小写混合的老数据库，传统习惯和兼容性的原因，至今仍广泛使用(product_name)

**注释脚本** 无论逻辑多么简单，都需要添加注释。

T-SQL注释分为两种

* **块注释：** 一般块注释常常用于头部，包括如下信息：脚本对象的名称；设计人员与程序员的名字；创建日期；修改日期；对象的作用和调试方法；验证与测试的批准注解。块注释以斜杠和至少一个星号开始，并以一个星号和斜杠结束，中间的所有文本都是注释。

```SQL
/*******************************************************************
* 查询聊天对话总数
* ALEXZENG 2012-01-08
* 测试完成
*******************************************************************/
```

* **行内注释：** 注释以两个 **--** 开头，查询解析器自动忽略后面的部分

```SQL
    -- Return all product history for defined time period
       SELECT PC.Name AS Category
             ,PSC.Name AS SubCategory
             ,P.Name AS ProductName
             ,SOH.OrderDate
             ,SOD.OrderQty
--           ,SOD.UnitPrice
       FROM   Sales.SalesOrderHeader AS SOH
       INNER JOIN Sales.SalesOrderDetail AS SOD 
          ON SOH.SalesOrderID = SOD.SalesOrderID 
       INNER JOIN Production.Product AS P 
          ON SOD.ProductID = P.ProductID
       INNER JOIN Production.ProductSubCategory AS PSC
          ON P.ProductSubCategoryID = PSC.ProductSubCategoryID 
       INNER JOIN Production.ProductCategory AS PC
          ON PSC.ProductCategoryID = PC.ProductCategoryID
       WHERE SOH.OrderDate BETWEEN @StartDate AND @EndDate
    ORDER BY PC.Name
        ,PSC.Name
        ,P.Name

--
```

####数据定义语言 DDL

|语句|描述|
|-*-|-----|
|CREATE|用来创建对象，包括数据库，表，视图，过程，触发器和函数等常见的数据库对象|
|ALTER|用来修改已有对象的结构|
|DROP|用来删除已有的对象。但有些对象是无法被删除的，例如对象是一个参加了关联的表，或者另一个对象对该对象有依赖。|

**创建表**

```SQL
CREATE TABLE Appointment
(AppointmentID int
,Description varchar(50)
,StartDateTime datetime
,EndDateTime datetime
,resource varchar(50) NULL
)
```

**创建视图**

视图只是一个经过优化的SELECT查询，它们并不真正保存数据，执行起来比较高效。可以配合其它编程对象完成一些好玩的事。

```SQL
CREATE VIEW vwProductByCategory
AS
SELECT PC.Name AS Category
      ,PSC.Name AS SubCategory
      ,P.Name AS Product
FROM   Production.ProductCategory PC
INNER JOIN Production.ProductSubcategory PSC
  ON PC.ProductCategoryID = PSC.ProductCategoryID 
INNER JOIN Production.Product P
  ON P.ProductSubcategoryID = PSC.ProductSubcategoryID; 

```

**创建存储过程**

存储过程可以配合其它编程对象完成对数据库插入，更新，删除表中的对象等。

```SQL
/******************************************************
*  Checks for existing Product record
*  If exists, updates the record.  If not,
*  inserts new record
******************************************************/
CREATE PROCEDURE spInsertOrUpdateProduct
  -- Input parameters -- 
   @ProductName nvarchar(50)
  ,@ProductNumber nvarchar(25)
  ,@StdCost money
AS
  IF EXISTS(SELECT * 
            FROM Production.Product 
            WHERE ProductNumber = @ProductNumber)
     UPDATE Production.Product 
      SET NAME = @ProductName, StandardCost = @StdCost
     WHERE ProductNumber = @ProductNumber
  ELSE
     INSERT INTO Production.Product 
     (Name, ProductNumber, StandardCost)
     SELECT @ProductName
           ,@ProductNumber
           ,@StdCost


```
**创建触发器**

创建触发器和创建存储过程的过程类似，它对表中的数据被执行（插入、更新、删除）时被执行。

```SQL
/******************************************************
   Checks for existing sales orders using
   the product being deleted.
   Prevents deletion if orders exist.
******************************************************/
CREATE TRIGGER tr_DelProduct
ON Production.Product
FOR DELETE
AS
  IF (SELECT Count(*) 
      FROM Sales.SalesOrderDetail 
      INNER JOIN Deleted 
        ON SalesOrderDetail.ProductID = Deleted.ProductID) > 0
  BEGIN
    RAISERROR ('Cannot delete a product with sales orders',14,1)
    ROLLBACK TRANSACTION
    RETURN
  END


```

**创建自定义函数**

创建自定义函数与创建存储过程的过程类似，用户创建自定义函数可以实现定制的业务逻辑。通常在视图和存储过程对函数进行调用。

```SQL
/**********************************************************
 Returns a date representing the last date
 of any given month.
**********************************************************/
CREATE Function dbo.fn_LastOfMonth(@TheDate datetime) 
Returns datetime
AS
BEGIN
 DECLARE @FirstOfMonth  datetime
 DECLARE @DaysInMonth int
 DECLARE @RetDate datetime
 SET @FirstOfMonth = DATEADD(mm, DATEDIFF(mm,0,@TheDate), 0)
 SET @DaysInMonth = DATEDIFF(d, @FirstOfMonth, DATEADD(m, 1, @FirstOfMonth))
 RETURN  DATEADD(d, @DaysInMonth - 1, @FirstOfMonth)
 END


--

IF NOT EXISTS (SELECT * FROM sys.objects 
               WHERE object_id = OBJECT_ID('Person.Address') 
                 AND type in (N'U'))
BEGIN
  CREATE TABLE Person.Address(
   AddressID int IDENTITY(1,1) NOT NULL
  ,AddressLine1 nvarchar(60) NOT NULL
  ,AddressLine2 nvarchar(60) NULL
  ,City nvarchar(30) NOT NULL
  ,StateProvinceID int NOT NULL
  ,PostalCode nvarchar(15) NOT NULL
  ,Rowguid uniqueidentifier ROWGUIDCOL  NOT NULL
  ,ModifiedDate datetime NOT NULL)
END

```

####数据控制语言 DCL

简而言之，DCL是由三个用来管理特定数据库上的用户与角色的安全权限的命令组成的：

* GRANT 用于授予用户或角色权限的集合。
* DENY 用来显示权限限制的集合。
* REVOKE 用来撤销对象上的权限集合。

<h2 id="sjjs">数据检索</h2>

####SELECT 语句

SELECT语句由4个子句或者组件组成。

|子句|解释|
|--|----|
|SELECE|后面跟一组列的列表，表示要返回的列。如果是*号，就代表返回所有的列。|
|FROM|后面跟着表或者视图的名字，或者带有连接表达式的多个表。|
|WHERE|后面跟着过滤规则。|
|ORDER BY|后面跟一组列的列表，表示要对这些列进行排序。|

*SELECT和FROM是必须的，其他子句是可选的。*

**列的选择**

```SQL
SELECT Name,standardCost,Color
FROM Production.Product
```

列的选择一般可以用*来代表选择所有的列，但是消耗资源太大。一般不建议这样选择。

**多表查询**
```SQL
SELECT Production.Product.ProductID
      ,Production.Product.Name
      ,Production.Product.Color
      ,Production.Product.StandardCost
      ,Production.Product.ListPrice
      ,Production.ProductModel.ProductModelId
      ,Production.ProductModel.Name
FROM Production.Product
INNER JOIN Production.ProductModel
ON Production.Product.ProductModelID = Production.ProductModel.ProductModelID

```

这样的代码没有什么问题，但是它会返回两个Name的值，我们就要为它设置别名

**列的别名**

```SQL
SELECT Production.Product.ProductID
      ,Production.Product.Name AS Product
      ,Production.Product.Color
      ,Production.Product.StandardCost
      ,Production.Product.ListPrice
      ,Production.ProductModel.ProductModelId
      ,Production.ProductModel.Name AS Model
FROM Production.Product
INNER JOIN Production.ProductModel
ON Production.Product.ProductModelID = Production.ProductModel.ProductModelID
```

给列起别名的原因有很多：更好理解、描述性更强、或者为其它程序做兼容性等等。

为列起别名的有以下3种：

```SQL
SELECT ListPrice - StandardCost AS Margin
FROM Production.Product
SELECT ListPrice - StandardCost Margin
FROM Production.Product
SELECT Margin = ListPrice - StandardCost
FROM Production.Product
```

一般我们选择最后理解，最具描述性的第一种。

**被计算和继承的列**

比较常见列的别名是在表达式或者计算中产生的，例如下面的例子，我们用两个值相乘，得出一个新的别名：

```SQL
SELECT SalesOrderID
      ,ProductID
      ,UnitPrice * OrderQty As PurchasePrice
FROM Sales.SalesOrderDetail
```

还可以用生日和当前日期算出雇员的岁数，具体实例如下：

```SQL
SELECT NationalIDNumber
      ,BirthDate
      ,DATEDIFF(YY, BirthDate, GETDATE()) As Age
FROM HumanResources.Employee
```

####行的过滤

过滤功能基本上由**WHERE**子句来完成。

最简单的过滤语句：

```SQL
SELECT * FROM Production.Product
WHERE Color = 'Black' 
```

* 比较运算符

|运算符|描述|
|------|----|
|=|等于|
|!=或者<>|不等于|
|>|大于|
|<|小于|
|!>|不大于|
|!<|不小于|
|>=|大于等于|
|<=|小于等于|
|Like|用于对字符型的值进行通配符匹配|

其它都比较容易理解，LIKE运算符比较复杂一点。要注意的是LIKE不表示类似，而表示**是**。运算符可以使用通配符来比较字符串。

|通配符|说明|
|------|----|
|%|0个或多个字符匹配|
|_|单个字符|
|[]|在指定范围或集合内的单个字符|
|[^]|不在指定范围或集合内的单个字符|

一般我们通配符都在开头和结尾出现，这样就可以搜索出包含某个字符。

```SQL
SELECT * FROM SlateGravel
WHERE LastName LIKE '%sto%'
```

要找出单个字符在指定范围或集合中的值，就用中括号：

```SQL
SELECT * FROM SlateGravel
WHERE FirstName LIKE 'D[ao]n'
```

要找一个范围内搜索，例如从字母a到字母o:
```SQL
SELECT * FROM SlateGravel
WHERE FirstName LIKE 'D[a-o]n'
```

要搜索不含某些字符的，可以使用^符合
```SQL
SELECT * FROM SlateGravel
WHERE FirstName LIKE 'D[^o]n'
```

LIKE运算符还可以和NOT关键字搭配，有更强大的功能：

```SQL
SELECT * FROM SlateGravel
WHERE FirstName NOT LIKE  'Dan'
```

* 逻辑比较

每个SQL比较和逻辑表达式只能得出一个结果：真或假。

1.AND运算符

返回结果是在AND的两端两个条件必须同时满足的结果。
如：

```SQL
SELECT ProductID, Name, ListPrice
FROM Production.Product 
WHERE ProductSubCategoryID = 1 AND ListPrice < 1000
```

2.OR运算符

返回结果是在AND的两端两个条件有满足其中一个条件的结果。

```SQL
SELECT ProductID, Name, ListPrice
FROM Production.Product 
WHERE ProductSubCategoryID = 1 OR ListPrice < 1000
```

3.NOT运算符
NOT运算符不是独立的，它是一个可以放在任何逻辑表达式前面的修饰符，能得到与结果相反的结果。


```SQL
SELECT ProductID, Name, ListPrice
FROM production.Product 
WHERE NOT ProductSubCategoryID = 2 
```

####强大的NULL

Null不等于任何值，甚至不等于它自己。可用一个特殊的符号来**IS运算符**测试Null,可以用IS Null来表示。

将没有含颜色属性的产品列表列出。

```SQL
SELECT ProductID, Name, Color
FROM Production.Product
WHERE Color IS NULL
```

####被拓展的过滤技术

如果想返回1962年至1985年之间出生的雇员，如果按照我们刚刚学到的AND运算符，那么语句就应该是这样写的：

```SQL
SELECT NationalIDNumber, LoginID
FROM HumanResources.Employee
WHERE BirthDate >= '1962-1-1'
  AND BirthDate <= '1985-12-31'
```

**BETWEEN运算符**

BETWEEN运算符用法：

```SQL
SELECT NationalIDNumber, LoginID
FROM HumanResources.Employee
WHERE BirthDate BETWEEN '1962-1-1' AND '1985-12-31'
```

####IN()函数

这个函数可以把一个字符与列表中的数值进行匹配，从而使查询短小、易读、节省开发人员的时间。

如果按照我们上面学过的，多匹配的代码需要这样写：

```SQL
SELECT ProductID
      ,Name AS Product
FROM  Production.Product
WHERE ProductSubCategoryID = 1
   OR ProductSubCategoryID = 2
   OR ProductSubCategoryID = 3
```

用IN函数可以省事多了：

```SQL
SELECT ProductID
      ,Name AS Product
FROM  Production.Product
WHERE ProductSubCategoryID IN (1,2,3)
```

####运算符优先级

逻辑运算符的优先级别顺序是：NOT,AND,OR.

**使用括号**

```SQL
SELECT Name
       , ProductNumber
       , ListPrice
       , ProductSubCategoryID
FROM Production.Product
WHERE (ProductSubCategoryID = 1 AND ListPrice > 1000)
   OR (ProductSubCategoryID = 2 AND ListPrice > 500) 
```

####结果排序

在默认的情况下，记录是以某种合理顺序返回。行可以以任何的组合为序进行排序。如果希望行以某种顺序返回，就必须使用ORDER BY子句强制排序。ORDER BY总是放在WHERE子句后面，使用ASC、DESC关键字可以指定排序。

下面没有指定ASC，两个返回是一样的

```SQL
SELECT Name AS Product
      ,ListPrice
FROM Production.Product
WHERE ListPrice > 0
ORDER BY ListPrice

--

SELECT Name AS Product
      ,ListPrice
      ,StandardCost
FROM Production.Product
WHERE ListPrice > 0
ORDER BY ListPrice ASC

```

如果指定降序，那么显示的结果就不一样。

```SQL
SELECT Name AS Product
      ,ListPrice
      ,StandardCost
FROM Production.Product
WHERE ListPrice > 0
ORDER BY ListPrice DESC
```

也可以对多列进行排序，先排完前面的列，再排后面的列。例如：

```SQL
SELECT Name AS Product
      ,ListPrice
      ,StandardCost
FROM Production.Product
WHERE ListPrice > 0
ORDER BY ListPrice, StandardCost
```

**排在前面的值**

```SQL
SELECT TOP 10 Name, ListPrice
FROM Production.Product
ORDER BY ListPrice DESC
```

这种查询结果将是返回绝对的10行，假如你想要取得的结果是前10个最高价格的产品结果集，只要加上WITH TIES，那么返回的行数将有可能不止10行。

```SQL
SELECT TOP 10 WITH TIES Name, ListPrice 
FROM Production.Product 
ORDER BY ListPrice DESC
```

此外，还可以用百分比来返回整个结果集的百分数，对记录进行计算，返回一个最接近的百分比。

<h2 id="sqlhs">SQL函数</h2>

SQL 函数：返回结果集之后，就需要对数据做些有用的处理。包括：执行计算与数学运算、转换数据、解析数值、组合值和聚合一个范围内的值。

|函数类别|作用|
|--------|----|
|聚合函数|返回一个标量值，表示在某个值域上的聚合，应用于特定的聚合选择或者汇总 |
|配置变量|返回SQL Server执行环境的信息，这些信息可用于给对象编程 |
|转换| 将值从一种数据类型转换为另一种类型。也用于字符格式化为日期、时间与数值|
|游标|在迭代游标时，用过程方式遍历结果集中的行 |
|日期和时间|从日期值中解析出日期与时间部分，也用于对日期/时间值进行比较和操作 |
|数学|执行各种通用或专用的数学运算，可用于代数、三角、统计、估算与财政运算。 |
|元数据|返回SQL Server配置细节信息，以及服务器和数据库设置细节信息 |
|等级|在有序的结果中枚举前几个值 |
|安全性|返回SQL Server用户的角色成员与权限信息，也包含一组管理事件与跟踪的函数。 |
|字符串处理|用于解析、替换、操纵字符串 |
|系统|执行许多不同任务的工具函数。包括值比较、值类型测试。这个类别也包罗了其他功能。 |
|系统统计|揭示数据库系统消耗与环境信息的管理工具 |

**函数的组成**

函数的目标是返回一个值。大多数函数都返回一个标量值，代表一个数据单元或一个简单值。

**一个论点**

函数可以有任意个参数，有些参数是必须的，有些参数是可选的。


**确定性函数**

如果函数的输出只与输入参数的值相关，而与其他外部因素无关，这个函数就是确定性函数。如果函数的输出基于环境条件，或者产生随机，或者依赖结果的算法，这个函数就是非确定性的。

**在函数中使用用户变量**

变量即可用于输入，也可用于输出。用户变量以@符号开头，用于声明为特定的数据类型。

下例将一个int的变量@MyNumber传递给SQRT()函数：
```SQL
DECLARE @MyNumber int
SET @MyNumber = 144
SELECT SQRT(@MyNumber)
```
输出结果是12,144开方得出的值。

* **1.用SET给变量赋值**
以下例子使用int变量@MyResult,来捕获该函数返回值。

```SQL
DECLARE @MyNumber int, @MyResult int
SET @MyNumber = 144
-- Assign the function result to the variable:
SET @MyResult = SQRT(@MyNumber)
-- Return the variable value
SELECT @MyResult 
```

* **2.用SELECT给变量赋值**
使用SELECT来替换SET命令的优点是可以在一个操作里面给多个变量赋值。

```SQL
DECLARE @MyNumber1 int, @MyNumber2 int, @MyResult1 int, @MyResult2 int
SELECT @MyNumber1 = 144, @MyNumber2 = 121
-- Assign the function result to the variable:
SELECT @MyResult1 = SQRT(@MyNumber1), @MyResult2 = SQRT(@MyNumber2) 
-- Return the variable value
SELECT @MyResult1, @MyResult2
```

**在查询中使用函数**

函数经常和查询表达式结合使用来修改列值。

```SQL
SELECT Title,Number,YEAR(BirthDate) AS BirthYear
FROM Employee
```

BirthDate的值传递给YEAR()函数生成别名为BirthYear的列。

###嵌套函数###

我们需要的功能常常不能仅由一个函数来实现。例如：GETDATE()仅仅是取得当前的时间，但是时间没有经过格式化，CONVERT()才是格式化函数功能，可以将GETDATE()函数输出作为CONVERT()输入参数。

`SELECT CONVERT(varchar(20), GETDATE(), 101) `

###聚合函数

聚合函数的意义就是：从全部数据中提取一种趋势的值或者汇总值。
```
SELECT AVG(15)
SELECT SUM(15)
SELECT MIN(15)
SELECT MAX(15)
```
他们都返回15，对于同一个值求平均、求和、求最小值、求最大值的结果都是那个值，那么对一个值进行计数，返回的是什么呢？

`SELECT COUNT(15)`返回为1

* **AVG()函数**
AVG()函数用于返回一组数值中所有非空数值的平均值。

* **COUNT()函数**
COUNT()函数用于返回一个列内所有非空值的个数，这是一个整型值。

`SELECT COUNT(Score) FROM GymEvent WHERE Gymnast='Sammi'`结果返回为1，她的平衡木成绩为NULL。


`SELECT COUNT(*) FROM GymEvent WHERE Gymnast='Sammi'`结果返回为2，COUNT(*)会忽略NULL.

* **MIN() MAX()函数**
MIN()函数用于返回一个列范围内的最小非空值；MAX()函数用于返回一个列范围内的最大非空值；

需要注意的是INT和VARCHAR类型，它们的最小值最大值在位数不一样的情况下，他们的结果不是一致的。

* **SUM()函数**
SUM()函数是最常用的聚合函数之一，返回一个列内所有非空值的总和。

###配置变量

* **@@ERROR变量**
在执行语句没有错误时，@@ERROR的值为0。在成功检索到@@ERROR的值后，@@ERROR值将有返回0.如果希望检索到更多错误信息，可以使用以下代码：

```SQL
SELECT 5 / 0
SELECT * FROM master.dbo.sysmessages WHERE error = @@ERROR
```

最终显示结果：
消息 8134，级别 16，状态 1，第 1 行
遇到以零作除数错误。

###转换函数

* **CAST()函数**
CAST()函数的参数是一个表达式，它包括用AS关键字分隔的源值和目标数据类型。

以下的例子可以将文本字符串'123'转换为int:`SELECT CAST('123' AS int)`

有小数点可以这样转换：`SELECT CAST('123.4' AS decimal(9,2))`

我们来执行一下Product数据表查询，我们希望最终效果是将ProductNumber, ProductLine, ProductModelID这三列用'-'串起来组成一个新列。下面用普通的查询语言当然是不可能实现的。

```SQL
SELECT ProductNumber, ProductLine, ProductModelID 
FROM Production.Product
WHERE ProductSubCategoryID < 4
```

最终完成的例子是：

```SQL
SELECT ProductNumber 
       + '-' 
       + ProductLine 
       + '-' 
       + CAST(ProductModelID AS char(4)) AS BikeSerialNum
FROM Production.Product
WHERE ProductSubCategoryID < 4
```

因为ProductModelID是一个int，如果不对它进行转换的话，程序就会报错。

* **CONVERT()函数**
对于简单的类型转换，CONVERT()函数和CAST()函数功能相同，只是语法不同。

```SQL
SELECT CONVERT(int, '123')
SELECT CONVERT(decimal(9,2), '123.4')
```
可以得到和上面一样的结果。

CONVERT()函数还有一些改进的功能，它可以返回经过格式化的字符串，而且可以把日期值格式化很多形式，下面列出常用的日期格式

|格式序号|年份位数|小时格式|说明|示例|
|--------|--------|--------|----|----|
|0|2|12|默认|Apr 25 05 1:05pm|
|1|2| |美国|04/24/05|
|8| |24|定制：仅时间|13:05:35|
|10|2| |美国|04-25-05|
|11|2| |亚洲|05/04/25|
|100|4|12|默认|Apr 25 2005 1:05pm|
|101|4| |美国|04/24/05|
|111|4| |亚洲|2005/04/25|
|114| |24|定制时间，毫秒|13:05:35:123|

使用方法如下：
```SQL
SELECT '现在日期时间是:' + CONVERT(varchar(50), GETDATE(),1)
SELECT '定制毫秒时间是:' + CONVERT(varchar(50), GETDATE(),114)
```

格式代码0,1,2也可以用于数字类型，他们对小数与千位分隔符格式产生影响。以下例子使用格式代码为0：

```SQL
DECLARE @Num Money
SET @Num = 1234.56
SELECT CONVERT(varchar(50), @Num, 0)
```

* 格式代码为0，输出值为**1234.56**
* 格式代码为1，输出值为**1,234.56**
* 格式代码为2，输出值为**1234.5600**

* **STR()函数**
这是一个将数字转换为字符串的快捷函数。这个函数有三个参数：原数值、总长度、小数位数。`SELECT STR(123.4, 8, 4) `返回值是：**123.4000**。`SELECT STR(123.456789, 8, 4) `返回值是：**123.4568**,原值加上小数点超过8位，STR()对最后一位进行四舍五入得出最后的值。如果指定返回总长度大于整数位数、小数点、小数位数之和，结果值将用空格在值的左边填充。

###游标函数与变量

游标可以处理多行数据，在过程循环中一次访问一行。

* **CURSOR_STATUS()函数**
这个函数返回一个整型值，表示传递给这个函数的游标类型变量的状态。

|返回值|说明|
|------|----|
|1|游标包含一行或多行(动态游标包含0行或者多行)|
|0|游标不包含行|
|-1|游标已关闭|
|-2|游标未分配|
|-3|游标不存在|

* **@@CURSOR_ROWS()全局变量**
这个变量是一个整型值，表示在当前连接中打开的游标中的函数。根据游标类型，这个值也能不代表结果集中的实际行数。

* **@@FETCH_STATUS全局变量**
这个变量是一个标记，用于表示当前游标指针的状态。这个变量主要用来判断某行是否存在。

###日期函数

日期函数可以操作DateTime与SmallDateTime数据类型的值。

|数据类型|存储空间需求|日期范围|
|--------|------------|--------|
|datetime|8字节|1/1/1753--12/31/9999|
|smalldatetime|4字节|1/1/1900--6/6/2079|

* **DATEADD()函数**
DATAADD函数用于日期/时间值加上日期单位间隔。例如
`SELECT DATEADD(Day, 90, '4-29-2007') `得到的值是：*2007-07-28 00:00:00.000*

时间间隔参考表

|时间间隔|时间间隔参数值|
|--------|--------------|
|年|Year,yyyy,yy|
|季度|Quarter,qq,q|
|月|Month,mm,m|
|一年内的天|DayOfYear,dy,y|
|天|Day,dd,d|
|星期|Week,wk,ww|
|小时|Hour,hh|
|分钟|Minute,mi,n|
|秒|Second,ss,s|
|毫秒|Millisecond,ms|

可以将CONVERT()函数和DATEADD()函数组合在一起，来对1989年9月8日的9个月前的日期进行格式化。

`SELECT CONVERT(varchar(20), DATEADD(m, -9, '9-8-1989'), 111) `返回的值：*1988/12/08*

* **DATEDIFF()函数**
DATEADD()和DATEDIFF()函数可以看作是一对表兄弟，像+和-的关系一样。在等式的两端有4个元素：起始日期、时间间隔、差值、最终日期。如果在DATEADD()函数中使用起始日期、一个整型值和一个时间间隔，就可以返回与起始日期相关的最终日期值。如果提供了起始日期，时间间隔和最终日期，就可以用DATEDIFF()函数返回差值。

下面的查询从表中返回姓名和生日。

```SQL
SELECT LastName
      ,FirstName
      ,BirthDate
      ,DATEDIFF(YEAR, BirthDate, GETDATE()) AS ApproximateAge
FROM SlateGravelEmployee
ORDER BY LastName 
```

* **DATEPART()与DATENAME()函数**
这两个函数用于返回DateTime与SmallDateTime值的日期部分。DATEPART()返回整型值，DATENAME()返回包含描述性文字的字符串。

```SQL
SELECT DATEPART(month, '4-29-1988') 
SELECT DATENAME(month, '4-29-1988')
```
上面返回4，下面返回04

* **GETDATE()与GETUTCDATE()函数**
这两个函数都用于返回datetime类型的当前日期和时间。
GETUTCDATE()返回一个格林威治标准时间。

```SQL
SELECT GETDATE()
SELECT GETUTCDATE()
```

* **DAY()、MONTH()和YEAR()函数**
这三个函数分别返回一整数表示的DateTime或者SmallDateTime类型的日、月、年。可以创建出个性化的日期字符串。
```SQL
SELECT '年: ' + CONVERT(varchar(4), YEAR(GETDATE())) 
   + ', 月: ' + CONVERT(varchar(2), MONTH(GETDATE())) 
   + ', 日: ' + CONVERT(varchar(2), DAY(GETDATE()))
```

###字符串操纵函数

字符串函数可以解析、替换、操纵字符型值。

* **ASCII()、CHAR()、UNICODE()、NCHAR()函数。**
这四个函数是相似的，它们都可以在字符和脂肪的标准数字表示之间转换。

* **CHARINDEX() 和PATINDEX()函数**
CHARINDEX()是原始的SQL函数，用于来寻找字符串中某字符第一次出现的位置。

`SELECT CHARINDEX('sh', 'Washington') `返回值是3.

PATINDEX()函数和CHARINDEX()函数类似，它执行相同的操作，但方法稍有不同，该函数增加了对通配符LIKE的支持。这个函数也可以和netxt,nchar,nvarchar等大字符类型一起使用。

```SQL
SELECT PATINDEX('%M_rs%', 'The stars near Mars are far from ours') 
```

* **LEN()函数**
LEN()函数用于返回一个代表字符串长度的整型值。经常与其他函数一起使用，来应用业务规则。以下例子将日期和月份提取出来，如果他们是单位数，将在前面加0,程序如下：

```SQL
DECLARE @MonthChar varchar(2), @DayChar varchar(2), @DateOut char(8)
SET @MonthChar = CAST(MONTH(GETDATE()) AS varchar(2))
SET @DayChar = CAST(DAY(GETDATE()) AS varchar(2))
-- Make sure month and day are two char long:
IF LEN(@MonthChar) = 1
  SET @MonthChar = '0' + @MonthChar
IF LEN(@DayChar) = 1
  SET @DayChar = '0' + @DayChar
-- Build date string:
SET @DateOut = @MonthChar + @DayChar + CAST(YEAR(GETDATE()) AS char(4))
SELECT @DateOut AS OutputDate
```

* **LEFT() 与 RIGHT()函数**
这两个函数是类似的，它们都返回一定长度的子字符串。这两个函数区别一个是返回最左边几个字符，一个是返回最右边的字符。

应用：例如在一个英文名中，如果想要返回全名中的名字或者姓氏，可以配合PATINDEX()函数或者CHARINDEX()函数来取得定位空格，然后再LEFT()取得名字。

例：

```SQL
DECLARE @FullName varchar(25), @SpaceIndex tinyint
SET @FullName = 'George Washington'
-- Get index of the delimiting space:
SET @SpaceIndex = CHARINDEX(' ' , @FullName)
-- Return all characters to the left of the space:
SELECT LEFT(@FullName, @SpaceIndex -  1)
```

* **SUBSTRING()函数**
能够从字符串的一个位置，往右数若干字符，返回一个特定长的字符。

现在可以对上面的代码对比一下了：

```SQL
DECLARE @FullName varchar(25)
SET @FullName = 'George Washington'
-- Return first name:
SELECT SUBSTRING(@FullName, 1, CHARINDEX(' ', @FullName) - 1) AS FirstName, 
       SUBSTRING(@FullName, CHARINDEX(' ', @FullName) + 1, LEN(@FullName)) 
       AS LastName
```

* **LOWER()与UPPER()函数**
这两个函数非常容易理解，它们将字符串全部返回大写或者小写。

下面简单演示一下，通过这两个自定义函数，对英文人名进行规范化的工作。

```SQL
CREATE FUNCTION dbo.fn_FixLastName ( @LastName varchar(25) )
RETURNS varchar(25)
AS
BEGIN
  DECLARE   @SpaceIndex tinyint
         , @AposIndex tiinyint
         , @ReturnName varchar(25)
  -- Find space in name:
  SET @SpaceIndex = CHARINDEX(' ', @LastName)
  -- Find literal ‘ in name:
  SET @AposIndex = CHARINDEX('''', @LastName)
  IF @SpaceIndex > 0             -- Space: Capitalize first & substring
           SET @ReturnName = UPPER(LEFT(@LastName, 1)) 
         + LOWER(SUBSTRING(@LastName, 2, @SpaceIndex - 1)) 
         + UPPER(SUBSTRING(@LastName, @SpaceIndex + 1, 1)) 
         + LOWER(SUBSTRING(@LastName, @SpaceIndex + 2, LEN(@LastName)))
  ELSE IF @AposIndex > 0          -- Apostrophe: Cap first & substring
           SET @ReturnName =  UPPER(LEFT(@LastName, 1)) 
         + LOWER(SUBSTRING(@LastName, 2, @AposIndex - 1)) 
         + UPPER(SUBSTRING(@LastName, @AposIndex + 1, 1)) 
         + LOWER(SUBSTRING(@LastName, @AposIndex + 2, LEN(@LastName)))
  ELSE                            -- No space: Cap only first char.
           SET @ReturnName =  UPPER(LEFT(@LastName, 1))  
         + LOWER(SUBSTRING(@LastName, 2, LEN(@LastName)))
RETURN @ReturnName
END
```

那么如何对这个自定义函数进行使用呢？

```SQL

--创建一个临时表
CREATE TABLE #MyIrishFriends (FirstName varchar(25), LastName varchar(25) )
INSERT INTO #MyIrishFriends (FirstName, LastName) SELECT 'James', 'O''grady'
INSERT INTO #MyIrishFriends (FirstName, LastName) SELECT 'Nancy', 'o''brian'
INSERT INTO #MyIrishFriends (FirstName, LastName) SELECT 'George', 'MC kee'
INSERT INTO #MyIrishFriends (FirstName, LastName) SELECT 'Jonas', 'mc intosh'
INSERT INTO #MyIrishFriends (FirstName, LastName) SELECT 'Florence', 'MC BRIDE'


SELECT FirstName, dbo.fn_FixLastName(LastName) AS LastName 
FROM #MyIrishFriends 
```

* **LTRIM()与RTRIM()函数**
这两个函数分别返回将字符串的左边和右边的空白修剪之后的字符串。

```SQL
DECLARE @Value1 char(10), @Value2 char(10)
SET @Value1 = 'One'
SET @Value2 = 'Two'
SELECT @Value1 + @Value2
SELECT CONVERT(varchar(5), LEN(@Value1 + @Value2)) + ' characters long. '
SELECT RTRIM(@Value1) + RTRIM(@Value2) 
SELECT CONVERT(varchar(5), LEN(RTRIM(@Value1) + RTRIM(@Value2))) 
       + ' characters long trimmed. '
```
得出的值为：

```SQL
One       Two       
----------------
13 characters long. 
--------------------
OneTwo
----------------------
6 characters long trimmed. 
```

* **REPLACE()函数**
这个函数可以把字符串的某个字符或某个子字符替换成另一个字符或者子字符，该函数可以用于全局查找和替换工具中。

```SQL
DECLARE @Phrase varchar(1000)
SET @Phrase = 'I aint gunna use poor grammar when commenting script and I aint gunna complain about it. '
SELECT REPLACE(@Phrase, 'aint', 'am not')
```

* **REPLICATE()与SPACE()函数**
在需要将一些字符重复填充进一个字符串时，这两个函数是很有用的。

`SELECT FullName + REPLICATE('*', 20 - LEN(FullName))
FROM #MyNames`
输出结果就显示：`MIKE JASON**********`

SPACE()这是返回空格填充。

* **REVERSE()函数**
这个函数用于将字符串的字符颠倒过来。

* **STUFF()函数**
这个函数可以将字符串的一部分替换为另一个字符串。它本质上是将一个字符串以特定的长度插入另一个字符串中的特定位置上。这对于原值与目的值长度不一样的字符串替换是很有用的。

```SQL
SELECT STUFF('Please submit your payment for 99.95 immediately. ', 32, 5, '109.95')
```

* **QUOTENAME()函数**
这个函数和SQL Server对象名组合使用，以将结果传递给表达式，它只用与给输入的字符串加一对方括号。

```SQL
SELECT QUOTENAME(COLUMN_NAME) AS ColumnName
FROM INFORMATION_SCHEMA. COLUMNS
```

###数字函数

|函数|说明|
|----|----|
|ABS()|返回一个绝对值|
|ACOS()|计算一个角的反余弦值，以弧度表示|
|ASIN()|计算一个角的反正弦值，以弧度表示|
|ATAN()|计算一个角的反正切值，以弧度表示|
|ATN2()|计算两个值的反正切，以弧度表示|
|CEILING()|返回大于或等于一个数的最小整数|
|COS()|计算一个角的正弦值，以弧度表示|
|COT()|计算一个角的余切值，以弧度表示|
|DEGREES()|将一个角从弧度转换为角度|
|EXP()|指数运算|
|FLOOR()|返回小于或等于一个数的最大整数|
|LOG()|计算以2为底的自然对数|
|LOG10()|计算以10为底的自然对数|
|PI()|返回以浮点数表示的圆周率|
|POWER()|幂运算|
|RADIANS()|将一个角转换为弧度|
|Rand()|返回以随机数算法算出一个小数，可以接收一个可选的种子值|
|ROUND()|对一个数进行四舍五入运算，使其具备特定的精度|
|SIGN()|根据参数是正是负，返回-1或者1|
|SIN()|计算一个角的正弦值，以弧度表示|
|SQRT()|返回一个数的平方根|
|SQUARE()|返回一个数的平方|
|TAN()|计算一个角正切值，以弧度表示|

###元数据函数

这是一些工具函数，它们返回SQL Server配置细节、服务器与数据库设置细节的信息。

###排列函数

* **ROW_NUMBER()函数**
根据作为参数传递给这个函数的ORDER BY子句的值，返回一个不断递增的整数值。

```SQL
SELECT 
    ProductCategoryID
  , Name
  , ROW_NUMBER() OVER (ORDER BY Name) AS RowNum
FROM Production.ProductCategory
ORDER BY Name
```

* **RANK()与DENSE_RANK()函数**
这两个函数与ROW_NUMBER()函数类似，它们都返回一个基于ORDER BY子句的值。不过这些值不一定永远都是唯一的。PANK()函数保留列表中的位置序号，对于每个重复的值，该函数会跳过下面与其相邻的值。

```SQL
SELECT 
    ProductID
   ,Name
   ,ListPrice
   ,RANK() OVER (ORDER BY ListPrice DESC) AS Rank
FROM Production.Product
ORDER BY Rank
```

得出的结果如下表：

|ProdutID|Name|ListPrice|Rank|
|--------|----|---------|----|
|10011|PC_tt_001|100.00|1|
|10012|WWWCT|1|100.00|
|10013|EOO_002|100.00|1|
|10014|keyMMP|99.97|4|
|10015|OK_PP1|98.90|5|

DENSE_RANK()函数，但是它不会跳过任何数字。

```SQL
SELECT 
    ProductID
   ,Name
   ,ListPrice
   ,DENSE_RANK() OVER (ORDER BY ListPrice DESC) AS Rank
FROM Production.Product
ORDER BY Rank
```

* **NTILE(n)**
这个函数也用于对结果进行排列，并返回一个整型的排列值，但是它不会对结果以唯一的排列顺序进行枚举，二十将结果切分为有些数量的排列组。

###安全函数

与安全相关的函数返回SQL Server用户的角色成员和权限信息。

###系统函数与系统变量

工具函数，包括值比较、值类型测试等功能。

* **COALESCE()函数**
从逗号分隔的表达式列表中返回第一个非空值。

假设一个产品表，商品的价格有促销价、销售价、定价，我们可以设计一个临时表。

```SQL
CREATE TABLE #ProductPrices (ProductName varchar(25), SuperSalePrice Money NULL, SalePrice Money NULL, ListPrice Money NULL)
GO
INSERT INTO #ProductPrices VALUES('Standard Widget', NULL, NULL, 15.95)
INSERT INTO #ProductPrices VALUES('Economy Widget', NULL, 9.95, 12.95)
INSERT INTO #ProductPrices VALUES('Deluxe Widget', 19.95, 20.95, 22.95)
INSERT INTO #ProductPrices VALUES('Super Deluxe Widget', 29.45, 32.45, 38.95)
INSERT INTO #ProductPrices VALUES('Executive Widget', NULL, 45.95, 54.95)
 GO
```

然后我们按促销价、销售价、定价排列，用COALESCE()排列，就可以将最低价格的非空值列出来

```SQL
SELECT ProductName, COALESCE(SuperSalePrice, SalePrice, ListPrice) AS CurrentPrice
FROM #ProductPrices
```

* **DATALENGTH()函数**
返回存储或处理一个值所需的字节数。对于字符串来说，这个函数返回值域LEN()函数相同，对于其他类型就不一定相同了。

###系统统计变量
用于确定数据库系统使用信息与环境信息的管理工具。


<h2 id="jhyfz">聚合与分组</h2>

* **使用聚合函数**

|函数|说明|
|----|----|
|COUNT()|计算指定列所有非空值的个数，可以用COUNT(*)返回所有行的个数。返回类型为int|
|COUNT_BIG()|和COUNT()一样，但返回的数据类型为biging，只有表中包含的数据超过20亿行时，才需要用这个函数|
|SUM()|返回某范围内所有非空值的总和，返回的数据类型和数字列的类型相同。|
|AVG()|返回某范围内所有非空值的平均值，返回的数据类型和数字列的类型相同。|
|MIN()|返回某范围内最小的非空值。可以用于任何可排序的数据类型|
|MAX()|返回某范围内最大的非空值。可以用于任何可排序的数据类型|
|STDEV()|返回某数字范围内所有的非空值的简单标准偏差。无论列是什么类型，返回的数据类型为float|
|STDEVP()|返回某数字范围内所有的非空值的样本总体标准偏差。无论列是什么类型，返回的数据类型为float|
|VAR()|返回某数字范围内所有的非空值的简单方差。无论列是什么类型，返回的数据类型为float|
|VARP()|返回某数字范围内所有的非空值的总体方差。无论列是什么类型，返回的数据类型为float|
|CHECKSUM_AGG()|返回聚合范围内的值的校验和，用于比较两个范围的值，确定他们是否相同。该函数的返回值一般仅用于比较另一个校验和|

###COUNT()函数

如果只需确定表中有多少个记录，或者有多少个记录具有相同的属性值，就可以COUNT()函数，它能够计算某列的值范围内的行或者非空的个数。由于不必考虑列的数据；类型，所以这个函数可以用于任何类型的数据。

假如，我们对产品表进行查询：
`SELECT COUNT(*) FROM Production.Product`

返回表包含504行。现在我们对Color进行查询：
`SELECT COUNT(Color) FROM Production.Product`

返回计数256行，选择，把DISTINCT关键字加进去
`SELECT COUNT(DISTINCT Color) FROM Production.Product`

由于很多产品的颜色是相同的，那么返回的计数只有9条记录。

DISTINCT 修饰符可以用于任何聚合函数。

###SUM()函数
返回数值列的某范围的值的总和。

```SQL
SELECT SUM(UnitPrice)
FROM Sales.SalesOrderDetail 
WHERE SalesOrderID = 50189
```

```SQL
SELECT SUM(UnitPrice * OrderQty) AS OrderTotalPrice
FROM Sales.SalesOrderDetail
WHERE SalesOrderID = 50189
```

###AVG()函数
用于返回一个范围内的数值的平均值，在函数内部，查询处理器首先计算所有值的总和，然后除于该范围内的行数。

```SQL
SELECT   MIN(UnitPrice)
FROM     Sales.SalesOrderHeader 
         INNER JOIN Sales.SalesOrderDetail ON 
         SalesOrderHeader.SalesOrderID = SalesOrderDetail.SalesOrderID
         INNER JOIN Production.Product 
          ON SalesOrderDetail.ProductID = Product.ProductID
WHERE    Product.ProductSubCategoryID = 2
         AND SalesOrderHeader.OrderDate BETWEEN '1-1-03' And '12-31-03'
```

从一辆自行车的最低付款价格开始。MIN(),MAX(),AVG()函数只返回一个值：

###STDEV()函数

标准偏差是根据一组数值的方差来计算，实际上，它是方差的平方根。

```SQL
SELECT   STDEV(UnitPrice) 
FROM     Sales.SalesOrderHeader 
         INNER JOIN Sales.SalesOrderDetail ON 
         Sales.SalesOrderHeader.SalesOrderID = Sales.SalesOrderDetail.SalesOrderID 
         INNER JOIN Production.Product ON Sales.SalesOrderDetail.ProductID = Product.ProductID
WHERE    Production.Product.ProductSubCategoryID = 2 
         AND Sales.SalesOrderHeader.OrderDate BETWEEN '1-1-03' And '12-31-03'
```

###STDEVP()函数

这个函数根据总体样本方差来计算标准偏差。

###VAR()函数

这个函数返回一组数值的方差。

###VARP()函数

样本总体方差仅是上述方差的另一个表示方式。

###数据分组

* **GROUP BY子句**
我们常常需要把表中的所有行或多表连接的结果放在带有总计或分组小计的汇总行中。

```SQL
SELECT ProductID
     , SpecialOfferID
FROM Sales.SalesOrderDetail
GROUP BY ProductID, SpecialOfferID
ORDER BY ProductID, SpecialOfferID
```

* **HAVING子句**
如果需要根据聚合值的结果来过滤分组查询的结果，必须首先执行聚合操作。这里不能使用WHERE子句，因为它会在分组和聚合之前对结果进行处理。因此就需要HAVING。这个子句非常类似于WHERE,但HAVING子句只能用于已在SELECT语句中定义的列和聚合表达式。

```SQL
SELECT Color, AVG(ListPrice)
FROM Production.Product 
WHERE ProductSubcategoryID = 2
GROUP BY Color
HAVING AVG(ListPrice) > 1200
```
 
* **总计与小计**
GROUP BY子句和聚合函数被用在许多常见的查询语句中，它们提供了一种方便的方法，在查询持续返回特定的列和行集的过程中，减少查询返回的函数。这样应用程序和报表工具就很容易使用这些数据。

```SQL
CREATE TABLE #tem(ID1 VARCHAR(2),ID2 VARCHAR(2),VALUE INT)
GO
INSERT INTO #tem VALUES('A','X',2)
INSERT INTO #tem VALUES('A','X',1)
INSERT INTO #tem VALUES('A','Y',2)
INSERT INTO #tem VALUES('A','Y',1)
INSERT INTO #tem VALUES('B','X',3)
INSERT INTO #tem VALUES('B','Y',2)
INSERT INTO #tem VALUES('B','Y',2)
```

对ID1,ID2包含在GROUP BY子句中，使用SUM()函数对第3个列的值进行总计：

```SQL
SELECT ID1,ID2,SUM(VALUE)
FROM #tem
GROUP BY ID1,ID2
```

得出最后的结果：

|ID1|ID2|VALUE|
|---|---|-----|
|A|X|3|
|A|Y|3|
|B|X|3|
|B|Y|4|

如果只对ID1进行汇总：

```SQL
SELECT ID1,SUM(VALUE)
FROM #tem
GROUP BY ID1
```

得出结果：

|ID1|VALUE|
|---|-----|
|A|6|
|B|7|

* **ROLLUP子句**
这是对GROUP BY列列表的第一列进行小计和总计计算的最简单方法。在上面的表中，出来计算每个唯一的总数外，再加一个A行B行的总和。

ROLLUP的两种使用方法：

```SQL
SELECT ID1,SUM(VALUE)
FROM #tem
GROUP BY ID1
WITH ROLLUP
```

```SQL
SELECT ID1,ID2,SUM(VALUE)
FROM #tem
GROUP BY ROLLUP(ID1,ID2)
```

得出结果：

|ID1|ID2|VALUE|
|---|---|-----|
|A|X|3|
|A|Y|3|
|A|Null|6|
|B|X|3|
|B|Y|4|
|B|Null|7|
|Null|Null|13|

* **CUBE子句**
CUBE子句是对ROLLUP子句拓展。CUBE也一样有两种写入方法。以下就不再加WITH了。

```SQL
SELECT ID1,ID2,SUM(VALUE)
FROM #tem
GROUP BY CUBE(ID1,ID2)
```
得出结果：

|ID1|ID2|VALUE|
|---|---|-----|
|A|X|3|
|B|X|3|
|Null|X|6|
|A|Y|3|
|B|Y|4|
|Null|Y|7|
|Null|Null|13|
|A|Null|6|
|B|Null|7|

* **GROUPING()函数**
GROUPING()函数可以返回一个位值，1或者0来表明某行一个累积值，如果该行为Null，那么值为1。我们看下面的例子。

```SQL

SELECT ID1,ID2,SUM(VALUE) AS SUM_VALUE,GROUPING(ID1) AS IsROllu
FROM #tem
GROUP BY CUBE(ID1,ID2)
```

|ID1|ID2|VALUE|IsROllu|
|---|---|-----|-------|
|A|X|3|0|
|B|X|3|0|
|Null|X|6|1|
|A|Y|3|0|
|B|Y|4|0|
|Null|Y|7|1|
|Null|Null|13|1|
|A|Null|6|0|
|B|Null|7|0|

综合一下，想想下面的程序有什么用~~
```SQL
SELECT ID1,ID2,SUM(VALUE) AS SUM_VALUE,GROUPING(ID1) AS AVG_VALUE
FROM #tem
GROUP BY CUBE(ID1,ID2)
HAVING GROUPING(ID1)=0
```

* **COMPUTE与COMPUTE BY 子句**
快速输出小计、总计的结果。

```SQL
SELECT ID1,ID2,VALUE 
FROM #tem
COMPUTE AVG(VALUE)
```

以下是以某项排序并进行小计的例子：
```SQL
SELECT ID1,ID2,VALUE 
FROM #tem
ORDER BY ID1
COMPUTE SUM(VALUE) BY ID1
```
得出结果:

|ID1|ID2|VALUE|
|---|---|-----|
|A|X|2|
|A|X|1|
|A|Y|2|
|A|Y|1|

|SUM|
|---|
|6|

|ID1|ID2|VALUE|
|---|---|-----|
|B|X|3|
|B|Y|2|
|B|Y|2|

|SUM|
|---|
|7|

<h2 id="dbcx">多表查询</h2>
本章主要内容

* 应用子查询和连接来合并多个表中的列
* 在FROM和WHERE子句中使用旧连续语法和ANSI标准连接语法
* 内连接、外连接、全连接与交叉连接自己的区别
* 应用UNION运算符合并多个结果集

###理解子查询与连接

* **在WHERE子句中连接表**
在下例中，Customer和SalesOrderHeader表都在FROM子句中被应用，而连接操作则在WHERE子句中进行。

```SQL
SELECT Customer.AccountNumber, SalesOrderHeader.OrderDate
FROM Sales.Customer, Sales.SalesOrderHeader
WHERE Customer.CustomerID = SalesOrderHeader.CustomerID
```

* **在FROM子子句中连接表**
同样的运算也可以通过FROM子句，加JOIN语句引用两个表，后面ON关键字。

```SQL
SELECT Customer.AccountNumber, SalesOrderHeader.OrderDate
FROM Sales.Customer INNER JOIN Sales.SalesOrderHeader
ON Customer.CustomerID = SalesOrderHeader.CustomerID
```

虽然是获得一样的结果，虽然性能消耗相差不大，但后面的例子符号ANSI标准。

* **连接的类型**
连接的类型主要有以下两种类型。

|连接类型|说明|
|--------|----|
|内连接|它只返回两个表中的相关记录。|
|外连接|它先返回一个表中的所有行，然后再返回第二个表的相关行|

* **内连接**
这是最常见的连接运算类型。内连接的目的是将一个表中的记录和另一个表中对应记录进行匹配，前提是两个表的相关列包含相同的值。如果其中一个表的列值不同，或者根本就没有值，查询就不会返回这些行。

举例：对于所有有订单的顾客以及所有有对应顾客的订单，需要查看每个顾客的账户号和每个订单的订单日期。

```SQL
SELECT Customer.AccountNumber, SalesOrderHeader.OrderDate
FROM Sales.Customer INNER JOIN Sales.SalesOrderHeader
ON Customer.CustomerID = SalesOrderHeader.CustomerID
```

主要INNER JOIN可以简写为JOIN

```SQL
SELECT Customer.AccountNumber, SalesOrderHeader.OrderDate
FROM Sales.Customer JOIN Sales.SalesOrderHeader
ON Customer.CustomerID = SalesOrderHeader.CustomerID 
```

* **外连接**
外连接用于从一个表中返回所有的行，然后匹配相关表中具有相同连接列值的行。外连接与内连接的区别是，在外连接中，查询将返回第一个表中不匹配的行。

举例：除了需要查看所有的顾客之外，对于有订单的顾客还要查看相关的订单信息，并且显示每个顾客的账户号。如果该顾客有订单，还要显示每个顾客与订单的组合行。

```SQL
SELECT Customer.AccountNumber, SalesOrderHeader.OrderDate
FROM Sales.Customer LEFT OUTER JOIN Sales.SalesOrderHeader
ON Customer.CustomerID = SalesOrderHeader.CustomerID
```

外连接总是偏向一个表，即读者请求返回所有行的那个表。可以用*LEFT JOIN*来代替*LEFT OUTER JOIN*，同理，*RIGHT JOIN*也可以代替*RIGHT OUTER JOIN*。

* **多列连接**
一些数据库设计了多列键，需要在查询中定义多列连接。连接中使用的列数没有明确的限制，通常只需使用多个连接列来支持特定的业务规则（因为连接通常在主键与外键列上进行。）

下面用这项技术来查询一经销价销售的产品销售记录，这需要在两个表中匹配ProductID,Product表中的ListPrice和SalesOrderDetail表中的UnitPrice：

```SQL
SELECT     Product.ProductID
         , Product.Name
         , Product.StandardCost
         , Product.ListPrice
         , SalesOrderDetail.UnitPrice
FROM       Production.Product 
           INNER JOIN Sales.SalesOrderDetail 
           ON  Product.ProductID = SalesOrderDetail.ProductID 
           AND Product.ListPrice = SalesOrderDetail.UnitPrice

```

* **非等同连接**
前面都是等同运算，也可以用不等于、大于、小于、大于等于、小于等于进行查询。如下：

```SQL
SELECT     Product.ProductID
         , Product.Name
         , Product.StandardCost
         , Product.ListPrice
         , SalesOrderDetail.UnitPrice
FROM       Production.Product 
           INNER JOIN Sales.SalesOrderDetail 
           ON  Product.ProductID = SalesOrderDetail.ProductID 
           AND Product.ListPrice > SalesOrderDetail.UnitPrice
```

* **特殊目的的连接运算**
下面介绍两种不常见的连接方式：

* 1.全连接
全连接或全外连接是一种不偏向两个表中的任何一个外连接，用于返回连接两端不匹配的值。

```SQL
SELECT Customer.CustomerID
     , EmployeeID
FROM Sales.Customer LEFT OUTER JOIN Sales.Store 
       ON Customer.CustomerID = Store.CustomerID
       FULL OUTER JOIN 
       HumanResources.Employee ON Store.SalesPersonID = Employee.EmployeeID
ORDER BY CustomerID, EmployeeID
```

* 2.交叉连接
在使用交叉连接时，不需要进行值匹配的连接列，查询只是返回两个表中所有可能的行组合。使用交叉连接会产生大量的行，这是测试数据填写测试数据库表的高效方法。

* 3.使用谓词在连接子句中过滤记录
在传统的多表查询时，首先是处理连接，然后再进行过滤。SQL Server也可以在需要时先进行过滤，保证不匹配过滤规则的记录不参加连接。

```SQL
SELECT Customer.CustomerID
     , LoginID
FROM   Sales.Customer INNER JOIN Sales.Store
        ON Customer.StoreID = Store.BusinessEntityID
       INNER JOIN HumanResources.Employee
        ON Store.SalesPersonID = Employee.BusinessEntityID
       AND Customer.CustomerID < 10
ORDER BY CustomerID, LoginID
```

* 4.在表达式上连接
以一个SELECT语句上，AS成一个别名再进行连接查询。

例如，市场主管想减少销售便宜货物的开销，想了解一下前10种最便宜的货物的累计销售情况，这就需要两个SELECT表达式来完成。第一个返回10种最便宜的产品行，第二个则在此结果的基础上进行过滤，返回累计销售额。

```SQL
SELECT 
       SalesOrderDetail.ProductID
     , CheapProducts.Name
     , CheapProducts.ListPrice
     , SUM(LineTotal) AS SalesTotal
FROM Sales.SalesOrderDetail
     INNER JOIN
        (SELECT TOP 10
              ProductID
            , Name
            , ListPrice 
         FROM Production.Product
         WHERE ListPrice <> 0
         ORDER BY ListPrice ASC) AS CheapProducts
     ON SalesOrderDetail.ProductID = CheapProducts.ProductID
GROUP BY 
       SalesOrderDetail.ProductID
     , CheapProducts.Name
     , CheapProducts.ListPrice
```

* 5.多表连接
在编写复杂查询时，通常需要使用图形化查询设计器作为设计的开始。没有设计器的帮助，编写复杂的多表连接会特别的棘手。

以下的例子是一张商店的列表，其中包括购买产品的商店/顾客、订单与产品信息的细节。

```SQL
SELECT     Store.Name AS StoreName
         , Customer.AccountNumber
         , SalesOrderHeader.OrderDate
         , ProductCategory.Name AS Category
         , ProductSubCategory.Name AS SubCategory
         , Product.Name AS ProductName
FROM       Sales.Store INNER JOIN Sales.Customer 
               ON Store.BusinessEntityID = Customer.StoreID 
           LEFT OUTER JOIN Sales.SalesOrderDetail 
           INNER JOIN Sales.SalesOrderHeader 
             ON Sales.SalesOrderDetail.SalesOrderID = SalesOrderHeader.SalesOrderID 
           INNER JOIN Production.Product 
               ON SalesOrderDetail.ProductID = Product.ProductID 
           INNER JOIN Production.ProductSubCategory 
               ON Product.ProductSubCategoryID = 
                  ProductSubCategory.ProductSubCategoryID 
           INNER JOIN Production.ProductCategory 
               ON ProductSubCategory.ProductCategoryID = 
                  ProductCategory.ProductCategoryID
               ON Customer.CustomerID = SalesOrderHeader.CustomerID
ORDER BY Store.Name
```

* **合并查询**
连接查询(JOIN)在水平方向上扩展了结果集，也就是说，它将多个表的列加到结果中。而UNION(合并)则是将增加结果集的行数。

合并查询也是经过合并和排序的，重复的行会被删除。

```SQL
SELECT TOP 5 FirstName AS Name, 'Contact' AS Source 
FROM Person.Person 
UNION
SELECT TOP 5 Name, 'Vendor' AS Source 
FROM Purchasing.Vendor
```

* 1.数据分区与联合
SQL Server通过远程服务器连接查询，进行多地区多服务器进行管理。

* 2.性能与规模上的考虑
分区存储解决方案，将数据分割到多个磁盘上，一种多表合并(UNION)查询的技术，可以很高效的完成工作。

<h2 id="gjcx">高级查询与脚本</h2>

###子查询
所谓子查询就是位于SELECT查询中的SELECT查询。子查询有许多种形式，从返回单一值或标量值，到多行结果集的值都有。

* **1.标量表达式**
在SELECT语句列选择列表中，嵌入的SELECT语句可以用于返回一个列的值。标量表达式最常见的例子就是使用聚合函数来返回一个多行范围内的值。

以下的例子是，我们要比较所支付产品的价格和所有产品平均销售价格。在查询的结果集中产生一个别名为AvgPrice(平均价格)的列：

```SQL
SELECT   ProductID
       , UnitPrice
       , (SELECT AVG(UnitPrice) FROM Sales.SalesOrderDetail)
    AS AvgPrice
FROM Sales.SalesOrderDetail
```

这时候所生成的AvgPrice(平均价格)是个常量，在每一行的值都是一样的，我们可以设计得更有分析效果一点：

```SQL
SELECT   ProductID
       , UnitPrice
       , UnitPrice - (SELECT Avg(UnitPrice) FROM Sales.SalesOrderDetail)
    AS AvgPriceDifference
FROM Sales.SalesOrderDetail
```

这是将当前产品的销售价格，与所有销售产品的平均销售价格之间的差。

不仅可以通过对一个表的聚合来取得标量值，也可以取外部的表。

```SQL
SELECT   ProductID
       , UnitPrice AS SalePrice
       , UnitPrice - (SELECT ListPrice FROM Production.Product 
          WHERE Product.ProductID = SalesOrderDetail.ProductID)
            AS PriceDifference
FROM Sales.SalesOrderDetail
```

* **2.交换连接运算**

* 1.内连接子查询
内连接子查询是个简单的表达式。这个连接是在WHERE子句中执行的，而不是在FROM。

```SQL
SELECT SOH.OrderDate
     , SOD.ProductID
     , SOD.UnitPrice
FROM Sales.SalesOrderHeader AS SOH, Sales.SalesOrderDetail AS SOD
WHERE SOH.SalesOrderID = SOD.SalesOrderID
ORDER BY SOH.OrderDate
```

