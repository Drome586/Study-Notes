# **JDBC**

```java
jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&Timezone=Asia/Shanghai
```



##  JDBC连接数据库

```java
/*
方式一：不加载配置文件信息
*/
String url = "jdbc:mysql://localhost:3306/test?";
String user="root";
String password = "123456";

Class.forName("com.mysql.cj.jdbc.Driver");

Connection conn = DriverManager.getConnection(url,user,password);

/*
方式二：使用配置文件
*/

InputStream is = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");

Properties properties = new Properties();
properties.load(is);

String user = properties.getProperty("user");
String password = properties.getProperty("password");
String url = properties.getProperty("url");
String driverClass = properties.getProperty("driverClass");

Class.forName(driverClass);

Connection connection = DriverManager.getConnection(url, user, password);
```

## JDBC进行增删改

1.获取数据库的连接（写成工具类）

2.预编译sql语句，返回prepareStatement的实例

3.填充占位符

4.执行

5.资源的关闭（写成工具类）

## JDBC查询操作

| Java类型           | SQL类型                  |
| ------------------ | ------------------------ |
| boolean            | BIT                      |
| byte               | TINYINT                  |
| short              | SMALLINT                 |
| int                | INTEGER                  |
| long               | BIGINT                   |
| String             | CHAR,VARCHAR,LONGVARCHAR |
| byte   array       | BINARY  ,    VAR BINARY  |
| java.sql.Date      | DATE                     |
| java.sql.Time      | TIME                     |
| java.sql.Timestamp | TIMESTAMP                |

![image-20230114132803536](C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230114132803536.png)

## 实操代码

```java
//获取数据库的连接工具类

public static Connection getConnection() throws Exception {

    InputStream is = ClassLoader.getSystemClassLoader().getResourceAsStream("jdbc.properties");

    Properties properties = new Properties();
    properties.load(is);

    String user = properties.getProperty("user");
    String password = properties.getProperty("password");
    String url = properties.getProperty("url");
    String driverClass = properties.getProperty("driverClass");

    Class.forName(driverClass);

    Connection connection = DriverManager.getConnection(url, user, password);

    return connection;
}

//关闭资源工具类
public static void closeResources(Connection connection, Statement prepare, ResultSet result){
    try {
        if (prepare!= null) {
            prepare.close();
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }

    try {
        if (connection!=null) {
            connection.close();
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }
    try {
        if (result!=null) {
            result.close();
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }
}

//加入事务之后更新数据的方法
public void CommonUpdate(Connection connection,String sql,Object ...args) throws Exception {
    //获取连接
    //Connection connection = null;
    PreparedStatement ps = null;
    try {
        //connection = JDBCUtils.getConnection();
        //预编译sql语句
        ps = connection.prepareStatement(sql);
        //占位符的填充
        for(int i = 0;i < args.length;i++){
            ps.setObject(i+1,args[i]);
        }
        ps.execute();
    } catch (Exception e) {
        e.printStackTrace();
    }finally {
        JDBCUtils.closeResources(null,ps);
    }

}

//通用的查询操作
   public  <T> T CommonSelect(Class<T> clazz,String sql,Object ...args) {
       Connection connection = null;
       PreparedStatement ps = null;
       ResultSet res = null;
       try {
           connection = JDBCUtils.getConnection();

           ps = connection.prepareStatement(sql);
           for(int i = 0;i < args.length;i++){
               ps.setObject(i + 1,args[i]);
           }
           //执行获取结果集,以及结果集的处理问题，包含多行
           res = ps.executeQuery();
           //获取结果集的元数据
           ResultSetMetaData metaData = res.getMetaData();
           //获取列数
           int columnCount = metaData.getColumnCount();
           if(res.next()){
               T t = clazz.newInstance();
               for(int i = 0; i < columnCount;i++){
                   //获取每一个列的列值
                   Object columnValue = res.getObject(i + 1);
                   //获取每一个列的列名
                   //String columnName = metaData.getColumnName(i + 1);
                   //获取每一个列的别名
                   String columnLabel = metaData.getColumnLabel(i + 1);
                   //反射
                   Field file = clazz.getDeclaredField(columnLabel);
                   file.setAccessible(true);
                   file.set(t,columnValue);

               }
               return t;
           }
       } catch (Exception e) {
           e.printStackTrace();
       }finally {
            JDBCUtils.closeResources(connection,ps,res);
       }
       return null;
   }
```

