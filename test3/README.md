### 201810112302         软工一班   邓永霜
## 实验3：创建分区表
### 实验目的
    掌握分区表的创建方法，掌握各种分区方式的使用场景。
### 实验内容
    本实验使用3个表空间：USERS,USERS02,USERS03。在表空间中创建两张表：订单表(orders)与订单详表(order_details)。
    使用你自己的账号创建本实验的表，表创建在上述3个分区，自定义分区策略。
    你需要使用system用户给你自己的账号分配上述分区的使用权限。你需要使用system用户给你的用户分配可以查询执行计划的权限。
    表创建成功后，插入数据，数据能并平均分布到各个分区。每个表的数据都应该大于1万行，对表进行联合查询。
    写出插入数据的语句和查询数据的语句，并分析语句的执行计划。
    进行分区与不分区的对比实验。
### 实验步骤
#### 在用户dys中创建表：orders（订单表）
#### 
    CREATE TABLE ORDERS
    (ORDER_ID NUMBER(10, 0) NOT NULL
    , CUSTOMER_NAME VARCHAR2(40 BYTE) NOT NULL
    , CUSTOMER_TEL VARCHAR2(40 BYTE) NOT NULL
    , ORDER_DATE DATE NOT NULL
    , EMPLOYEE_ID NUMBER(6, 0) NOT NULL
    , DISCOUNT NUMBER(8, 2) DEFAULT 0
    , TRADE_RECEIVABLE NUMBER(8, 2) DEFAULT 0
    , CONSTRAINT ORDERS_PK PRIMARY KEY
      (
        ORDER_ID
      )
      USING INDEX
      (
        CREATE UNIQUE INDEX ORDERS_PK ON ORDERS (ORDER_ID ASC)
      LOGGING
      TABLESPACE USERS
      PCTFREE 10
      INITRANS 2
      STORAGE
      (
        BUFFER_POOL DEFAULT
      )
      NOPARALLEL
      )
      ENABLE
    )
    TABLESPACE USERS
    PCTFREE 10
    INITRANS 1
    STORAGE
    (
      BUFFER_POOL DEFAULT
    )
    NOCOMPRESS
    NOPARALLEL
    PARTITION BY RANGE (ORDER_DATE)
    (
      PARTITION PARTITION_2015 VALUES LESS THAN (TO_DATE    ('2016-01-01 00:00:00', 'SYYYY-MM-DD     HH24:MI:SS',     'NLS_CALENDAR=GREGORIAN'))
      NOLOGGING
      TABLESPACE USERS
      PCTFREE 10
      INITRANS 1
      STORAGE
      (
        INITIAL 8388608
        NEXT 1048576
        MINEXTENTS 1
        MAXEXTENTS UNLIMITED
        BUFFER_POOL DEFAULT
      )
      NOCOMPRESS NO INMEMORY
    , PARTITION PARTITION_2016 VALUES LESS THAN (TO_DATE    ('2017-01-01 00:00:00', 'SYYYY-MM-DD     HH24:MI:SS',     'NLS_CALENDAR=GREGORIAN'))
      NOLOGGING
      TABLESPACE USERS
      PCTFREE 10
      INITRANS 1
      STORAGE
      (
        BUFFER_POOL DEFAULT
      )
      NOCOMPRESS NO INMEMORY
    , PARTITION PARTITION_2017 VALUES LESS THAN (TO_DATE    ('2018-01-01 00:00:00', 'SYYYY-MM-DD     HH24:MI:SS',     'NLS_CALENDAR=GREGORIAN'))
      NOLOGGING
      TABLESPACE USERS
      PCTFREE 10
      INITRANS 1
      STORAGE
      (
        BUFFER_POOL DEFAULT
      )
      NOCOMPRESS NO INMEMORY
    , PARTITION PARTITION_2018 VALUES LESS THAN (TO_DATE    ('2019-01-01 00:00:00', 'SYYYY-MM-DD     HH24:MI:SS',     'NLS_CALENDAR=GREGORIAN'))
      NOLOGGING
      TABLESPACE USERS02
      PCTFREE 10
      INITRANS 1
      STORAGE
      (
        BUFFER_POOL DEFAULT
      )
      NOCOMPRESS NO INMEMORY
    , PARTITION PARTITION_2019 VALUES LESS THAN (TO_DATE    ('2020-01-01 00:00:00', 'SYYYY-MM-DD     HH24:MI:SS',     'NLS_CALENDAR=GREGORIAN'))
      NOLOGGING
      TABLESPACE USERS02
      PCTFREE 10
      INITRANS 1
      STORAGE
      (
        BUFFER_POOL DEFAULT
      )
      NOCOMPRESS NO INMEMORY
    , PARTITION PARTITION_2020 VALUES LESS THAN (TO_DATE    ('2021-01-01 00:00:00', 'SYYYY-MM-DD     HH24:MI:SS',     'NLS_CALENDAR=GREGORIAN'))
      NOLOGGING
      TABLESPACE USERS02
      PCTFREE 10
      INITRANS 1
      STORAGE
      (
        BUFFER_POOL DEFAULT
      )
      NOCOMPRESS NO INMEMORY
    , PARTITION PARTITION_2021 VALUES LESS THAN (TO_DATE    ('2022-01-01 00:00:00', 'SYYYY-MM-DD     HH24:MI:SS',     'NLS_CALENDAR=GREGORIAN'))
      NOLOGGING
      TABLESPACE USERS03
      PCTFREE 10
      INITRANS 1
      STORAGE
      (
        BUFFER_POOL DEFAULT
      )
      NOCOMPRESS NO INMEMORY
       );

