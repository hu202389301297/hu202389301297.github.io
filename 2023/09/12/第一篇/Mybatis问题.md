#### 1 #{}  和 ${} 优缺点

#{} 会预编译sql 用？ 替代#{}，最后用入参替代？

​	替代时会自动给参数拼接前后引号

 	常见问题： 某些情况不需要加引号的 #{} 不适用

   如privoth函数后面(sqlServer）

)

${} 直接拼接进sql ，不会加引号，会有sql注入问题



#### 2.返回结果是Map时，会过滤掉value值为null的字段

>  说明: 一般对有明确返回结果字段的不建议用Map做返回结果类型，但有些情况结果的返回字段不明确或者说是动态时就会用到Map，
>
>  		如行转列的情况，privot 转换后返回字段根据 IN 后面的字段变化而变化 
>
> ```sql
> SELECT ... 
> FROM ... 
> PIVOT ( 
>     <aggregate function> [AS <alias>] [, <aggregate function> [AS <alias>]] ... 
>     FOR (<column> [, <column>] ...) 
>     IN ( 
>         (<value> [, <value>] ...) AS <new column> 
>         [, (<value> [, <value>] ...) AS <new column>] 
>         ... 
>        ) 
>     ) 
> [...]
> ```

解决：把为NULL的字段转换为 空字符串 就可以全部返回, <font style="color:red"> **注意后面要 取别名（ as fields）不然会报错**</font>

> Mysql中：IFNULL(fields, '') as fields
>
> SqlServer中： ISNULL(fields, '') as fields

#### 3.时间格式化中 MM 和M 区别

 08/07 和8/7 的区别

> yyyy-MM-dd HH:mm:ss  和 yyyy-M-d HH:mm:ss  
>
> 2023-09-09 17:56:08  和  2023-9-9 17:56:08

#### 4.请求接收VO类不能接收请求中大写开头的字段

> 加上 @JsonProperty("Name")
>
> ​     private String Name;

注：一般java请求中都是要求只能是小写开头的字段

#### 5.#{name, jdbcType=VARCHAR} 后面的 jdbcType=VARCHAR必须加吗？

一般 #{}里面要加，不加一般也不会报错

<font style ="color:red">  ${}里面不能加，加了会编译报错 </font>

jdbcType=VARCHAR,这是为了程序的安全性，使一些特殊情况，当传入的参数为name为空时不会使程序出现问题，当name为空时，mybatis不知道具体要转换成什么jdbcType类型，有些特殊情况会报错，Mybatis经常出现的：无效的列类型: 1111 错误，就是因为没有设置JdbcType造成的。

> jdbcType是一个枚举类，有以下类型：
>
> ```java
> public enum JdbcType {
>     ARRAY(2003),
>     BIT(-7),
>     TINYINT(-6),
>     SMALLINT(5),
>     INTEGER(4),
>     BIGINT(-5),
>     FLOAT(6),
>     REAL(7),
>     DOUBLE(8),
>     NUMERIC(2),
>     DECIMAL(3),
>     CHAR(1),
>     VARCHAR(12),
>     LONGVARCHAR(-1),
>     DATE(91),
>     TIME(92),
>     TIMESTAMP(93),
>     BINARY(-2),
>     VARBINARY(-3),
>     LONGVARBINARY(-4),
>     NULL(0),
>     OTHER(1111),
>     BLOB(2004),
>     CLOB(2005),
>     BOOLEAN(16),
>     CURSOR(-10),
>     UNDEFINED(-2147482648),
>     NVARCHAR(-9),
>     NCHAR(-15),
>     NCLOB(2011),
>     STRUCT(2002),
>     JAVA_OBJECT(2000),
>     DISTINCT(2001),
>     REF(2006),
>     DATALINK(70),
>     ROWID(-8),
>     LONGNVARCHAR(-16),
>     SQLXML(2009),
>     DATETIMEOFFSET(-155);
> }
> ```
>
> ```sql
> Exception in thread "main" org.springframework.jdbc.UncategorizedSQLException: Error setting null for parameter #6 with JdbcType OTHER .
>  Try setting a different JdbcType for this parameter or a different jdbcTypeForNull configuration property. Cause: java.sql.SQLException: 无效的列类型: 1111; uncategorized SQLException for SQL []; SQL state [99999]; error code [17004];
> ```



#### 6.标签foreach separator 前后会自带空格

#### 7.List<Map>返回时，结果顺序会和查询结果不一致

用LinkedHashMap 代替Map，就会保证顺序一致



