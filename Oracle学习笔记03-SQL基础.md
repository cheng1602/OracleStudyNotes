# SQL（结构化查询语言）基础

*Structured Query Language*

## 子查询

- 子查询语法

  ```sql
  --查询比Abel工资高的人
  SELECT LAST_NAME,SALARY
  FROM EMPLOYEES
  WHERE SALARY > (SELECT SALARY
                  FROM EMPLOYEES
                  WHERE LAST_NAME = 'Abel')
  ```

  - 子查询（内查询）在主查询之前执行完成。
  - 子查询的结果被主查询（外查询）使用。
  - 子查询要包含在括号内，将子查询放在比较条件的右侧。

- 单行子查询

  只返回单行，使用单行比较操作符。

  `=,>,>=,<,<=,<>`

- 多行子查询

  返回多行，使用多行比较操作符。

  | 多行比较操作符 | 作用                       |
  | -------------- | -------------------------- |
  | IN             | 等于列表中任意一个         |
  | ANY            | 和子查询返回的某一个值比较 |
  | ALL            | 和子查询返回的所有值比较   |

  - ANY操作符

    ```sql
    --返回其他部门在比'IT_01'部门的任一工资低的员工的工号、姓名、部门及工资
    SELECT EMPLOYEE_ID,LAST_NAME,JOB_ID,SALARY
    FROM EMPLOYEES
    WHERE JOB_ID <> 'IT_01' AND SALARY < ANY (
                                              SELECT SALARY
                                              FROM EMPLOYEES
                                              WHERE JOB_ID = 'IT_01'
                                              )
    ```

  - ALL操作符

    ```sql
    --返回其他部门在比'IT_01'部门的所有工资都低的员工的工号、姓名、部门及工资
    SELECT EMPLOYEE_ID,LAST_NAME,JOB_ID,SALARY
    FROM EMPLOYEES
    WHERE JOB_ID <> 'IT_01' AND SALARY < ALL (
                                              SELECT SALARY
                                              FROM EMPLOYEES
                                              WHERE JOB_ID = 'IT_01'
                                              )
    ```

    

## 创建和管理表

- 常见的数据库对象

  | 数据库对象 | 描述                             |
  | ---------- | -------------------------------- |
  | 表         | 基本的数据存储集合，由行和列组成 |
  | 视图       | 从表中抽出的逻辑上相关的数据集合 |
  | 序列       | 提供有规律的数值                 |
  | 索引       | 提高查询的效率                   |
  | 同义词     | 给对象起别名                     |

  

- Oracle数据库中的表

  - 用户定义的表

    用户自己创建并维护的一组表，可使用以下代码查询：

     `SELECT * FROM USER_TABLES;`

    

- 表名和列名的命名规则

  1. 必须以字母开头
  2. 必须在 1-30 个字符之间
  3. 只能包含 A-Z，a-z，0-9，_，$，#
  4. 不能和用户定义的其他对象重名
  5. 不能是Oracle的保留字

- CREATE TABLE语句

  - 要求
    - 具备CREATE TABLE权限及存储空间
    - 指定表名、列名、尺寸

  ```sql
  --方法1(白手起家)
  CREATE TABLE TEST_TABLE(
  ID NUMBER(4),
  NAME VARCHAR2(20),
  SALARY NUMBER(10,2),
  HIRE_DATE DATE
  )
  ```

  ```sql
  --方法2：使用子查询创建表，创建表和插入数据相结合
  CREATE TABLE TEST_TABLE2
  AS
  SELECT ID,NAME,SALARY,HIRE_DATE
  FROM TEST_TABLE
  --可通过WHERE子句限制数据的继承，1=2显然不满足，则继承来的将是一个空表
  WHERE 1 = 2
  ```

  

- 数据类型

  | 数据类型       | 描述                                   |
  | -------------- | -------------------------------------- |
  | VARCHAR2(size) | 可变长字符数据                         |
  | CHAR(size)     | 定长字符数据                           |
  | NUMBER(p,s)    | 可变长数值数据                         |
  | DATE           | 日期型数据                             |
  | LONG           | 可变长字符数据，最大可达到2G           |
  | CLOB           | 字符数据，最大可达到4G                 |
  | RAW (LONG RAW) | 原始的二进制数据                       |
  | BLOB           | 而精致数据，最大可到达4G               |
  | BFILE          | 存储外部文件的二进制数据，最大可到达4G |
  | ROWID          | 行地址                                 |

  

- ALTER TABLE语句

  1. 追加新的列

     ```sql
     ALTER TABLE TEST_TABLE
     ADD(EMAIL VARCHAR2(20))
     ```

  2. 为新追加的列定义默认值

     ```sql
     ALTER TABLE TEST_TABLE
     MODIFY(SALARY NUMBER(10,2) DEFAULT 2000)
     ```

  3. 修改现有的列

     ```sql
     --数据类型和容量都可以修改，但若要修改书记类型，所修改的列必须为空
     ALTER TABLE TEST_TABLE
     MODIFY(ID NUMBER(5))
     ```

  4. 删除现有的列

     ```sql
     ALTER TABLE TEST_TABLE
     DROP COLUMN EMAIL
     ```

  5. 重命名表的一个列名

     ```sql
     ALTER TABLE TEST_TABLE
     RENAME COLUMN SALARY TO SAL
     ```

  - 回滚：`ROLLBACK;`

    *DDL操作无法回滚。*

  

- 删除与清空与改变对象的名称

  - 删除表

    ```sql
    --数据和结构都被删除
    --所有相关索引被删除
    DROP TABLE TEST_TABLE2
    ```

  - 清空表

    ```sql
    --删除表中的所有数据
    --释放存储空间
    TRUNCATE TABLE TEST_TABLE
    ```

  - 改变对象的名称

    ```sql
    RENAME TEST_TABLE TO TEST
    ```

    

