# Unity3D 通过WWW获取服务器数据

## 1. 服务器

PHP

```php
 if (isset($_GET['username']) && isset($_GET['password'])){
     echo "Get -> username is ".$_GET['username'] . " and password is " . $_GET['password']."=====" .time();
 } else if (isset($_POST['username']) && isset($_POST['password'])){
     echo 'Post -> username is '.$_POST['username'] . " and password is ". $_POST['password']."=====". time();
 } else {
     echo '没有传递参数 error '. time();
 }

```

java

```java
package com.john.web.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * @author Administrator
 * @Created by Administrator
 * @Created with IntelliJ IDEA.
 * Basic5
 * @User: john
 * @Author: 张桓
 * @Email: yz30.com@aliyun.com
 * @QQ: 248404941
 * @Date: 2017/11/23
 * @Time: 19:40
 * 　　　　　　　　　　　　　　　 _ooOoo_
 * 　　　　　　　　　　　　　　　o8888888o
 * 　　　　　　　　　　　　　　　88" . "88
 * 　　　　　　　　　　　　　　　(| -_- |)
 * 　　　　　　　　　　　　　　　 O\ = /O
 * 　　　　　　　　　　　　　 ____/`---'\____
 * 　　　　　　　　　　　　 .   ' \\| |// `.
 * 　　　　　　　　　　　　 / \\||| : |||// \
 * 　　　　　　　　　　　　/ _||||| -:- |||||- \
 * 　　　　　　　　　　　     | | \\\ - /// | |
 * 　　　　　　　　　　　　　| \_| ''\---/'' | |
 * 　　　　　　　　　　　　 \ .-\__ `-` ___/-. /
 * 　　　　　　　　　　 ___`. .' /--.--\ `. . __
 * 　　　　　　　　 ."" '< `.___\_<|>_/___.' >'"".
 * 　　　　　　　　| | : `- \`.;`\ _ /`;.`/ - ` : | |
 * 　　　　　　　　   \ \ `-. \_ __\ /__ _/ .-` / /
 * 　　　　　======`-.____`-.___\_____/___.-`____.-'======
 * 　　　　　　　　　　　　　　　 `=---='
 * 　　　　　 .............................................
 * 　　　　　　　　　　佛祖保佑             永无BUG
 * 　　　　　佛曰:
 * 　　　　　　　　　　写字楼里写字间，写字间里程序员；
 * 　　　　　　　　　　程序人员写程序，又拿程序换酒钱。
 * 　　　　　　　　　　酒醒只在网上坐，酒醉还来网下眠；
 * 　　　　　　　　　　酒醉酒醒日复日，网上网下年复年。
 * 　　　　　　　　　　但愿老死电脑间，不愿鞠躬老板前；
 * 　　　　　　　　　　奔驰宝马贵者趣，公交自行程序员。
 * 　　　　　　　　　　别人笑我忒疯癫，我笑自己命太贱；
 * 　　　　　　　　　　不见满街漂亮妹，哪个归得程序员？
 */
@WebServlet("/demo")
public class Demo extends HttpServlet{

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        if ( username == null || password == null){
            response.getWriter().write("Get 方式请求的但是，没有传递参数");
        } else {
            response.getWriter().write("Get -> username is " + username + " and Password is " + password);
        }

    }


    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        if ( username == null || password == null){
            response.getWriter().write("Post 方式请求的但是，没有传递参数");
        } else {
            response.getWriter().write("Post -> username is " + username + " and Password is " + password);
        }
    }
}

```

## 2. Unity3D

```c#
using UnityEngine;
using System.Collections;
using System;
using System.Collections.Generic;

public class WebManager : MonoBehaviour {

    // 全局变量, 用来接收提示信息，初始化“Nothing”
    string m_info = "Nothing";

    private void OnGUI()
    {


        GUI.BeginGroup(new Rect(Screen.width * 0.1f, Screen.height * 0.1f, 800, 600), "张桓");       

        // 创建一个按钮，设置位置、大小，按钮上的标题为“Get Data”
        if (GUI.Button(new Rect(10, 50, 300, 30), "Get Data"))
        {
            // 这里写点击按钮所触发的行为、事件
            StartCoroutine(IGetData());


            //Debug.Log("点击按钮所触发的行为、事件  Get Data");
        }

        //创建一个按钮，设置位置、大小，按钮上的标题为“Post Data”。    
        if (GUI.Button(new Rect(10, 100, 300, 30), "Post Data"))
        {
            //这里写点击按钮所触发的行为、事件。    
            StartCoroutine(IPostData());


            //Debug.Log("点击按钮所触发的行为、事件 Post Data");

        }

        // 创建一个标签， 设置位置、大小,将接收信息提示的全局变量作为标签的文本内容
        GUI.Label(new Rect(10, 200, 800, 400), m_info);
        GUI.EndGroup();


    }



    // Use this for initialization
    void Start () {
	
	}
	
	// Update is called once per frame
	void Update () {
	
	}


    private IEnumerator IGetData()
    {
        // 使用Get的方式访问Http地址
        //WWW www = new WWW("http://localhost:8081/basic/demo1/demo.php?username=izhanghuan&password=ijohnhuan");
        WWW www = new WWW("http://202.118.16.50:8080/Basic5/demo?username=izhanghuan&password=ijohnhuan");

        //等待服务器相应
        yield return www;

        // 如果出现错误
        if (www.error != null)
        {
            // 获取服务器的错误信息
            m_info = www.error;
            yield return null;
        }

        // 获取服务器的相应文本
        m_info = www.text;

    }

    private IEnumerator IPostData()
    {
        Dictionary<string, string> headers = new Dictionary<string, string>();
        headers["Content-Type"] = "application/x-www-form-urlencoded";

        //将要发送的Post文本内容  
        string data = "username=izhanghuan&password=ijohnhuan";
        //将文本转为byte数组  
        byte[] bs = System.Text.UTF8Encoding.UTF8.GetBytes(data);

        //向HTTP服务器提交Post数据  
        WWW www = new WWW("http://202.118.16.50:8080/Basic5/demo", bs, headers);

        //等待服务器的响应  
        yield return www;

        //如果出现错误  
        if (www.error != null)
        {
            //获取服务器的错误信息  
            m_info = www.error;
            yield return null;
        }

        //获取服务器的响应文本  
        m_info = www.text;
    }
}

```

