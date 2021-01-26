# SQL（结构化查询语言）基础

*Structured Query Language*

- SQL语句的分类
  1. SELECT查询语句
  
  2. DML（数据库操纵语言）Insert/Update/Delete/Merge
  
  3. DDL（数据定义语言）Create/Alter/Drop/Truncate
  
  4. DCL（数据控制语言）Commit/Rollback/Savepoint
  
     *SQL语句对大小写不敏感，包括登录的用户名、密码在内都不区分大小写。*





## SELECT语句

*SQL\*Plus常用关键字：*`desc(describe),ed(edit)`

- 基础语法

  ```sql
  SELECT * FROM TABLE
  ```

  查询表中的所有数据，也可查询部分字段。

  

- 运算符号

  查询过程中对于数值型的数据可使用+,-,*,/运算符号，对于日期型数据可使用+,-运算符号。

  ```sql
  SELECT 5/2 FROM DUAL
  ```

  空值不同于0，凡是空值参与的运算，结果都为空(null)。

  

- 别名

  别名的三种表示方法：

  ```sql
  SELECT USER_ID AS 'Id',USER_NAME 'Name',USER_SEX Sex FROM USERTABLE
  ```

  分别输出别名：Id，Name， SEX。

  *日期和字符只能在单引号中出现*

  

- 连接符

  ”||“类似java中的”+“，把列与列，列与字符连接在一起。

  ```sql
  SELECT USER_NAME||'‘s job is'||USER_JOB FROM USERTABLE
  ```

  

- 重复行

  默认情况下SELECT语句将输出全部数据，包括重复的部分，可使用DISTINCT关键字去除。

  ```sql
  SELECT DISTINCT USER_JOB FROM USERTABLE
  ```

​       

- 过滤

  使用WHERE子句紧随FROM子句后进行过滤。

  *字符的大小写敏感，日期格式敏感，日期的默认格式为：DD-MON月-RR*

  1. 逻辑运算符

     `AND,OR,NOT`

  2. 比较运算符

     `=,>,>=,<,<=,!=(or <>)`

     `:=(赋值)`

  3. 比较关键字

     | 比较关键字        | 用法                     |
     | ----------------- | ------------------------ |
     | BETWEEN...AND...  | 在两个值之间（包含边界） |
     | IN(SET1,SET2,...) | 等于值列表中的...        |
     | LIKE              | 模糊查询                 |
   | IS NULL           | 空值                     |
  
     ```sql
     WHERE USER_NAME LIKE '__\_%' ESCAPE '\'
   ```
  
   *'_'表示一个字符，'%'表示0到无穷多个字符，'\\'用作转义功能*
  
4. 优先级（仅作了解）
  
   算数运算符>连接符>比较符>IS,LIKE,IN>BETWEEN>NOT>AND>OR。
  
5. 排序
  
   紧跟FROM子句及WHERE子句之后使用ORDER BY 子句进行排序。
  
   DESC(从大到小)，ASC(从小到大)，默认情况下为从小到大排列。
  
   ```sql
     ORDER BY USER_SALARY ASC,USER_JOB ASC
     ```
  
   

## 单行函数

*函数=方法*

只对一行进行变换，每行返回一个结果。

- 字符函数

  - 大小写控制函数

    1. `LOWER('ABCdef')` 输出 `abcdef`

       转换为小写

    2. `UPPER('ABCdef')` 输出 `ABCDEF` 

       转换为大写

    3. `INITCAPE('ABCdef')` 输出 `Abcdef` 

       转换为首字母大写

  - 字符控制函数

    1. `CONCAT('Hello','World')` 输出 `HelloWorld `

       连接

    2. `SUBSTR('HelloWorld',1,5)` 输出 `Hello `

       从第1位起截取5位

    3. `LENGTH('HelloWorld')` 输出 `10` 

       长度

    4. `INSTR('HelloWorld','W')` 输出 `6` 

       第1次出现时的位置

    5. `LPAD(USERSALARY,10,'?')` 输出 `?????15000` 

       输出10位，不足的用？补全在前面

    6. `RPAD(USERSALARY,10,'?')` 输出 `15000?????` 

       输出10位，不足的用？补全在后面

    7. `TRIM('A' FROM 'ABABA')` 输出 `BAB` 

       去除掉首尾处的'A'

    8. `REPLACE('ABABA','A','O')` 输出 `OBOBO` 

       将'A'全部替换为'O'

  

