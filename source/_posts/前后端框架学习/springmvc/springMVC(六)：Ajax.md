---
title: springMVC(六)：Ajax
categories: 前后端框架学习
date: 2022-12-17  16:22:33
tags: 
- springMVC
- 框架 
---

## 异步与同步

- 异步传输是面向字符的传输，它的单位是字符；
- 而同步传输是面向比特的传输，它的单位是帧，它传输的时候要求接收方和发送方之间的时钟是一致的。

同步的话，必须这个操作完了才会执行下一步，在等待期间浏览器会挂起不执行下面的js代码；异步则是【告诉】浏览器去做，【告诉】是一瞬间的事情，然后继续执行下一步，等到结果返回之后，浏览器会通知js执行相应的回调。

## 理解

- ajax全称Asynchronous JavaScript and XML（异步的JavaScript 和 XML）
- AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
- **Ajax 不是一种新的编程语言，而是一种用于创建更好更快以及交互性更强的Web应用程序的技术。**
- Google Suggest 使用 AJAX 创造出动态性极强的 web 界面：当您在谷歌的搜索框输入关键字时，JavaScript 会把这些字符发送到服务器，然后服务器会返回一个搜索建议的列表
- 传统的网页(即不用ajax技术的网页)，想要更新内容或者提交一个表单，都需要重新加载整个网页。
- 使用ajax技术的网页，通过在后台服务器进行少量的数据交换，就可以实现异步局部更新。
- 使用Ajax，用户可以创建接近本地桌面应用的直接、高可用、更丰富、更动态的Web用户界面。

## 简单的伪造ajax

我们可以使用前端的一个标签来伪造一个ajax的样子。iframe标签

点击提交按钮，就会把文本框里的网址内容动态的加载到内联框中，页面内容发生变化，但我们并没有刷新页面
```html
<head>
    <meta charset="UTF-8">
    <title>iframe测试体验页面无刷新</title>
    <script>
        function go(){
            //所有的值变量，提前获取
            let url = document.getElementById("url").value;
            document.getElementById("iframe1").src = url;
        }
    </script>
</head>
<body>
<div>
    <p>请输入地址</p>
    <p>
        <input type="text" id="url" value="https://www.bilibili.com/">
        <input type="button" value="提交" onclick="go()">
    </p>
</div>
<!--内联网页，用来显示上面的网址内容-->
<div>
    <iframe id = "iframe1" style="width: 100%;height:500px"></iframe>
</div>

</body>
```

## ajax的实现

### xhr

- 纯JS原生实现Ajax我们不去讲解这里，直接使用jquery提供的，方便学习和使用，
避免重复造轮子，有兴趣可以去了解下JS原生XMLHttpRequest ！
- Ajax的核心是XMLHttpRequest对象(XHR)。XHR为向服务器发送请求和解析服务器响应提供了接口。能够以异步方式从服务器获取新数据。
- jQuery Ajax本质就是 XMLHttpRequest，对他进行了封装，方便调用！

利用AJAX可以做：
- 注册时，输入用户名自动检测用户是否已经存在。
- 登陆时，提示用户名密码错误
- 删除数据行时，将行ID发送到后台，后台在数据库中删除，数据库删除成功后，在页面DOM中将数据行也删除。

### 测试1

- 配置环境，创建Ajax控制类
```java
@Controller
@RequestMapping(value = "/ajax", produces = "application/json;charset=utf-8")
public class AjaxController {
    //从前端获取name，判断是不是管理员，如果是，在控制台输出yes，否则输出no
    @RequestMapping(value = "/a1", produces = "application/json;charset=utf-8")
    public void test(String name, HttpServletResponse response) throws IOException {
        if ("admin".equals(name)) {
            response.getWriter().print("yes");
        } else {
            response.getWriter().print("no");
        }
    }
}
```
- 2.前端页面
```html
<head>
    <title>hanser</title>
    <!--使用jquery，就要先引入他的包-->
      <script src="${pageContext.request.contextPath}/static/js/jquery-3.6.0.js"></script>
    <script type="text/javascript">
      
      function test(){
          /* ajax我们只需要配置几个属性，只有url是必须的，且可以写成一行
          *  url：请求地址，就是，前端请求后端的地址
          *  data：要发送的数据，就是从前端传过来的参数，交给后端
          *  success:成功之后执行的回调函数(全局),就是完事之后，要执行的操作
          * */
          $.ajax({
              url:"${pageContext.request.contextPath}/ajax/a1",
              data:{"name":$("#userName").val()},
              success:function (data,status){
                  console.log(data)
                  console.log(status)
              }
          });
      }
    </script>
</head>
<body>

<!--onblur代表失去焦点，当文本框失去焦点出发test()函数-->
用户名：<input type="text" id="userName"  onblur="test()">
</body>
```
这样，在前端文本框失去焦点时，观察到网络中有一个xhr的请求，实现的ajax





