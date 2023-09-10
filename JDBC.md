JDBC

- from[廖雪峰]([JDBC连接池 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1321748528103458))

- 简介

JDBC是Java标准库提供的一套数据库访问标准接口，各数据库通过实现JDBC提供数据库驱动，驱动实际上就是一个jar包。我们通过引用JDBC接口，间接引用数据库驱动，通过网络访问（TCP）数据库服务器。

- JDBC连接数据库三板斧
  
  ```java
  // 1.获取连接
  try(Connection conn = DriverManager.getConnection(URL, USER, PASSWORD)) {
      // 2.获取查询
      try(PreparedStatement ps = conn.prepareStatement("select * from tb where grade = ?")){
          // 设置SQL模板参数
          ps.setObject(1, 100);
          // 3. 获取返回值
          try(ResultSet rs = ps.executeQuery()){
              while(rs.next()){
                  Long id = rs.getLong("id");
                  String name = rs.getString("name");
                  ...
  }}}}
  //用try()可以在使用完毕自动释放资源
  ```

- JDBC连接池

管理JDBC连接资源，工厂模式提供JDBC连接，内部维护若干Connect实例。当获取连接时，就选择一个空闲连接，并标记它为“正在使用”然后返回，释放后就把连接再次标记为“空闲”从而等待下次调用。

好处是节省连接资源，可灵活设置参数。

连接池创建也是昂贵的操作，通常连接池实例是全局变量，且贯穿应用程序整个生命周期。

```java
// 实例化连接池
HikariConfig config = new HikariConfig();
config.setJdbcUrl("jdbc:mysql://localhost:3306/test");
config.setUsername("root");
config.setPassword("password");
config.addDataSourceProperty("connectionTimeout", "1000"); // 连接超时：1秒
config.addDataSourceProperty("idleTimeout", "60000"); // 空闲超时：60秒
config.addDataSourceProperty("maximumPoolSize", "10"); // 最大连接数：10
DataSource ds = new HikariDataSource(config);

// 从连接池获取连接
try(ds.getConnect()){
...
}
```
