# HTTP

本地传输找mac（物理地址）

网络传说找ip

送到哪个软件找端口



先通过ip地址判断是否在同一个子网，不是就发给默认网关，就是路由器，是就是通过交换机找mac地址

客户端发送请求，服务器返回响应

## 常用方法

|  方法  |     含义     |
| :----: | :----------: |
|  GET   |   获取资源   |
|  POST  |  创建新资源  |
|  PUT   | 完整更新资源 |
| PATCH  | 部分更新资源 |
| DELETE |   删除资源   |

## 请求结构

1.请求行 ：方法 + URL + HTTP版本

如： GET / index.html HTTP/1.1

2请求头 ：描述请求的信息

3请求体：可选择，存放要发送给服务器的数据



POST /api/users HTTP/1.1   



Host: www.example.com     
Content-Type: application/json   
Authorization: Bearer xyz123    
User-Agent: Mozilla/5.0...     



{"username": "小明", "age": 20}   





## 响应结构

1状态行：包含HTTP版本，状态码 状态信息

状态码

| 分类    | 含义           | 常见例子                         |
| :------ | :------------- | :------------------------------- |
| **1xx** | **信息性**     | 请求已收到，继续处理。（不常见） |
| **2xx** | **成功**       | 请求已被成功处理。               |
| **3xx** | **重定向**     | 需要客户端进一步操作以完成请求。 |
| **4xx** | **客户端错误** | 请求有语法错误或无法实现。       |
| **5xx** | **服务器错误** | 服务器处理请求时发生错误         |

2响应头：包含关于响应的元信息

3响应体：返回的信息，图片，代码等

HTTP/1.1 200 OK 



Content-Type: application/json; charset=utf-8
Content-Length: 89
Date: Fri, 01 Nov 2024 08:00:00 GMT
Server: nginx



{
  "id": 123,
  "name": "张三",
  "email": "zhangsan@example.com",
  "isActive": true
}



## d. URL, Query, Header, Body 的含义与作用

#### 1. URL

- **全称**：统一资源定位符。就是常说的“网址”。
- **作用**：唯一标识互联网上的一个资源地址。
- **例子**：`https://www.example.com:443/products/index.html`
  - `https`：协议
  - `www.example.com`：域名（服务器地址）
  - `:443`：端口号（HTTPS默认443，通常隐藏）
  - `/products/index.html`：资源路径

#### 2. Query

- **位置**：在URL中，跟在 `?`后面。
- **作用**：用于向服务器传递额外的参数，通常用于 `GET`请求。
- **格式**：`?key1=value1&key2=value2`
- **例子**：`https://www.example.com/search?q=HTTP&page=1`
  - 表示搜索关键词 `q`是 “HTTP”，要第 `1`页的结果。

#### 3. Header

- **作用**：HTTP的“元数据”，用于传递附加信息。**不包含主要数据内容，而是描述数据内容或请求/响应本身**。
- **常见请求头**：
  - `Host`：指定服务器的域名（必需）。
  - `User-Agent`：告诉服务器客户端的类型（如浏览器、操作系统）。
  - `Content-Type`：**告诉服务器请求体的数据类型**（如 `application/json`， `application/x-www-form-urlencoded`）。
  - `Authorization`：携带身份验证凭证（如Token）。
- **常见响应头**：
  - `Content-Type`：**告诉客户端响应体的数据类型**（如 `text/html; charset=UTF-8`）。
  - `Set-Cookie`：服务器向客户端设置Cookie。

#### 4. Body

- **作用**：存放**实际要传输的数据内容**。
- **常见于**：`POST`， `PUT`， `PATCH`请求。
- **响应中**：Body 就是服务器返回的网页、JSON数据等。
- **格式**：由请求头中的 `Content-Type`决定。
  - `application/json`: `{"username": "john", "age": 20}`
  - `application/x-www-form-urlencoded`: `username=john&age=20`
  - `multipart/form-data`: 用于上传文件















