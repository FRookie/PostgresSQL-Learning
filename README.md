# PostgresSQL-Learning
@[toc]
## Postgres概念

> + PostgreSQL是一种关系型数据库管理系统 （RDBMS）。这意味着它是一种用于管理存储在关系中的数据的系统。关系实际上是表的数学术语。 今天，把数据存储在表里的概念已经快成了固有的常识了， 但是还有其它的一些方法用于组织数据库。在类 Unix 操作系统上的文件和目录就形成了一种层次数据库的例子。 更现代的发展是面向对象数据库。

> + 每个表都是一个命名的行集合。一个给定表的每一行由同一组的命名列组成，而且每一列都有一个特定的数据类型。虽然列在每行里的顺序是固定的， 但一定要记住 SQL 并不对行在表中的顺序做任何保证（但你可以为了显示的目的对它们进行显式地排序）。

> + 表被分组成数据库，一个由单个PostgreSQL服务器实例管理的数据库集合组成一个数据库集簇。

## Postgres数据类型 ---> [详细介绍](https://www.runoob.com/postgresql/postgresql-data-type.html)

> + PostgreSQL支持标准的SQL类型int、smallint、real、double precision、char(N)、varchar(N)、date、time、timestamp和interval，还支持其他的通用功能的类型和丰富的几何类型。PostgreSQL中可以定制任意数量的用户定义数据类型。因而类型名并不是语法关键字，除了SQL标准要求支持的特例外.
> + varchar(80)指定了一个可以存储最长 80 个字符的任意字符串的数据类型。int是普通的整数类型。real是一种用于存储单精度浮点数的类型。
> + 类型point就是一种PostgreSQL特有数据类型,它表示一个坐标类型输入，例如：point类型要求一个座标对作为输入  <kbd>'(-194.0, 53.0)'</kbd>.

## Postgres聚集函数

> + 和大多数其它关系数据库产品一样，PostgreSQL支持聚集函数。 一个聚集函数从多个输入行中计算出一个结果。 比如，我们有在一个行集合上计算count（计数）、sum（和）、avg（均值）、max（最大值）和min（最小值）的函数.

> + 聚集函数不能被用于WHERE子句中（存在这个限制是因为WHERE子句决定哪些行可以被聚集计算包括；因此显然它必需在聚集函数之前被计算）。

> + 理解聚集和SQL的WHERE以及HAVING子句之间的关系对我们非常重要。WHERE和HAVING的基本区别如下：WHERE在分组和聚集计算之前选取输入行（因此，它控制哪些行进入聚集计算）， 而HAVING在分组和聚集之后选取分组行。因此，WHERE子句不能包含聚集函数； 因为试图用聚集函数判断哪些行应输入给聚集运算是没有意义的。相反，HAVING子句总是包含聚集函数（严格说来，你可以写不使用聚集的HAVING子句， 但这样做很少有用。同样的条件用在WHERE阶段会更有效）。

## Postgres视图

> + 对视图的使用是成就一个好的SQL数据库设计的关键方面。视图允许用户通过始终如一的接口封装表的结构细节，这样可以避免表结构随着应用的进化而改变。

> + 视图几乎可以用在任何可以使用表的地方。在其他视图基础上创建视图也并不少见,创建语句：

    CREATE VIEW XX_VIEW AS SELECT XXX FROM XXX ...

## Postgres事务

> + PostgreSQL实际上将每一个SQL语句都作为一个事务来执行。如果我们没有发出BEGIN命令，则每个独立的语句都会被加上一个隐式的BEGIN以及（如果成功）COMMIT来包围它。一组被BEGIN和COMMIT包围的语句也被称为一个事务块。

## Postgres窗口函数

> + 一个窗口函数在一系列与当前行有某种关联的表行上执行一种计算。这与一个聚集函数所完成的计算有可比之处。但是窗口函数并不会使多行被聚集成一个单独的输出行，这与通常的非窗口聚集函数不同。取而代之，行保留它们独立的标识。在这些现象背后，窗口函数可以访问的不仅仅是查询结果的当前行。

> + 一个窗口函数调用总是包含一个直接跟在窗口函数名及其参数之后的OVER子句。这使得它从句法上和一个普通函数或非窗口函数区分开来。OVER子句决定究竟查询中的哪些行被分离出来由窗口函数处理。OVER子句中的PARTITION BY子句指定了将具有相同PARTITION BY表达式值的行分到组或者分区。对于每一行，窗口函数都会在当前行同一分区的行上进行计算。我们可以通过OVER上的ORDER BY控制窗口函数处理行的顺序（窗口的ORDER BY并不一定要符合行输出的顺序。）。

