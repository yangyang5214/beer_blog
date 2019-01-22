---
title: jdbc
tags: java
notebook: 
---



### jdbc 连接步骤

- 注册驱动
- 获取链接
- 新建语句
- 查询
- 关闭链接

```
package com.beer.interview.basic;

import java.sql.*;

/**
 * Created by beer on 18-12-20.
 */
public class JDBCService {


    public static void main(String[] args) {

        String url = "jdbc:mysql://localhost:3306/ruting";
        String username = "debian-sys-maint";
        String pwd = "S9VjvHHxCqzrkuSB";

        try {

            //注册驱动
            Class.forName("com.mysql.jdbc.Driver");

            //获取链接
            Connection connection = DriverManager.getConnection(url, username, pwd);

            //新建语句
            Statement statement = connection.createStatement();

            //查询结果
            ResultSet resultSet = statement.executeQuery("select name from company");
            while (resultSet.next()) {
                String name = resultSet.getString("name");
                System.out.println(name);
            }

            //关闭链接
            connection.close();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

}

```