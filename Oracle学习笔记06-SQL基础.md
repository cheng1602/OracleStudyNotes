# SQL（结构化查询语言）基础

*Structured Query Language*

## 视图

*view：从表中抽取出的逻辑上相关的数据集合*

- 视图

  - 视图是一种虚表

  - 视图建立在已有表的基础上，视图赖以建立的这些表称为基表

  - 向视图提供数据内容的语句为SELECT语句，可以将视图理解为存储起来的SELECT语句

  - 视图向用户提供了基表数据的另一种表现形式

  - 使用视图可以控制数据访问、简化查询、避免重复访问相同的数据

    ```sql
    --CREATE OR REPLACE修改视图
    CREATE OR REPLACE VIEW CX_STAFF_VIEW
    AS
    --视图中同样可以命名别名
    SELECT ID,NAME,SALARY SAL,MANAGER
    --简化查询：基于多个表创建视图
    FROM CX_STAFF,CX_DEPARTMENT
    WHERE DEPARTMENT_ID = 01
    --只读
    WITH READ ONLY
    ```

  - 可与表一样使用`SELECT * FROM VIEW`查看



- 简单视图与复杂视图 

  | 特性     | 简单视图 | 复杂视图   |
  | -------- | -------- | ---------- |
  | 表的数量 | 一个     | 一个或多个 |
  | 函数     | 没有     | 有         |
  | 分组     | 没有     | 有         |
  | DML操作  | 可以     | 有时可以   |

  ```sql
  CREATE OR REPLACE VIEW CX_STAFF_VIEW
  AS
  --AVG(...)必须加别名，因为原本不存在这一列
  SELECT MANAGER,AVG(SALARY) AVG
  FROM CX_STAFF,CX_DEPARTMENT
  WHERE CX_STAFF.DEPARTMENE_ID = CX_DEPARTMENT.DEPARTMENE_ID
  GROUP BY MANAGER
  ```

  

- 视图中使用DML的规定

  - 可在简单视图中执行DML操作
  - 当视图定义中包含以下元素之一时不能使用delete：
    - 组函数
    - GROUP BY 子句
    - DISTINCT 关键字
    - ROWNUM 伪列 

  

- 删除视图

  ```sql
  DROP VIEW CX_STAFF_VIEW
  ```

  

- Top-N 分析

  - Top-N分析查询一个列中最大或最小的n个值
  
    ```sql
    SELECT ROWNUM,ID,NAME,SALARY
    FROM (SELECT ID,NAME,SALARY
         FROM CX_STAFF
         ORDER BY SALARY DESC)
    --SALARY最高的前2位
    WHERE ROWNUM <= 2 
    --对ROWNUM只能使用 < 或 <=，而用 =, >, >=都不能返回任何数据
    ```
  
    ```sql
    --可以把以上表看作一个新的表，伪列看做一个新的列
    SELECT RN,ID,NAME,SALARY
    FROM(
    --通过别名区分
         SELECT ROWNUM RN,ID,NAME,SALARY
         FROM (SELECT ID,NAME,SALARY
               FROM CX_STAFF
               ORDER BY SALARY DESC)
         )
    --SALARY从高到低的第2到第3位
    WHERE RN >= 2 AND RN <= 3
    ```
  
    

## 其他常见数据库对象

- 序列

  - 可供多个用户用来产生唯一数值的数据库对象

    - 自动提供唯一的数值

    - 共享对象

    - 主要用于提供主键值

    - 将序列装入内存可以提高访问效率

  - CREATE SEQUNCE 语句

    ```sql
    --创建序列
    CREATE SEQUENCE CX_SEQ
    INCREMENT BY 10 --每次增长10
    START WITH 10 --从10开始增长
    MAXVALUE 100 --提供的最大值
    CYCLE --需要循环/NOCYCLE不需要循环
    NOCACHE --不需要缓存登录/CHACHE n需要缓存登录
    ```

    ```sql
    --查看序列的下一个值
    SELECT CX_SEQ.NEXTVAL FROM DUAL;
    --查看序列的当前值
    SELECT CX_SEQ.CURRVAL FROM DUAL;
    ```

  - ALTER SEQUNENCE 语句

    ```sql
    --修改序列可以修改增量、最大值、最小值、循环选项和是否装入内存
    ALTER SEQUENCE CX_SEQ
    INCREMENT BY 1
    NOCYCLE
    --初始值只有删除序列后重新创建序列来更改
    DROP SEQUENCE CX_SEQ
    ```

  - 裂缝

    回滚、系统异常或多个表同时使用同一序列会出现裂缝

    

- 索引

  - 作用

    - 加速Oracle服务器的查询速度
    - 索引被删除或破坏不会对表产生影响，指挥影响查询的速度

  - 创建索引

    - 自动创建

      在定义PRIMARY KER或UNIQUE约束后系统自动的在相应的列上创建唯一性索引

    - 手动创建

      用户可在其他列上创建非唯一的索引，以加速查询

      ```sql
      --在一个或多个列上创建索引
      CREATE INDEX CX_INDEX
      ON CX_STAFF(NAME)
      --删除
      DROP INDEX CX_INDEX
      ```

    - 什么时候创建索引

      1. 列中数据值分布范围很广
      2. 列经常在WHERE子句或连接条件中出现
      3. 表经常访问且数据量很大，访问的数据大概占数据总量的2%到4%

    - 什么时候不要创建索引

      1. 表很小
      2. 列不经常作为连接条件或出现在WHERE中
      3. 查询的数据大于2%到4%
      4. 表经常更新



- 同义词

  *SYNONYM*

  - 作用

    - 使用同义词访问相同的对象
    - 方便访问其他用户的对象且缩短对象的名字长度

    ```sql
    --创建
    CREATE SYNONYM C_S FOR CX_STAFF
    --使用
    SELECT * FROM C_S
    --删除
    DEOP SYNONYM C_S
    ```

    



