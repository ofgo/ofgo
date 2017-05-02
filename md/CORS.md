# 跨域资源共享（CORS）

## 几个概念

### 同源策略

### HTTP跨域请求

## CORS

CORS即Cross-Origin Resource Sharing，中文译名跨域资源共享。
它是一个由WAWG(Web Applications Working Group)提出的规范，目的是让跨域数据传输变得更加安全。

## 使用实例

### 简单请求VS预请求

1. 简单请求

  形成条件：
    * 只使用`GET`,`HEAD`,`POST`方法。如果是`POST`,`Content-Type`只能是`application/x-www-form-urlencoded`,`multipart/form-data`,`text/plain`之一
    * 不含任何自定义请求头（header）

  跨域实现：
    * 响应头`Access-Control-Allow-Origin`值为`*`，或者是请求头`Origin`指明的域

2. 预请求

  形成条件：
    * 请求方法不是`GET`,`HEAD`,`POST`。如果是`POST`,`Content-Type`是`application/x-www-form-urlencoded`,`multipart/form-data`,`text/plain`以外的值。
    * 请求含自定义请求头

  预请求要求必须先发一个`OPTIONS`请求给目的服务器，以此查当前明跨域请求是否是安全可接受的。

### 附带Cookie的请求

设置XHR的`withCredentials`属性为`true`。

如果响应头中没有`Access-Control-Allow-Credentials: true`头，那么浏览器将不会把响应结果传递给请求脚本。

**注意**：对于带有`withCredentials`属性的请求，响应头`Access-Control-Allow-Origin`必须指明请求的域名，不能是`*`。

## Reference

[HTTP访问控制](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)