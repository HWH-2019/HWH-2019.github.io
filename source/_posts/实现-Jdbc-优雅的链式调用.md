---
title: 实现 Jdbc 优雅的链式调用
author: HWH
math: true
mermaid: true
tags:
  - Jdbc
  - 设计模式
categories:
  - - Summarize
abbrlink: d530df2d
date: 2022-12-10 20:56:08
---

### 写在前面
- 今天用到了 `Jdbc` 进行数据库的一些操作，要写很多重复连接关闭的代码，刚好最近在学设计模式，就突发奇想浅浅的用一下设计模式，给它封装一下吧
- 写的很简单，也可能有很多不足的地方，欢迎评论指出哦。
- 只是设计模式的一个练习，不一定实用，大家且看看吧。

### 实现说明
#### 核心
- 链式调用
- 建造者模式
- 接口回调

#### 相关说明
##### 链式调用
- 链式调用: `chained invocation`(链式调用）或者`Method chaining`，这种风格的AP设计叫做`fluent API`或者`Fluent interface`
- 常用于`Builder Pattern`(建造者模式)。

##### 建造者模式
- 建造者模式是设计模式的一种，将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

##### 接口回调
- 接口回调是指：可以把使用某一接口的类创建的对象的引用赋给该接口声明的接口变量，那么该接口变量就可以调用被类实现的接口的方法。
- 实际上，当接口变量调用被类实现的接口中的方法时，就是通知相应的对象调用接口的方法，这一过程称为对象功能的接口回调。

### 源码部分
- 如何使用话已经总结在代码开头的注释了，连同方法作用也一并整理了。
- 好了直接看源码如下：

```java
package com.huang.util;

import java.sql.*;

/**
 * 描述：Jdbc 工具类 实现Jdbc的快速使用，链式调用
 *
 * 方法：Builder() -- 构建 jdbc 基本属性
 *      setUrl() -- 设置 jdbc url
 *      setName() -- 设置 jdbc 用户名
 *      setPwd() -- 设置 jdbc 密码
 *      -------------------------
 *      connect() -- 连接 初始化 Connection 对象
 *      -------------------------
 *      setSql() -- 设置 sql 语句, 初始化 PreparedStatement 对象
 *      -------------------------(待更新)
 *      setParamString() -- 设置 sql 语句中对应参数 字符串类型
 *      setParamInt() -- 设置 SQL 语句中对应参数 整型
 *      -------------------------(待更新)
 *      execute() -- 插入，删除语句执行
 *      executeQuery() -- 查询语句执行
 *      executeUpdate() -- 更新语句执行
 *      -------------------------
 *      setCallBack() -- 设置回调函数
 *
 * 使用：new JdbcUtil.Builder()
 *                 .connect()
 *                 .setSql("select * from user where email = ? and password = ?;")
 *                 .setParamString(1, email)
 *                 .setParamString(2, password)
 *                 .setCallBack(new CallBack() {
 *                     @Override
 *                     public void executeResult(ResultSet resultSet) {
 *                         super.executeResult(resultSet);
 *
 *                     }
 *                 })
 * 作者：HWH
 * 日期：2022-4-12 14:29
 * 邮箱：huangwenhuan2019@outlook.com
 */

public class JdbcUtil {


    /**
     * 接口回调
     */
    private interface ICallBack{
        void executeResult(ResultSet resultSet);
        void executeResult(int result);
        void executeResult(boolean result);
    }

    public static abstract class CallBack implements ICallBack{
        @Override
        public void executeResult(int result) {

        }

        @Override
        public void executeResult(boolean result) {

        }

        @Override
        public void executeResult(ResultSet resultSet) {

        }
    }

    public static class Builder{
        private String url;
        private String name;
        private String pwd;
        private Connection connection;
        private PreparedStatement preparedStatement;
        private CallBack callBack;



        /**
         * 构建 jdbc 基本属性
         */
        public Builder(){
            try {
                Class.forName("com.mysql.cj.jdbc.Driver");
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }
            //获取连接
            url = "jdbc:mysql://localhost:3306/webservice?serverTimezone=GMT";
            name = "root";
            pwd = "15715293569";
        }

        /**
         * 设置 jdbc url
         * @param url jdbc 的 url
         * @return Builder 对象
         */
        public Builder setUrl(String url) {
            this.url = url;
            return this;
        }

        /**
         * 设置 jdbc 用户名
         * @param name jdbc 的 用户名
         * @return Builder 对象
         */
        public Builder setName(String name) {
            this.name = name;
            return this;
        }

        /**
         * 设置 jdbc 密码
         * @param pwd jdbc 的 密码
         * @return Builder 对象
         */
        public Builder setPwd(String pwd) {
            this.pwd = pwd;
            return this;
        }

        /**
         * 连接 初始化 Connection 对象
         * @return Builder 对象
         */
        public Builder connect(){
            try {
                connection = DriverManager.getConnection(url, name, pwd);
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
            return this;
        }

        /**
         * 设置 sql 语句, 初始化 PreparedStatement 对象
         * @param sql sql 语句 字符串
         * @return Builder 对象
         */
        public Builder setSql(String sql){
            try {
                if(connection==null) throw new Exception("connection is null, please invoke connect() to create it.");
                preparedStatement = connection.prepareStatement(sql);
            } catch (Exception throwables) {
                throwables.printStackTrace();
            }
            return this;
        }

        /**
         * 设置 sql 语句中对应参数 字符串类型
         * @param index 参数索引
         * @param value 参数值
         * @return Builder 对象
         */
        public Builder setParamString(int index,String value){
            try {
                if(preparedStatement!=null){
                    preparedStatement.setString(index,value);
                }else{
                    throw new Exception("preparedStatement is null, please invoke the setSql() to create it.");
                }
            }catch (Exception e){
                e.getStackTrace();
            }
            return this;
        }

        /**
         * setParamInt() -- 设置 SQL 语句中对应参数 整型
         * @param index 参数索引
         * @param value 参数值
         * @return Builder 对象
         */
        public Builder setParamInt(int index,int value){
            try {
                if(preparedStatement!=null){
                    preparedStatement.setInt(index,value);
                }else{
                    throw new Exception("preparedStatement is null, please invoke the setSql() to create it.");
                }
            }catch (Exception e){
                e.getStackTrace();
            }
            return this;
        }

        /**
         * 插入，删除语句执行
         */
        public void execute(){
            boolean result = false;
            try {
                result = preparedStatement.execute();
                callBack.executeResult(result);
                preparedStatement.close();
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        /**
         * 查询语句执行
         */
        public void executeQuery(){
            ResultSet resultSet = null;
            try {
                resultSet = preparedStatement.executeQuery();
                callBack.executeResult(resultSet);
                resultSet.close();
                preparedStatement.close();
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        /**
         * 更新语句执行
         */
        public void executeUpdate(){
            int result = 0;
            try {
                result = preparedStatement.executeUpdate();
                callBack.executeResult(result);
                preparedStatement.close();
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        /**
         * 设置回调函数
         * @param callBack 回调函数
         * @return Builder 对象
         */
        public Builder setCallBack(CallBack callBack){
            this.callBack = callBack;
            return this;
        }

    }
}

```

### 写在最后
- 感觉对于一般的数据库操作已经覆盖了，当然还有很多其他的方法没有实现，可以在必要的时候添加。
