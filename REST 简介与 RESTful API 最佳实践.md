### REST 简介

REpresentational State Transfer的缩写，直译为"表现层状态转化"。它是一种互联网应用程序的API设计理念：**用URI定位资源**，**用HTTP动词（GET，POST，DELETE，PUT）描述操作**。

#### URI定位资源

Uniform Resource Identifier的缩写，即统一资源标识符。服务器上每一种资源，比如文档、图像、视频片段、程序都由一个URI进行定位。

#### HTTP动词描述操作

常用的HTTP动词有下面五个

- GET（SELECT）：从服务器取出资源（一项或多项）。
- POST（CREATE）：在服务器新建一个资源。
- PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
- PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
- DELETE（DELETE）：从服务器删除资源。

### RESTful API 最佳实践

> 看URL就知道要操作的资源是什么
>
> 看HTTP Method就知道操作行为是什么，POST or DELETE
>
> 看HTTP Status Code就知道操作结果是什么

1. URL的根路径

```
http://api.chesxs.com/v1
```

2. 需要有API版本信息

```
http://api.chesxs.com/v1
```

3. URL中只使用名词指定资源，不用动词，且推荐使用复数

```
http://api.chesxs.com/v1/cars // 获取某个账户下的车辆列表
http://api.chesxs.com/v1/fences // 获取某个账户下的围栏列表
```

4. 用HTTP协议里的动词来实现资源的添加，修改，删除等操作

简单来说就是url地址中只包含名词表示资源，使用http动词表示动作进行操作资源
举个例子：左边是错误的设计，而右边是正确的

```
GET /blog/getArticles --> GET /blog/Articles  获取所有文章
GET /blog/addArticles --> POST /blog/Articles  添加一篇文章
GET /blog/editArticles --> PUT /blog/Articles  修改一篇文章 
GET /rest/api/deleteArticles?id=1 --> DELETE /blog/Articles/1  删除一篇文章
```

5. GET应该是安全的，不会改变资源状态

 get的时候就只是获取资源，而不涉及添加、更新、删除资源

6. 使用正确的HTTP Status Code返回状态码

|      | 类别                           | 原因                       |
| ---- | ------------------------------ | -------------------------- |
| 1xx  | Information(信息性状态码)      | 接受的请求正在处理         |
| 2xx  | Success(成功状态码)            | 请求正常处理完成           |
| 3xx  | Redirection(重定向状态码)      | 需要进行附加操作已完成请求 |
| 4xx  | Client Error(客户端错误状态码) | 服务器无法处理请求         |
| 5xx  | Server Error(服务器错误状态码) | 服务器处理请求出错         |

 一些常用的状态码：

- 200 OK 请求处理成功，返回相关信息
- 204 No Content 请求处理成功，但响应报文没有主题返回
- 206 Partial Content 客户端进行了范围请求，服务器成功执行请求并返回指定范围的实体内容
- 301 Moved Permanently 永久性重定向。请求的资源已经被分配到新的url
- 302 Found 临时性重定向
- 304 Not Modified 客户端发送附带条件的请求后，服务器允许请求，但内容并没修改，返回304。即客户端可以使用缓存的内容
- 400 Bad Request 请求报文存在语法错误。需要修正请求报文后再次发送请求
- 403 Forbidden 请求资源的访问被服务器拒绝。服务器没必要给出拒绝的理由。
- 404 Not Found 服务器上无法找到被请求的资源
- 500 Internet Server Error 服务器在执行请求时发生了错误。可能是Web应用存在的 bug 或者临时的障碍
- 503 Service Unavailable 服务器处于超载或者故障状态。如果事先得知何时可以解决故障，可以将时间写入Retry-after首部字段再返回给客户端。

7. 过滤信息

如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。

一些常见的参数：

```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?producy_type=1：指定筛选条件
```

8. 规范返回的数据

为了保障前后端的数据交互的顺畅，建议规范数据的返回，并采用固定的数据格式封装。

接口返回模板：

```
{
    status:0,
    data:{}||[],
    msg:’’
}
```