- 数字函数

  - `ROUND(3.14159,3)`   输出 `3.142` 

    保留三位小数四舍五入

  - `TRUNC(3.14159,3)`   输出 `3.141` 

    保留三位小数截断

  - `MOD(1600,300)`   输出 `100` 

    求余

  

- 日期

  Oracle中的日期型数据实际含有两个值：日期和时间。

  - 日期的数学运算

    1. 日期与数字加减后返回的仍是日期。

    2. 日期与日期相减返回相差的天数，日期之间不允许做加法运算。

       ```sql
       SELECT SYSDATE-USER_HIRE_DATE FROM USER
       ```

    3. 可用数字除24来向日期中加上或减去天数。

  - 日期函数

    1.   `MONTHS_BETWEEN(SYSDATE,HIREDATE)`

       两个日期相差的月数

    2. `ADD_MONTHS(SYSDATE,2)`

       向指定日期中加上若干月数

    3. `NEXT_DAY(SYSDATE,'星期日')`

       指定日期的下一星期?对应的日期

    4. `LAST_DAY(SYSDATE)`

       本月最后一天

    5. `ROUND(SYSDATE,'MONTH'),(SYSDATE,'MM')`

       日期四舍五入

    6. `TRUNC(SYSDATE,'HH')`

       日期截断



- 转换函数

  数据类型的转换

  - 隐式数据类型转化

    Oracle自动完成下列转化

    | 源数据类型       | 目标数据类型 |
    | ---------------- | ------------ |
    | VARCHAR2 or CHAR | NUMBER       |
    | VARCHAR2 or CHAR | DATE         |
    | NUMBER           | VARCHAR2     |
    | DATE             | VARCHAR2     |

  - 显式数据类型转化

    | 源数据类型 | 目标数据类型 | 转换函数  |
    | ---------- | ------------ | --------- |
    | DATE       | CHARACTER    | TO_CHAR   |
    | CHARACTER  | DATE         | TO_DATE   |
    | CHARACTER  | NUMBER       | TO_NUMBER |
    | NUMBER     | CHARACTER    | TO_CHAR   |

    ```sql
    WHERE TO_CHAR(USER_HIRE_DATE,'YYYY-MM-DD') = '2000-10-1'
    WHERE TO_DATE('2000-10-1','YYYY-MM-DD') = USER_HIRE_DATE
    --两者效果相同
    ```

    ```sql
    SELECT TO_CHAR(123456.78,'$999,999,999.999') FROM DUAL
    --输出 $123,456.780
    SELECT TO_CHAR(123456.78,'$000,000,000.000') FROM DUAL
    --输出 $000,123,456.780
    ```

    

* 通用函数

  这些函数适用于任何数据类型，同时也适用与空值。

  - NVL函数

    ```sql
    SELECT 1 + NVL(NULL,0) FROM DUAL
    --'NULL'处为可能为空的值
    ```

    `NVL2(EXPR1,EXPR2)`

     将空值转换成一个已知的值。

  - NVL2函数

    `NVL2(EXPR1,EXPR2,EXPR3)`

    EXPR1不为空，返回EXPR2；为空则返回EXPR3。
  
  - NULLIF函数
  
    `NULLIF(EXPR1,EXPR2)`
  
    相等返回NULL，不等返回EXPR1。
  
  - COALESCE函数 
  
    `COALESCE(EXPR1,EXPR2,EXPR3,...)`
  
    如果第一个表达式为空，则返回下一个表达式，对其他参数进行COALESCE。
    
    - 条件表达式

    1. CASE表达式

       ```sql
       CASE EXPR WHEN COMPARISON_EXPR1 THEN RETURN_EXPER1
                 WHEN COMPARISON_EXPR1 THEN RETURN_EXPER1
                 WHEN COMPARISON_EXPR1 THEN RETURN_EXPER1
                 ELSE ELSE_EXPER
       END
       --类似Java中的case语句
       ```

    2. DECODE表达式

       ```sql
       DECODE(EXPR,SEARCH1,RESULT1,
                   SEARCH2,RESULT2,
                   DEFAILT)
       --与CASE表达式结构不同作用相同
       ```  
  
  

