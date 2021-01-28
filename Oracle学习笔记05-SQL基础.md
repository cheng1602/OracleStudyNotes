# SQL（结构化查询语言）基础

*Structured Query Language*

## 数据处理

*上节笔记中的创建和管理表属于DDL语句，无法回滚，本节笔记为DML语句，可以进行回滚操作。*

- 插入数据

  - INSERT语句语法

    ```sql
    /*
    Name      Type         Nullable Default Comments 
    --------- ------------ -------- ------- -------- 
    ID        NUMBER(5)    Y                         
    NAME      VARCHAR2(20) Y                         
    SAL       NUMBER(10,2) Y        2000             
    HIRE_DATE DATE         Y                       
    */
    --这种语法一次只可插入一条数据
    INSERT INTO TEST
    VALUES(1,'ZhangSan',3000,sysdate) 
    ```

    ```sql
    --DATE只能输入DATE型数据，字符和日期型数据应包含在单引号中
    INSERT INTO TEST
    VALUES(2,'LiSi',4000,TO_DATE('2008/08/08','YYYY/MM/DD')) 
    ```

    ```sql
    --向表中插入空值
    INSERT INTO TEST
    VALUES(2,'LiSi',NULL,TO_DATE('2013/08/08','YYYY/MM/DD')) 
    ```

    ```sql
    --向表中插入指定的值
    INSERT INTO TEST(ID,NAME)
    VALUES(4,'ZhaoLiu') 
    ```

    

- 更新数据

  - UPDATE语句语法

    ```sql
    UPDATE CX_STAFF SET DEPARTMENT_ID = 02 WHERE ID = 0005
    ```

    修改后还需提交：`commit`

    

- 删除数据

  - DELETE语句语法

    ```sql
    DELETE FROM CX_STAFF WHERE NAME = '吴为'
    ```

    *“ORA-02292已找到子记录,数据完整性错误”*



- 数据库事务

  - 事务

    一组逻辑操作单元使数据从一种状态变换到另一种状态。

  - 数据库事务的组成

    1. 一个或多个DML语句，一个DDL语句，一个DCL语句。
    2. 以第一个DML语句的执行作，以COMMIT语句/ROLLBACK语句/DDL语句（自动提交）/用户会话正常结束/系统异常终止，作为结束。

  - 控制事务

    可以添加保存点并选择性的回滚：

    ​    `SAVEPOING A;`

    ​    `ROLLBACK TO SAVEPOINT A`  

  - 提交或回滚前的数据状态

    - 改变前的数据是可以恢复的
    - 执行DML操作的用户可以通过SELECT语句查询之前的修正
    - 其他用户不能看到当前用户所做的改变，知道结束事务
    - DML语句所涉及到的行被锁定，其他用户不能操作



## 约束

*约束是表级的强制规定。列约束必须跟在列的定义后面，表约束不与列一起，而是单独定义。*

- NOT NULL

  *只能定义在列上*

  ```sql
  CREATE TABLE CX_TEST(
      --定义约束名跟在数据类型后、约束前,尽量见名知意
      --NOT NULL:不能为空
      ID NUMBER(2)CONSTRAINT CX_TEST_ID_NN NOT NULL，
      NAME VARCHAR2(20)NOT NULL,
      SALARY NUMBER(10,2)
      ）
  ```

- UNIQUE

  ```sql
  CREATE TABLE CX_TEST(
      --UNIQUE:不能重复
      --列级约束
      ID NUMBER(2)CONSTRAINT CX_TEST_ID_UK UNIQUE,
      --ORA-00001:违反唯一约束条件(...CX_TEST_ID_UK)
      --定义约束名有助于快速排查
      NAME VARCHAR2(20)NOT NULL,
      EMAIL VARCHAR2(20),
      --表级约束
      constraint EMAIL_UK UNIQUE(EMAIL)
      --不能重复但是都能为空
      ）
  ```

  

- PRIMARY KET

  ```sql
  CREATE TABLE CX_TEST(
      --PRIMARY KET:主键不能为空、不能重复
      ID NUMBER(2)CONSTRAINT CX_TEST_ID_PK UPRIMARY KET,
      NAME VARCHAR2(20)NOT NULL,
      EMAIL VARCHAR2(20),
      constraint EMAIL_UK UNIQUE(EMAIL)
      ）
  ```

  

- FOREIGN KEY

  ```sql
  CREATE TABLE CX_TEST(
      ID NUMBER(2)CONSTRAINT CX_TEST_ID_PK UPRIMARY KET,
      NAME VARCHAR2(20)NOT NULL,
      DEPARTMENT_ID NUMBER(2),
      --FOREIGN KEY
      CONSTRAINT CX_TEST_DEPT_ID_FK FOREIGN KEY(DEPARTMNET_ID) REFENCES (CX_DEPARTMENT_ID) ON DELETE CASCADE
      --ON DELETE CASCADE 级联删除
      --ON DELETE SET NULL 级联置空
      ）
  ```

  

- CHECK

  ```sql
  CREATE TABLE CX_TEST(
      ID NUMBER(2)，
      NAME VARCHAR2(20),
      SALARY NUMBER(10,2) CONSTRAINT CX_TEST_SAL_CK CHECK(SALARY > 2000 AND SALARY < 20000)
      ）
  ```



- 添加约束的语法

  使用ALTER TABLE语句：

  - 添加或删除约束，但不能修改

  - 有效化或无效化约束

  - *添加NOT NULL约束要用MODIFY语句*

    ```sql
    --添加NOT NULL
    ALTER TABLE CX_TEST
    MODIFY(NAME VARCHAR2(20) NOT NULL)
    --添加其他
    ALTER TABLE CX_TEST
    ADD CONSTRAINT CX_TEST_ID_UK UNIQUE(CX_TEST_ID)
    ```

    ```sql
    CREATE TABLE CX_TEST(
        ID NUMBER(2)CONSTRAINT CX_TEST_ID_NN NOT NULL，
        NAME VARCHAR2(20),
        SALARY NUMBER(10,2)
        ）
    --删除
    ALTER TABLE CX_TESTR
    DROP CONSTRAINT CX_TEST_ID_NN
    ```

    ```sql
    --无效化
    ALTER TABLE CX_TESTR
    DISABLE CONSTRAINT CX_TEST_ID_NN
    ```

    ```sql
    --有效化
    ALTER TABLE CX_TESTR
    ENABLE CONSTRAINT CX_TEST_ID_NN
    ```

    

