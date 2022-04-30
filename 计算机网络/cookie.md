# cookie

## 1. 为什么有 cookie

http 协议 --> 无状态协议

客户端向服务端请求连接
服务端返回的响应头里有字段：set-cookie，返回 cookie 给客户端 （前端无法触及）
客户端收到 cookie 之后，会将其保存下来（浏览器完成，无关前端）
之后客户端再继续向服务端通信都会携带上 cookie，以供多个请求的关联
但是，每次请求都携带上 cookie，会增加网络的开销。所以使用 cookie 的时候，应该注意只存储一些轻量而重要的数据（例如登录状态）。

# cookie 的特性

## 1. cookie 不可跨域

但是可以通过设置 domain 这个属性，来实现跨一级域名和二级域名（例如：登陆www.baidu.com，然后打开zhidao.bai.com,也是登陆状态）

## 2. cookie 存储在浏览器里面

准确的说，应该是 cookie 存储在浏览器安装目录下的某个文件内，只要是相同域名下的网站，浏览器就可以调用该域名的 cookie，来保持关联性
但是由于每个浏览器的安装目录都不一样，所以在 A 浏览器登录的账号，到 B 浏览器要重新登录（在 Google 登录百度，到 Safari 还要重新登录一次）

## 3. cookie 有数量与大小的限制

      1、数量在50个左右（超过数量浏览器会删除cookie，每个浏览器的删除规则不一样，有的是最长时间没登录的，有的是随机删除）
      2、大小在4kb左右

## 4. cookie 的存储时间非常灵活

可以设置页面关闭就立马删除(会话期)，也可以设置几个月或者明年再删除

## 5. cookie 不光可以服务器设置，客户端（前端开发者）也可以设置

document.cookie(可读可写) 形式 --> key:value
document.cookie = "name=muggle; age=17";
console.log(document.cookie); // name=muggle age=17 这个字符串无效，cookie 一次只能设置一条键值对
document.cookie = "age=17"; // 重新再设置一次 age=17 的 cookie
console.log(document.cookie); // name=muggle; age=17

## 6.cookie 的属性

### 1、name --> cookie 的名字(name 就是键值对里的键，具有唯一性)

```    		
document.cookie = "name=daye";
document.cookie = "age=20";
console.log(document.cookie); // name=daye; age=20,name和age这两个键和上面的键重复了，值就会覆盖上面的“muggle”和“17”
```

### 2、value --> cookie 的值

### 3、domain --> 设置 cookie 对哪个域是有效的(默认是当前页面的域名)

### 4、path --> 设置 cookie 的路径

domain 和 path 一个设置 cookie 的有效域名，一个设置 cookie 的有效路径

### 5、expires --> 设置 cookie 的过期时间(默认为会话期)(http1.0)

日期的格式是 GMT(格林尼治标准时间，中国按东八区的时间)
document.cookie = "margin=20; expires=" + new Date(2028, 1, 1);

### 6、max-age --> cookie 的有效期(和 expires 一样，用于设置 cookie 的有效周期)(http1.1)

和 expires 的不同： - expires 设置的是 cookie 过期的时间点，max-age 的值是设置 cookie 能存在多长时间。比如 max-age=300；则 cookie 会在 300 秒后删除 - 协议版本不同
几种特殊的值：—1(会话期临时 cookie，关闭窗口就消失)
0(有效期已到)
document.cookie = "padding=30; max-age=1";

### 7、HttpOnly 有这个标记的 cookie，前端无法获取，只能在浏览器 --> 应用 --> cookie 里看到

### 8、Secure 设置这个 cookie 只能通过 https 传输

### 9、SameSite 设置 cookie 在跨域请求的时候不能被发送(有这个设置的 cookie，在网络请求的时候不会被发送到服务端)