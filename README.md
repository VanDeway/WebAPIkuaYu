# 解决WebAPI跨域访问的问题的一种方法(应该还有其他方法)
1、在服务端的Web.config文件里的<system.webServer>标签里加入如下内容：

   <pre>"
    <httpProtocol>
      <customHeaders>
        <!--响应类型 (值为逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法)-->
        <add name="Access-Control-Allow-Methods" value="GET,POST,PUT,DELETE,OPTIONS" />
        <!--响应头设置（Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain）-->
        <add name="Access-Control-Allow-Headers" value="x-requested-with,content-type" />
        <!--如果设置 Access-Control-Allow-Origin:*，则允许所有域名的脚本访问该资源-->
        <add name="Access-Control-Allow-Origin" value="*" />
        <!--<add name="Access-Control-Allow-Origin" value="http://domain1.com, http://domain2.com" />  设置允许跨域访问的网址-->
      </customHeaders>
    </httpProtocol>"
   </pre>

 <br/>
2、在服务端的Global.asax.cs文件里加入如下内容：
 <pre>
   <code>
    // 跨域设置
    protected void Application_BeginRequest()
    {
      //OPTIONS请求方法的主要作用：
      //1、获取服务器支持的HTTP请求方法；也是黑客经常使用的方法。
      //2、用来检查服务器的性能。如：AJAX进行跨域请求时的预检，需要向另外一个域名的资源发送一个HTTP OPTIONS请求头，用以判断实际发送的请求是否安全。
      if (Request.Headers.AllKeys.Contains("Origin") && Request.HttpMethod == "OPTIONS")
      {
        //表示对输出的内容进行缓冲，执行page.Response.Flush()时，会等所有内容缓冲完毕，将内容发送到客户端。
        //这样就不会出错，造成页面卡死状态，让用户无限制的等下去
        Response.Flush();
      }
    }
</code>
 </pre>
