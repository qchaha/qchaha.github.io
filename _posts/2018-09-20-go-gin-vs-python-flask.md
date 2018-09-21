---
layout: post
title: web框架Gin和flask的简单对比
--- 

最近在学习go，看了挺多关于说go性能好的评价，打算亲自测试下，也当做是用来练练手。
我们先来分别看看两者的介绍：

<br>
### 1. flask框架

Flask是一个使用 Python 编写的轻量级 Web 应用框架。其 WSGI 工具箱采用 Werkzeug ，模板引擎则使用 Jinja2 。Flask使用 BSD 授权。  

在这里，我写了一段用来接受请求的代码：

```python

#!/usr/bin/env python3
#coding:utf-8

from flask import Flask,jsonify,request

app = Flask(__name__)

@app.route('/test', methods = ['GET'])
def fetch_operation_tag():
    return jsonify({'name':'test'})

@app.route('/tp', methods = ['POST'])
def a():
    a = request.json['v1']
    b = request.json['v2']
    return jsonify({"v1":a,"v2":b})

if __name__ == '__main__':
    app.run(host='0.0.0.0',port=8888,debug=False,threaded=True)

```

内容很简单，两条路由，分别接受GET请求和POST请求。

GET请求直接返回`{"name":"test"}`。

POST请求返回请求的内容`{"v1":"第一个值","v2":"第二个值"}`

<br>
### 2. Gin框架