> + 一个窗口函数所考虑的行属于那些通过查询的FROM子句产生并通过WHERE、GROUP BY、HAVING过滤的“虚拟表”。例如，一个由于不满足WHERE条件被删除的行是不会被任何窗口函数所见的。在一个查询中可以包含多个窗口函数，每个窗口函数都可以用不同的OVER子句来按不同方式划分数据，但是它们都作用在由虚拟表定义的同一个行集上。

> + 我们已经看到如果行的顺序不重要时ORDER BY可以忽略。PARTITION BY同样也可以被忽略，在这种情况下会产生一个包含所有行的分区。

> + 这里有一个与窗口函数相关的重要概念：对于每一行，在它的分区中的行集被称为它的窗口帧。 一些窗口函数只作用在窗口帧中的行上，而不是整个分区。默认情况下，如果使用ORDER BY，则帧包括从分区开始到当前行的所有行，以及后续任何与当前行在ORDER BY子句上相等的行。如果ORDER BY被忽略，则默认帧包含整个分区中所有的行。 

> + 窗口函数只允许出现在查询的SELECT列表和ORDER BY子句中。它们不允许出现在其他地方，例如GROUP BY、HAVING和WHERE子句中。这是因为窗口函数的执行逻辑是在处理完这些子句之后。另外，窗口函数在非窗口聚集函数之后执行。这意味着可以在窗口函数的参数中包括一个聚集函数，但反过来不行。

> + 当一个查询涉及到多个窗口函数时，可以将每一个分别写在一个独立的OVER子句中。但如果多个函数要求同一个窗口行为时，这种做法是冗余的而且容易出错的。替代方案是，每一个窗口行为可以被放在一个命名的WINDOW子句中，然后在OVER中引用它。

    select X , X() over (partition by X ...) from XX;
    
>> + 窗口函数

    FIRST_VALUE：取分组内排序后，截止到当前行，第一个值 
    LAST_VALUE： 取分组内排序后，截止到当前行，最后一个值 
    LEAD(col,n,DEFAULT) ：用于统计窗口内往下第n行值。第一个参数为列名，第二个参数为往下第n行（可选，默认为1），第三个参数为默认值（当往下第n行为NULL时候，取默认值，如不指定，则为NULL） 
    LAG(col,n,DEFAULT) ：与lead相反，用于统计窗口内往上第n行值。第一个参数为列名，第二个参数为往上第n行（可选，默认为1），第三个参数为默认值（当往上第n行为NULL时候，取默认值，如不指定，则为NULL）

>> + OVER从句

>>> + 1、使用标准的聚合函数COUNT、SUM、MIN、MAX、AVG 
>>> + 2、使用PARTITION BY语句，使用一个或者多个原始数据类型的列 
>>> + 3、使用PARTITION BY与ORDER BY语句，使用一个或者多个数据类型的分区或者排序列
>>> + 4、使用窗口规范，窗口规范支持以下格式：

    (ROWS | RANGE) BETWEEN (UNBOUNDED | [num]) PRECEDING AND ([num] PRECEDING | CURRENT ROW | (UNBOUNDED | [num]) FOLLOWING)
    (ROWS | RANGE) BETWEEN CURRENT ROW AND (CURRENT ROW | (UNBOUNDED | [num]) FOLLOWING)
    (ROWS | RANGE) BETWEEN [num] FOLLOWING AND (UNBOUNDED | [num]) FOLLOWING

## Postgres继承

> 继承是面向对象数据库中的概念。它展示了数据库设计的新的可能性。
> 我们创建两个表：表cities和表capitals。自然地，首都也是城市，所以我们需要有某种方式能够在列举所有城市的时候也隐式地包含首都。

    CREATE TABLE capitals (
      name       text,
      population real,
      altitude   int,    -- (in ft)
      state      char(2)
    );

    CREATE TABLE non_capitals (
      name       text,
      population real,
      altitude   int     -- (in ft)
    );

    CREATE VIEW cities AS
      SELECT name, population, altitude FROM capitals
        UNION
      SELECT name, population, altitude FROM non_capitals;