#### 在用户dys中创建表：order_details（订单详情表）
####
    CREATE TABLE order_details
    (
    id NUMBER(10, 0) NOT NULL
    , order_id NUMBER(10, 0) NOT NULL
    , product_name VARCHAR2(40 BYTE) NOT NULL
    , product_num NUMBER(8, 2) NOT NULL
    , product_price NUMBER(8, 2) NOT NULL
    , CONSTRAINT order_details_fk1 FOREIGN KEY  (order_id)
    REFERENCES orders  (  order_id   )
    ENABLE
    )
    TABLESPACE USERS
    PCTFREE 10 INITRANS 1
    STORAGE (BUFFER_POOL DEFAULT )
    NOCOMPRESS NOPARALLEL
    PARTITION BY REFERENCE (order_details_fk1);

#### 建表完成后
![Image text](3-1.png)
![Image text](3-2.png)

#### 以system用户修改dys用户的权限
![Image text](3-3.png)
![Image text](3-4.png)
![Image text](3-5.png)
![Image text](3-6.png)

#### 对有分区表执行计划分析
##### 以system用户运行：
![Image text](3-7.png)
##### 执行计划分析：
![Image text](3-8.png)
![Image text](3-9.png)
#### 以用户sys创建无分区表
#### 
    CREATE TABLE ORDERS_NOSPACE 
    (
      ORDER_ID NUMBER(10, 0) NOT NULL 
    , CUSTOMER_NAME VARCHAR2(40 BYTE) NOT NULL 
    , CUSTOMER_TEL VARCHAR2(40 BYTE) NOT NULL 
    , ORDER_DATE DATE NOT NULL 
    , EMPLOYEE_ID NUMBER(6, 0) DEFAULT 0 
    , DISCOUNT NUMBER(8, 2) DEFAULT 0 
    , CONSTRAINT ORDERS_ID_ORDERS_DETAILS PRIMARY KEY 
      (
        ORDER_ID 
      )
      USING INDEX 
      (
          CREATE UNIQUE INDEX ORDERS_ID_ORDERS_DETAILS ON     ORDERS_NOSPACE (ORDER_ID ASC) 
          LOGGING 
          TABLESPACE USERS 
          PCTFREE 10 
          INITRANS 2 
          STORAGE 
          ( 
            BUFFER_POOL DEFAULT 
          ) 
          NOPARALLEL 
      )
      ENABLE 
    ) 
    LOGGING 
    TABLESPACE USERS 
    PCTFREE 10 
    INITRANS 1 
    STORAGE 
    ( 
      BUFFER_POOL DEFAULT 
    ) 
    NOCOMPRESS 
    NO INMEMORY 
    NOPARALLEL;
    
    #建立orders_details_nospace表
    CREATE TABLE ORDER_DETAILS_NOSPACE 
    (
      ID NUMBER(10, 0) NOT NULL 
    , ORDER_ID NUMBER(10, 0) NOT NULL 
    , PRODUCT_NAME VARCHAR2(40 BYTE) NOT NULL 
    , PRODUCT_NUM NUMBER(8, 2) NOT NULL 
    , PRODUCT_PRICE NUMBER(8, 2) NOT NULL 
    ) 
    LOGGING 
    TABLESPACE USERS 
    PCTFREE 10 
    INITRANS 1 
    STORAGE 
    ( 
      INITIAL 65536 
      NEXT 1048576 
      MINEXTENTS 1 
      MAXEXTENTS UNLIMITED 
      BUFFER_POOL DEFAULT 
    ) 
    NOCOMPRESS 
    NO INMEMORY 
    NOPARALLEL;
    
    ALTER TABLE ORDER_DETAILS_NOSPACE
    ADD CONSTRAINT ORDERS_FOREIGN_ORDERS_DETAILS FOREIGN KEY
    (
      ORDER_ID 
    )
    REFERENCES ORDERS_NOSPACE
    (
      ORDER_ID 
    )
    ENABLE;
![Image text](3-10.png)
![Image text](3-11.png)
#### 对无分区表执行计划分析
![Image text](3-12.png)
![Image text](3-13.png)
#### 查看数据库的使用情况
    SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='dys';
    SELECT a.tablespace_name "dys",Total/1024/1024 "大小MB",
    free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
    Round(( total - free )/ total,4)* 100 "使用率%"
    from (SELECT tablespace_name,Sum(bytes)free
        FROM  dba_free_space group  BY tablespace_name)a,
        (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
    where  a.tablespace_name = b.tablespace_name;
#### 实验总结
##### 结论：
      用过这次实验，我明白了如果在分区表里查询数据，同一个分区查找明显比不同分区查找快、
      在orders数据量为10000,order_details数据量为30000时，有分区比无分区查找数据优势更大，
      且分区表在数据量大时进行查找的优势比较大，数据小时有无分区的差别不大。