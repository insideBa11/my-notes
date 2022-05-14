# BlogList

## 1.获取数据

导入`api`，获取模拟数据：

```javascript
import fetchData from '@/mixins/fetchData' // 组件混入
import {getBlogs} from '@/api/blog'
```

```javascript
// getBlogs:
export async function getBlogs(page = 1, limit = 10, categoryid = -1) {
	return await request.get("/api/blog", {
		params: {
			page,
			limit,
			categoryid,
		},
	});
}
```

采用计算属性的方法来实时获取数据：

```javascript
// 1. 通过组件混入，返回数据 data 和 loading
methods:{
      async fetchData() {
      return await getBlogs(this.routeInfo.routePage, 
                            this.routeInfo.routeLimit,
                            this.routeInfo.blogCategory)
    },
}
```

----

## 2.获取路由参数

**在安装`vue-router`时，会默认注入一个全局属性`$router`，可以通过这个属性来获取路由信息**

```javascript
// 为了实时获取模拟数据和路由参数，采用 computed
computed:{
		// 如果 getBlogs 还没返回数据，则返回 null
    blogsData() {
      if (this.data) return this.data.rows;
      else return null
    },
    // 获取到的 blogData 就可以挂载到 VNode上 
     
    routeInfo() {
      // 通过属性 $route 来获取路由信息
      // 如果 $route 有值，使用 $route 的值(str)，反之对其设置一个默认值
      const routePage = +this.$route.query.page || 1;
      const routeLimit = +this.$route.query.limit || 10;
      const blogCategory = +this.$route.params.categoryId || -1;
      return {
        routePage,
        routeLimit,
        blogCategory
      }
    }
}
```

---

## 3.引入页码组件，将路由信息与其挂钩

<img src="http://mdrs.yuanjin.tech/img/20201113130301.png" style="zoom:50%;" />

### 属性

| 属性名        | 含义       | 类型   | 必填 | 默认值 |
| ------------- | ---------- | ------ | ---- | ------ |
| current       | 当前页码   | Number | 否   | 1      |
| total         | 总数据量   | Number | 否   | 0      |
| limit         | 页容量     | Number | 否   | 10     |
| visibleNumber | 可见页码数 | Number | 否   | 10     |

### 事件

| 事件名 | 含义     | 事件参数 | 参数类型 |
| ------ | -------- | -------- | -------- |
| change | 页码变化 | 新的页码 | Number   |

```vue
<Pager v-if="blogsData" 
       :total="data.total" // 先将 data 挂载到 Pager 上
       :limit="routeInfo.routeLimit" // 将路由信息挂载到 Pager 上
       :current="routeInfo.routePage"
       @change="handleChange" /> // 处理子组件抛出事件
```

### 处理子组件抛出事件：

**注意：在 Vue 实例中，你可以通过 `$router` 访问路由实例。因此你可以调用 `this.$router.push`。**

想要导航到不同的 URL，可以使用 `router.push` 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，会回到之前的 URL。

```js
methods:{
    handleChange(newPage) {
      // 编程式导航
      // $router 控制页面跳转
      // 命名的路由，并加上参数，让路由建立 url
      // router.push({ name: 'user', params: { username: 'eduardo' },query: { plan:'private' }})
      if (this.routeInfo.blogCategory === -1) {
        this.$router.push({name: 'Blog', query: {page: newPage + ""}})
      } else {
        this.$router.push({
          name: 'CategoryBlog',
          params: {cate: this.routeInfo.blogCategory + ""},
          query: {page: newPage + ""}
        })
      }
    }
}
```

[$router](https://router.vuejs.org/zh/guide/essentials/navigation.html#%E5%AF%BC%E8%88%AA%E5%88%B0%E4%B8%8D%E5%90%8C%E7%9A%84%E4%BD%8D%E7%BD%AE)

至此，已完成`模拟数据获取`，`路由信息获取`，`点击分页组件更新导航栏`

---

## 导航栏更新后的数据更新

**侦听器——watch**

```js
// 起监听作用
watch:{
    // 写 $route ，则会监听 $route
    async $route() {
      this.isLoading = true;
      // 滚动高度设置为0
      this.$refs.container.scrollTop = 0;
      this.data = await this.fetchData();
      this.isLoading = false;
    }
    // $data(){} 监听 this.data
}
```

# 细节

## 路由跳转逻辑

![image-20210107140253824](http://mdrs.yuanjin.tech/img/20210107140253.png)

---

## 组件逻辑

**通过 `$route`，让`BlogList`和`BlogCategory`实现间接通信**

![image-20210107142310757](http://mdrs.yuanjin.tech/img/20210107142310.png)

### BlogList

![image-20210107153623557](http://mdrs.yuanjin.tech/img/20210107153623.png)

### BlogCategory

![image-20210107154531659](http://mdrs.yuanjin.tech/img/20210107154531.png)

# 知识

## 动态路由

我们希望下面的地址都能够匹配到`Blog`组件

- `/article`，显示全部文章
- `/article/cate/1`，显示分类`id`为`1`的文章
- `/article/cate/3`，显示分类`id`为`3`的文章
- ...

第一种情况很简单，只需要将一个固定的地址匹配到`Blog`组件即可

```js
{
  path: "/article",
  name: "Blog",
  component: Blog
}
```

但后面的情况则不同：匹配到`Blog`组件的地址中，有一部分是动态变化的，则需要使用一种特殊的表达方式：

```js
{
  path: "/article/cate/:categoryId",
  name: "CategoryBlog",
  component: Blog
}
```

在地址中使用`:xxx`，来表达这一部分的内容是变化的，在`vue-router`中，将变化的这一部分称之为`params`，可以在`vue`组件中通过`this.$route.params`来获取

```js
// 访问 /article/cate/3
this.$route.params // { categoryId: "3" }
// 访问 /article/cate/1
this.$route.params // { categoryId: "1" }
```

### 动态路由的导航

```vue
<router-link to="/article/cate/3">to article of category 3</router-link>

<router-link :to="{
   name: 'CategoryBlog',
   params: {
       categoryId: 3           
   }                    
}">to article of category 3</router-link>
```

---

## 编程式导航

除了使用`<RouterLink>`超链接导航外，`vue-router`还允许在代码中跳转页面

```js
this.$router.push("跳转地址"); // 普通跳转
this.$router.push({ // 命名路由跳转
  name:"Blog"
})

this.$router.go(-1); // 回退。类似于 history.go
```

---

## watch

利用`watch`配置，可以直接观察某个数据的变化，变化时可以做一些处理

```js
export default {
  // ... 其他配置
  watch: {
    // 观察 this.$route 的变化，变化后，会调用该函数
    $route(newVal, oldVal){
      // newVal：this.$route 新的值，等同 this.$route
      // oldVal：this.$route 旧的值
    },
    // 完整写法
    $route: {
      handler(newVal, oldVal){},
      deep: false, // 是否监听该数据内部属性的变化，默认 false
      immediate: false // 是否立即执行一次 handler，默认 false
    }
    // 观察 this.$route.params 的变化，变化后，会调用该函数
    ["$route.params"](newVal, oldVal){
      // newVal：this.$route.params 新的值，等同 this.$route.params
      // oldVal：this.$route.params 旧的值
    },
  	// 完整写法
    ["$route.params"]: {
      handler(newVal, oldVal){},
      deep: false, // 是否监听该数据内部属性的变化，默认 false
      immediate: false // 是否立即执行一次 handler，默认 false
    }
  }
}
```

