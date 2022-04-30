# Web Storage

- 大小：5mb(cookie 4kb) 1025kb \* 5
- 永远不会过期，只能手动删除
- 和 cookie 一样<不能跨域>
- 一般用于保持用户偏好设置(颜色、主题)、搜索引擎历史输入和购物车

## WebStorage 提供的两个本地存储对象：

- **LocalStorage(整个域名下、所有的页面都有效)**
- **SessionStroage(会话级、仅在当前窗口有效)**

# LocalStorage 的属性：

## 1.length 本地存储数据的个数

    		console.log(localStorage.length);

## 2.key() 通过索引找到存储的数据(鸡肋，因为 local storage 并不是按数据储存的时间排序)

## 3.getItem() 通过 key 得到本地存储的 value

## 4.setItem() 设置一个本地存储数据

```
localStorage.setItem("name", "muggle");
localStorage.setItem("age", "17");
localStorage.setItem("sex", "male");
localStorage.setItem("hobby", "sleep");
let cat = ["muggle", "daye"];
localStorage.setItem("cat", cat); // 能够解析出数组里面的数据
let test = { t1: 1, t2: 2 };
localStorage.setItem("test", test); // 对象的话会显示 [object Object]，需要把对象转换成json存储，想要获得数据再用json.parse()进行转换
let test2 = { t1: "1", t2: "2" };
localStorage.setItem("test2", JSON.stringify(test2));
console.log(JSON.parse(localStorage.getItem("test2"))); // {t1: '1', t2: '2'}
```

## 5.removeItem() 删除一个本地存储数据

## 6.clear() 清空本地存储数据

> 无痕模式 --> 浏览器不会保持 localstorage(生成一个临时数据库存放 localstorage，页面关闭则删除临时数据库)

localstorage 的方法(跨页面，实现页面同步数据)



