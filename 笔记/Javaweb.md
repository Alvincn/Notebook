# 配置Tomcat

首先创建**Javaweb**项目，操作如下

1. 创建正常的**Java**项目

   ![image-20220703202548316](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703202548316.png)

2. 新建项目上右键，选择**Add Framework Support...**

   ![image-20220703202704083](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703202704083.png)

3. 勾选**Web Application**

   ![image-20220703202746472](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703202746472.png)

4. 这样一个**Javaweb**项目就创建好了。

   ![image-20220703202943472](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703202943472.png)

然后配置**Tomcat**：

1. 点击**Add Configuration...**

   ![image-20220703203021472](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703203021472.png)

2. 点击加号

   ![image-20220703203047068](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703203047068.png)

3. 选中**Tomcat Server -> Local**

   ![image-20220703203157214](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703203157214.png)

4. 点击**Deployment**配置**Tomcat**中文件

   ![image-20220703203340831](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703203340831.png)

5. 这里的**Application context**代表进入的路径

   ![image-20220703203429820](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703203429820.png)

6. 按如下配置

   ![image-20220703203758314](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220703203758314.png)

# 表单示例

在**web**文件夹下创建**index.html**,代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/add" method="post">
        名称：<input type="text" name="fname"><br/>
        单价：<input type="text" name="price"><br/>
        库存：<input type="text" name="fcount"><br/>
        备注：<input type="text" name="remark"><br/>
        <input type="submit" value="添加">
    </form>
</body>
</html>
```

这里可以看见有一个**form**表单（想要提交必须设置name，带有name属性的数据可以发送给后台），提交路径为**/add**。

在**src**文件夹下，创建包**servlets**，在**servlets**文件夹下创建**Class** **AddSServlet**,编写如下代码：

```java
package servlets;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class AddServlet extends HttpServlet {
    @Override
    // 有两个参数，第一个是客户端发过来的请求，第二个是服务器端返回
   	// doPost方法，当有 POST 请求时会执行
    public void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求体的 fname
        String fname = req.getParameter("fname");
        // 获取请求体的 price
        String priceStr = req.getParameter("price");
        // 将 price 转换为 数值类型
        Integer price = Integer.parseInt(priceStr);
        // 获取请求体的 fcount
        String fcountStr = req.getParameter("fcount");
        // 将 fcount 转换为 数值类型
        Integer fcount = Integer.parseInt(fcountStr);
        // 获取请求体的 remark
        String remark = req.getParameter("remark");
        // 将获取到的内容输出
        System.out.println("fname = " + fname);
        System.out.println("price = " + price);
        System.out.println("fcount = " + fcount);
        System.out.println("remark = " + remark);
    }
}
```

这里可以直接输入**dopost**自动生成代码，这个继承如果报错，操作如下：

1. 点击左上角**File**
2. 点击**Project Structure...**
3. 点击**Modules**
4. 点击要添加的项目
5. 点击右边的**+ 点击**Add Selected**
6. 应用

在**WEB-INF**文件夹下**web.xml**文件中，编写**servlet**以及**servlet-mapping**，代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>AddServlet</servlet-name>
        <servlet-class>servlets.AddServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>AddServlet</servlet-name>
        <url-pattern>/add</url-pattern>
    </servlet-mapping>
</web-app>
```

此处的具体操作如下：

1. 首先填写表单，点击按钮进行提交，此时表单会将数据传输到**/add**此路径中
2. 在**<url-pattern></url-pattern>**中搜索到**/add**
3. 从而查找到对应的**servlet-mapping -> servlet-name**为**AddServlet**
4. 根据**AddServlet**在**servlet -> servlet-name**中查找
5. 从而查找到对应的**<servlet-class></servlet-class>**为**servlet.AddServ20020423zlet**
6. 从而查找到对应的**Java**类，并执行**AddServlet**中的**doPost**方法。