Gin 是一个用 Go 语言编写的 WEB 框架。它具有和 maritini 类似的 API 并拥有更好的性能， 感谢 **[httprouter](https://github.com/julienschmidt/httprouter)** 使它的速度提升了 40 倍。如果你需要性能和良好的生产力，你将会爱上 Gin 。

下面是用于接受请求的go代码，为了测试的公平性，代码功能最大限度跟py的一致：
```go

package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/test", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "test",
		})
	})
	r.POST("/tp", func(c *gin.Context) {
		type jbody struct {
			V1 string `json:"v1"`
			V2 string `json:"v2"`
		}
		var val jbody
		c.BindJSON(&val)
		c.JSON(200, gin.H{
			"v1": val.V1, "v2": val.V2,
		})
	})
	r.Run(":8888")
}

```

跟py一致，两条路由：

GET请求直接返回`{"name":"test"}`。

POST请求返回请求的内容`{"v1":"第一个值","v2":"第二个值"}`。

<br>
### 3. 测试对比

测试对比在这里分为2组：

第一组：分别用py和go写一段调用系统命令curl请求的循环；

第二组：分别用py下的requests包和go下的net/http包进行循环请求；

#### 3.1 调用系统curl命令

现在我们来看看py下的调用代码：

```python

#!/usr/bin/env python3

import requests
import datetime
import subprocess

a = 0
starttime = datetime.datetime.now()
while True:
    a = a + 1
    if a > 99:
        break
    subprocess.call("curl 127.0.0.1:8888/test -H \"Content-Type:application/json;charset=UTF-8\">/dev/null 2>&1", shell=True)
endtime = datetime.datetime.now()
print("py-curl-get:{}.{}s".format((endtime - starttime).seconds,(endtime - starttime).microseconds))

a = 0
starttime = datetime.datetime.now()
while True:
    a = a + 1
    if a > 99:
        break
    subprocess.call("curl -X POST 127.0.0.1:8888/tp --data '{\"v1\":\"123\",\"v2\":\"456\"}' -H \"Content-Type:application/json;charset=UTF-8\" > /dev/null 2>&1",shell=True)
endtime = datetime.datetime.now()
print("py-curl-post:{}.{}s".format((endtime - starttime).seconds,(endtime - starttime).microseconds))

```

flask框架，py调用curl命令请求方式，循环次数99，调用次数3：

![flask框架，curl命令请求方式，循环次数99，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/flask-curl-99-python.png)


Gin框架，py调用curl命令请求方式，循环次数99，调用次数3：

![Gin框架，curl命令请求方式，循环次数99，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/gin-curl-99-python.png)


下面则是go下的调用代码：
```go

package main

import (
	"fmt"
	"time"
	"os/exec"
	"os"
)

func shellExe(ip, port string){
	var cmd *exec.Cmd
	var err error
	var output []byte
	count := 0
	start := time.Now()
	var end time.Time
	for true{
		count++
		if count > 99{
			break
		}
		//cmdStr := fmt.Sprintf("curl -X POST %s:%s/tp --data '{\"v1\":\"123\",\"v2\":\"456\"}' -H \"Content-Type:application/json;charset=UTF-8\"", ip, port)
		cmdStr := fmt.Sprintf("curl %s:%s/test -H \"Content-Type:application/json;charset=UTF-8\" > /dev/null 2>&1", ip, port)
		cmd = exec.Command("/bin/bash", "-c", cmdStr)
		
		if output, err = cmd.Output();  err != nil {
			fmt.Println(err)
			fmt.Println(string(output))
			os.Exit(1)
		}
		//fmt.Println(string(output))
	}
	end = time.Now()
	fmt.Printf("go-curl-get: %s\n",end.Sub(start))

	count = 0
	start = time.Now()
	for true{
		count++
		if count > 99{
			break
		}
		cmdStr := fmt.Sprintf("curl -X POST %s:%s/tp --data '{\"v1\":\"123\",\"v2\":\"456\"}' -H \"Content-Type:application/json;charset=UTF-8\"  > /dev/null 2>&1", ip, port)
		cmd = exec.Command("/bin/bash", "-c", cmdStr)
		
		if output, err = cmd.Output();  err != nil {
			fmt.Println(err)
			fmt.Println(string(output))
			os.Exit(1)
		}
		//fmt.Println(string(output))
	}
	end = time.Now()
	fmt.Printf("go-curl-post: %s\n",end.Sub(start))
}

func main(){
	shellExe("127.0.0.1", "8888")
	//conRequest("127.0.0.1", "8888")
}

```

flask框架，go调用curl命令请求方式，循环次数99，调用次数3。

![flask框架，curl命令请求方式，循环次数99，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/flask-curl-99-go.png)


Gin框架，go调用curl命令请求方式，循环次数99，调用次数3。

![Gin框架，curl命令请求方式，循环次数99，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/gin-curl-99-go.png)


我们不难发现，上面四种调用请求的方式，处理时间上几乎一样，并没有明显不同，难道说两个框架性能相当吗？go也没有传说中的那么快。会不会是循环次数不够多看不出效果，然后我把循环次数加到了999，发现结果就是99次循环的结果乘以10。真的是这样的吗？
不急不急，我们再继续往下看。


#### 3.2 调用python下的requests进行请求
调用代码如下，我们先循环99次看看情况：

```python

#!/usr/bin/env python3

import requests
import datetime
import time

a = 0 
starttime = datetime.datetime.now()
while True:
    a = a + 1
    if a > 99:
        break

    r = requests.get("http://127.0.0.1:8888/test")
    if a == 99:
    	print(r.json())   

endtime = datetime.datetime.now()
print("py-requests-get: {}.{}s".format((endtime - starttime).seconds,(endtime - starttime).microseconds))

a = 0 
starttime = datetime.datetime.now()
while True:
    a = a + 1
    if a > 99:
        break

    r = requests.post("http://127.0.0.1:8888/tp", json={"v1":"123","v2":"456"})
    if a == 99:
    	print(r.json())   

endtime = datetime.datetime.now()
print("py-requests-post: {}.{}s".format((endtime - starttime).seconds,(endtime - starttime).microseconds))

```

flask框架，py调用requests包请求方式，循环次数99，调用次数3。

![flask框架，py调用requests包请求方式，循环次数99，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/flask-requests-99-python.png)

哇天啊，你没看错，同样是循环99次，通过调用requests方式请求，耗时只需要大概0.3s，不激动，我们再看看调用requests方式对Gin框架进行请求，结果会如何？

Gin框架，py调用requests包请求方式，循环次数99，调用次数3。

![Gin框架，py调用requests包请求方式，循环次数99，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/gin-requests-99-python.png)

结果类似的，不过看上去好像Gin框架要比flask框架快那么一点，耗时大概为0.22s。为了进一步验证，我们把循环次数改到999：

flask框架，py调用requests包请求方式，循环次数999，调用次数3。

![flask框架，py调用requests包请求方式，循环次数999，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/flask-requests-999-python.png)

Gin框架，py调用requests包请求方式，循环次数999，调用次数3。

![Gin框架，py调用requests包请求方式，循环次数999，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/gin-requests-999-python.png)

结果也是正相关，大概就是两者99次循环的耗时乘以10。**看得出随着请求次数不断增多，差距会逐渐拉大。**

#### 3.2 调用go下的net/http包进行请求
调用代码如下，我们先循环99次看看情况：
```go

package main

import (
	"encoding/json"
	"fmt"
	"time"
	"net/http"
	"io/ioutil"
	"bytes"
)

func conRequest(ip, port string){
	count := 0
	start := time.Now()
	var end time.Time
	srv := fmt.Sprintf("http://%s:%s/test", ip, port)
	for {
		count ++ 
		if count > 99 {
			break
		}
		resp, err := http.Get(srv)
		if err != nil{
			fmt.Println(err)
		}
		defer resp.Body.Close()
		body, err := ioutil.ReadAll(resp.Body)
		if count == 99 {
			fmt.Println(string(body))
		}
	}
	end = time.Now()
	fmt.Printf("go-http-get: %s\n",end.Sub(start))

	values := map[string]string{"v1": "123", "v2": "456"}
	jsonValue, _ := json.Marshal(values)
	srv = fmt.Sprintf("http://%s:%s/tp", ip, port)
	count = 0
	start = time.Now()
	for {
		count ++ 
		if count > 99 {
			break
		}
		resp, err := http.Post(srv, "application/json", bytes.NewBuffer(jsonValue))
		if err != nil{
			fmt.Println(err)
		}
		defer resp.Body.Close()
		body, err := ioutil.ReadAll(resp.Body)
		if count == 99 {
			fmt.Println(string(body))
		}
	}
	end = time.Now()
	fmt.Printf("go-http-post: %s\n",end.Sub(start))
}	

func main(){
	//shellExe("127.0.0.1", "8888")
	conRequest("127.0.0.1", "8888")
}

```

flask框架，go调用net/http包get请求方式，循环次数99，调用次数3。

![flask框架，go调用net/http包get请求方式，循环次数99，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/flask-http-99-go.png)

哇，你没看错，用go调用net/http包的get方式请求flask框架，时间进一步缩短，99次循环请求仅用0.11s左右。非常期待用这种方式请求Gin有没有更惊人的速度。

Gin框架，go调用net/http包请求方式，循环次数99，调用次数3。

![Gin框架，go调用net/http包get请求方式，循环次数99，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/gin-http-99-go.png)

!!!!!!!惊人的，用go调用net/http包的方式get请求Gin框架，99次循环请求仅耗时0.03s左右，这终于抛开flask几个马位了。

下面试试把循环次数加到999。

flask框架，go调用net/http包get请求方式，循环次数999，调用次数3。

![flask框架，go调用net/http包get请求方式，循环次数999，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/flask-http-999-go.png)

Gin框架，go调用net/http包get请求方式，循环次数999，调用次数3。

![Gin框架，go调用net/http包get请求方式，循环次数999，调用次数3](/images/2019-09-20-go-gin-vs-python-flask/gin-http-999-go.png)

可以看出，两种情况下的耗时也是对应情景下99次循环乘以10的结果，符合**线性关系。**

<br>
### 4.总结

下面把上述对比结果汇总到表格：

|  循环次数   |  请求方式            | flask框架处理耗时    |  Gin框架处理耗时  |
| :--------: | :--------------- : | :-----------------: | :--------------:|
|     99     | py调用curl-get      | 2.25s               |  2.34s         |
|     99     | go调用curl-get      | 2.34s               |  2.41s         |
|     99     | py调用curl-post     | 2.34s               |  2.33s         |
|     99     | go调用curl-post     | 2.36s               |  2.48s         |
|     99     | py调用requests-get  | 0.27s               |  0.22s         |
|     99     | go调用http-get      | 0.11s               |  0.03s         |
|     99     | py调用requests-post | 0.29s               |  0.24s         |
|     99     | go调用http-post     | 0.11s               |  0.03s         |
|     999    | py调用requests-get  | 2.87s               |  2.51s         |
|     999    | go调用http-get      | 1.04s               |  0.25s         |
|     999    | py调用requests-post | 3.25s               |  2.69s         |
|     999    | go调用http-post     | 1.15s               |  0.31s         |