> 这个模式对于查询而言工作正常，但是当我们需要更新一些行时它就变得不好用了。

    CREATE TABLE cities (
      name       text,
      population real,
      altitude   int     -- (in ft)
    );

    CREATE TABLE capitals (
      state      char(2)
    ) INHERITS (cities);
   
> 在这种情况下，一个capitals的行从它的父亲cities继承了所有列（name、population和altitude）。列name的类型是text，一种用于变长字符串的本地PostgreSQL类型。州首都有一个附加列state用于显示它们的州。在PostgreSQL中，一个表可以从0个或者多个表继承。

### 注意：尽管继承很有用，但是它还未与唯一约束或外键集成，这也限制了它的可用性。


## 个人语句练习

    select * from public."Test";
    alter table public."Test" add column isAdmin boolean default false;
    alter table public."Test" add column birthdaysss timestamp ;
    alter table public."Test" rename column birthdaysss to birthday;
    //修改表名时，如果新表名不加引号，则默认首字母小写，否则按照引号内的定义大小写
    alter table public."test" rename to "Test"; 
    
### 定义枚举类型

    create type week as Enum('Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday');
    alter table public."Test" add column week_day week default 'Monday'; 
    insert into public."Test" values (5,'ss',1,true,current_date,'Friday');
    insert into public."Test" values (6,'ss',1,true,'2019/12/27','Friday');
    
### PostgreSQL 模式（SCHEMA）

    PostgreSQL 模式（SCHEMA）可以看着是一个表的集合。

    一个模式可以包含视图、索引、据类型、函数和操作符等。

    相同的对象名称可以被用于不同的模式中而不会出现冲突，例如 schema1 和 myschema 都可以包含名为 mytable 的表。

    使用模式的优势：

       1. 允许多个用户使用一个数据库并且不会互相干扰。

       2. 将数据库对象组织成逻辑组以便更容易管理。

       3. 第三方应用的对象可以放在独立的模式中，这样它们就不会与其他对象的名称发生冲突。

    模式类似于操作系统层的目录，但是模式不能嵌套。
    
    创建schema：  create schema mySchema;       
    删除一个为空的模式（其中的所有对象已经被删除）：  drop schema myschema;
    删除一个模式以及其中包含的所有对象：  drop schema myschema cascade;

### PostgreSQL WITH 子句

>> 在 PostgreSQL 中，WITH 子句提供了一种编写辅助语句的方法，以便在更大的查询中使用。

>> WITH 子句有助于将复杂的大型查询分解为更简单的表单，便于阅读。这些语句通常称为通用表表达式（Common Table Express， CTE），也可以当做一个为查询而存在的临时表。

>> WITH 子句是在多次执行子查询时特别有用，允许我们在查询中通过它的名称(可能是多次)引用它。

>> WITH 子句在使用前必须先定义

>> WITH 查询的基础语法如下：

    WITH
       name_for_summary_data AS (
          SELECT Statement)
       SELECT columns
       FROM name_for_summary_data
       WHERE conditions <=> (
          SELECT column
          FROM name_for_summary_data)
       [ORDER BY columns]
       
>> name_for_summary_data 是 WITH 子句的名称，name_for_summary_data 可以与现有的表名相同，并且具有优先级。

>> 可以在 WITH 中使用数据 INSERT, UPDATE 或 DELETE 语句，允许您在同一个查询中执行多个不同的操作。

>　WITH 递归

>> 在 WITH 子句中可以使用自身输出的数据。
>> 公用表表达式 (CTE) 具有一个重要的优点，那就是能够引用其自身，从而创建递归 CTE。递归 CTE 是一个重复执行初始 CTE 以返回数据子集直到获取完整结果集的公用表表达式。

### PostgreSQL HAVING 子句

> HAVING 子句可以让我们筛选分组后的各组数据。

> WHERE 子句在所选列上设置条件，而 HAVING 子句则在由 GROUP BY 子句创建的分组上设置条件。

> 语法

>> 下面是 HAVING 子句在 SELECT 查询中的位置：

    SELECT
    FROM
    WHERE
    GROUP BY
    HAVING
    ORDER BY
    
> HAVING 子句必须放置于 GROUP BY 子句后面，ORDER BY 子句前面，下面是 HAVING 子句在 SELECT 语句中基础语法：

    SELECT column1, column2
    FROM table1, table2
    WHERE [ conditions ]
    GROUP BY column1, column2
    HAVING [ conditions ]
    ORDER BY column1, column2
