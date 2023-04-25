---
title: 解决 WebService 返回 DataSet XML 数据解析问题
author: HWH
math: true
mermaid: true
tags:
  - WebService
categories:
  - - Solutions
abbrlink: 8dc87216
date: 2022-12-10 20:59:25
---

### 写在前面
- 最近在写 WebService 时，遇到了使用第三方的 WebService 生成客户端代码后，对于请求返回的 DataSet 数据类型无法正确解析的问题。
- 经过近一天的尝试终于解决此问题，在此记录并分享。
- 这里贴上常用的第三方 WebService，[常用的 WebService](http://www.webxml.com.cn/zh_cn/web_services.aspx?offset=1)

###  问题描述
- 使用 `IntelliJ IDEA 2020.2.3` 中提供的 `Glassfish /JAX-WS 2.2 RI / Metro 1.X/ JwSDP2.2` 方式生成客户端代码

![](https://img-blog.csdnimg.cn/3f1c22052f8e42bebe208870e233810f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 在调用请求返回的 DataSet 数据类型无法正确解析
- 返回结果 `[[xs:schema: null], [diffgr:diffgram: null]]`


### 解决方法
- 使用 `Axis` 生成客户端代码(`IntelliJ IDEA` 的可能存在问题，实在不行可以试试 `eclipse`)

![](https://img-blog.csdnimg.cn/57af88dc0d3e4339b3c00825b7e3d3cc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 使用代理对象返回数据（此处以获取邮政编码为例）

```java
ChinaZipSearchWebServiceSoapProxy chinaZipSearchWebServiceSoapProxy = new ChinaZipSearchWebServiceSoapProxy();

GetAddressByZipCodeResponseGetAddressByZipCodeResult addressByZipCode = chinaZipSearchWebServiceSoapProxy.getAddressByZipCode("212300", "");

MessageElement[] any = addressByZipCode.get_any();
```

- 返回的 `any` 即为包含目标数据的`XML` 结果
- 手动编写代码对 `XML` 进行解析并获得数据

### 写在最后
- 我对 XML 的解析操作进行了封装，将其封装成一个工具类，具体代码如下：

```java
package com.huang.util;

import org.apache.axis.message.MessageElement;
import org.jdom2.Document;
import org.jdom2.Element;
import org.jdom2.JDOMException;
import org.jdom2.input.SAXBuilder;

import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * 描述：WebService 返回 DataSet XML 数据解析
 *
 * 方法：DataSetXmlToListMap(MessageElement[] any,List<String> elemNames)
 *      -- 解析 any 中  elemNames 指定的数据
 *
 * 使用场景：WebService 采用 Axis 生成客户端代码，服务请求返回结果为 DataSet XML
 *
 * 作者：HWH
 * 日期：2022-4-24 20:42
 * 邮箱：huangwenhuan2019@outlook.com
 */
public class WebServiceXmlUtil {

    /**
     * 解析 any 中  elemNames 指定的数据
     * @param any MessageElement 数组对象
     * @param elemNames List<String> 为解析指定的字段名
     * @return List<Map<String,Object>> any 中  elemNames 指定的数据，其中 Map 键为小写的 elemNames 中的元素
     * @throws JDOMException
     * @throws IOException
     */
    public static List<Map<String,Object>> DataSetXmlToListMap(MessageElement[] any,List<String> elemNames) throws JDOMException, IOException {
        List children = any[1].getChildren();
        List<Map<String, Object>> list = new ArrayList<>();
        SAXBuilder sb = new SAXBuilder();
        for (Object child : children) {
            String str = child.toString();
            InputStream in = new ByteArrayInputStream(str.getBytes(StandardCharsets.UTF_8));
            Document doc = sb.build(in);
            Element root = doc.getRootElement(); // 得到根元素
            List<Element> focs = root.getChildren();
            for (Element o : focs) {
                Map<String, Object> tmp = new HashMap<>();
                for(String elemName : elemNames){
                    tmp.put(elemName.toLowerCase() , o.getChild(elemName).getText());
                }
                list.add(tmp);
            }
        }
        return list;
    }

}

```
