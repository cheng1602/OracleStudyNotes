# SQL（结构化查询语言）基础

*Structured Query Language*

## 多表查询

* 笛卡尔集

  ```sql
  --在表中有相同列时，在列名之前加上表名前缀
  SELECT EMPLOYEES.EMPLOYEE_NAME,DEPARTMENTS.DEPARTMENT_NAME 
  --笛卡尔集错误：所有行相互连接
  FROM EMPLOYEES,DEPARTMENTS
  --可在WHERE加入有效的连接条件避免笛卡尔集
  WHERE EMPLOYEES.DEPARTMENT_ID = DEPARTMENTS.DEPARTMENT_ID
  ```



* 等值连接

  *主键不能为空，不可重复，主键外键体现实体与实体之间的关系。*

  ```sql
  --不相同的列可省略前缀
  SELECT EMPLOYEE_ID,EMPLOYEE_NAME,D.DEPARTMENT_ID,D.LOCATION_ID,CITY
  --可以通过别名简化前缀
  FROM EMPLOYEES E,DEPARTMENTS D,LOCATION L
  WHERE E.DEPARTMENT_ID = D.DEPARTMENT_ID AND D.LOCATION_ID = L.LOCATION_ID
  ```

  ```sql
  --非等值连接
  WHERE EMPLOYEES.SALARY BETWEEN JOB_GRADES.LOWEST_SAL AND JOB_GRADES.HIGHEST_SAL
  ```

  

* 内连接与外连接  

  - 内连接

    合并具有同一列的两个以上的表的行，结果集中不包含一个表与另一个表不匹配的行。

    *等值连接，非等值连接*

  - 外连接

    - 两个表在连接过程中除了返回满足条件的行以外还返回左（右）表中不满足条件的行，称为左（右）外连接。没有匹配的行时，结果表中相应的列为空。

    - 外连接的WHERE子句条件类似于内连接，但连接条件中没有匹配行的表的列后面要加上外连接运算符`(+)`。

    ```sql
    --左外连接
    WHERE E.DEPARTMENT_ID = D.DEPARTMENT_ID(+)
    ```

    

- SQL:1999语法连接

  - 叉集（了解）

    使用CROSS JOIN子句使连接的表产生叉集（与笛卡尔集相同）。

  - 自然连接

    - NATURAL JOIN子句以两个表中具有相同名字的列为条件创建等值连接。

    - 如果只是列名相同而数据类型不同，则会产生错误。

    ```sql
    SELECT DEPARTMENT_ID,DEPARTMENT_NAME,
    LOCATION_ID,CITI
    FROM DEPARTMENT
    NATURAL JOIN LOCATIONS
    --将以最小的相同列做等值连接
    ```

  - JOIN...USING连接

    ```sql
    FROM EMPLOYEES JOIN DEPARTMENTS USING(DEPARTMENT_ID) 
    --指定列名做等值连接
    ```

    

  - JOIN...ON连接

    ```sql
    FROM EMPLOYEES JOIN DEPARTMENTS
    ON EMPLOYEES.DEPARTMENT_ID = DEPARTMENTS.DEPARTMENT_ID
    ```

    ```sql
    --左外连接
    FROM EMPLOYEES LEFT OUTER JOIN DEPARTMENTS
    ON EMPLOYEES.DEPARTMENT_ID = DEPARTMENTS.DEPARTMENT_ID
    ```

    ```sql
    --满外连接
    FROM EMPLOYEES FULL OUTER JOIN DEPARTMENTS
    ON EMPLOYEES.DEPARTMENT_ID = DEPARTMENTS.DEPARTMENT_ID
    ```

  - 自连接

    ```sql
    --表与自己连接
    SELECT EMP.LAST_NAME,MANAGER.LASTNAME,MANAGER.SLARY,MANAGER.EMAIL
    FROM EMPLOYEES EMP,EMPLOYEES MANAGER
    WHERE EMP.MANAGER_ID = MANAGER.EMPLOYEE_ID AND UPPER(EMP.LAST_NAME) = 'CHENG'
    ```



## 分组函数

分组函数作用于一组数据，并对一组数据返回一个值。

* 组函数类型

  - `AVG(EXPR)`

    返回EXPR的样本平均值 `AVG = SUM/COUNT`

  - `COUNT(EXPR)`

    返回EXPR的样本中不为空的记录总数

  - `MAX(EXPR)`

    返回EXPR的样本的最大值

  - `MIN(EXPR)`

    返回EXPR的样本的最小值

  - `STDDEV(EXPR)`

    返回EXPR的样本标准偏差

  - `SUM(EXPR)`

    返回EXPR的样本的和

   *AVG,SUM只能针对NUMBER类型的数据，MAX,MIN,COUNT可以对任意数据类型使用*



* 组函数与空值

  - 组函数忽略空值

  - 在组函数中使用NVL函数使分组函数无法忽略空值

    ```sql
    SELECT AVG(NVL(SALARY,0)) FROM EMPLOYEES
    ```

  - DISTINCT关键字

    `COUNT(DISTINCT EXPER)`返回EXPR非空且不重复的记录总数。



- 分组数据

  - GROUP BY子句

    ```sql
    --分别列出40，60，80三个部门的平均工资
    SELECT DEPARTMENT_ID,AVG(SALARY)
    FROM EMPLOYEES
    --WHERE子句过滤条件必须紧跟FROM子句
    WHERE DEPARTMENT_ID IN (40,60,80)
    GROUP BY DEPARTMENT_ID
    ```

  - HAVING子句

    ```sql
    --分别列出平均工资大于6000的部门的平均工资
    SELECT DEPARTMENT_ID,AVG(SALARY)
    FROM EMPLOYEES
    --不可这么写：WHERE AVG(SALARY) > 6000
    --组函数不能在WHERE子句中使用，可在HAVING子句中使用
    HAVING AVG(SALARY) > 6000
    GROUP BY DEPARTMENT_ID
    ORDER BY DEPARTMENT_ID
    ```

  - 嵌套组函数

    ```sql
    SELECT MAX(AVG(SALARY))
    FROM EMPLOYEES
    GROUP BY DEPARTMENT_ID
    ```

    

    

  

  