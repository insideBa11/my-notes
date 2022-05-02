# router

## 使用vue-router的前置工作：

### 1.安装vue-router

由于vue与vue-router版本兼容的问题，需要在安装语句后加入需要下载vue-router版本，不然会下载最新的vue-router版本。

```shell
npm install --legacy-peer-deps vue-router@3.5.2
```

### 2.导入vue-router

```javascript
import VueRouter from 'vue-router'
```

**再用Vue.use(plugin)，通过全局混入来添加组件选项**

```javascript
// Vue.use() 使用一个vue插件
Vue.use(VueRouter);
```

**使用了vue.use()注册插件之后就可以在所有的vue文件中通过`this.$route`使用路由**

### 3.配置router

第二部中导入的VueRouter是一个构造函数，可以通过这个构造函数构建一个对象，来配置router：

```javascript
// 使用VueRouter创建一个router对象，在router内配置路由规则
const router = new VueRouter({
   // 路由配置
   routes: [ // 一个数组，数组里填写路由规则，规则的形式是对象形式
	{name: 'Home', path: '/', component: Home},
	{name: 'Blog', path: '/blog', component: Blog},
	{name: 'Message', path: '/message', component: Message},
	{name: 'About', path: '/About', component: About},
	{name: 'Project', path: '/Project', component: Project},
   ],
   // 使用history模式
   mode: 'history' // 默认为hash模式
})
```

### 4.挂载router

```javascript
new Vue({
   router,
   render: h => h(App),
}).$mount('#app')
```

## 实际使用：

### 1.`<router-link></router-link>`和`<router-view></router-view>`

- `router-link`

  相当于一个`a`标签，将需要跳转到的路径写在标签的to属性之后

  ```vue
  <router-link to='path'></router-link>
  ```

- `router-view`
  router-view 将显示与当前 path 对应的组件（在router配置里所对应的组件）。你可以把它放在任何地方，以适应你的布局。

### 2.激活状态

`vue-router`提供了全局的组件`RouterLink`，它的渲染结果是一个`a`元素

默认情况下，`vue-router`会用 **当前路径** 匹配 **导航路径** ：

- 如果当前路径是以导航路径开头，则算作匹配，会为导航的a元素添加类名`router-link-active`
- 如果当前路径完全等于导航路径，则算作精确匹配，会为导航的a元素添加类名`router-link-exact-active`

```vue
//如果需要精确匹配，则可以给 router-link 加上一个 exact 属性
<router-link exact:true /> // 表示需要精确匹配。
```

然后再为类名添加样式即可：

```css
  .router-link-active {
  	// css styles
  }
```

## 路由模式

路由模式决定了：

1. 路由从哪里获取访问路径
2. 路由如何改变访问路径

`vue-router`提供了三种路由模式：

1. hash：默认值。路由从浏览器地址栏中的hash部分获取路径，改变路径也是改变的hash部分。该模式兼容性最好。

   **原理是利用锚链接。**

   ```
   http://localhost:8081/#/blog  -->  /blog
   http://localhost:8081/about#/blog  --> /blog
   ```

2. history：路由从浏览器地址栏的`location.pathname`中获取路径，改变路径使用的H5的`history api`。该模式可以让地址栏最友好，但是需要浏览器支持`history api`

   ```
   http://localhost:8081/#/blog  -->  /
   http://localhost:8081/about#/blog  --> /about
   http://localhost:8081/blog  --> /blog
   ```

3. abstract：路由从内存中获取路径，改变路径也只是改动内存中的值。这种模式通常应用到非浏览器环境中。

   ```shell
   内存： /			-->   /
   内存： /about	--> /about
   内存： /blog	  --> /blog
   ```

## vue router如何做到无刷新跳转？

1. 如果是hash模式下的路由，则是才用了锚链接实现这一功能；（因为是基于锚链接实现的，所以兼容性好）
    - 不足：路径中会带`#`
2. 如果是history模式下的路由，底层原理是采用h5的api：`history.pushState()`实现这一功能。（需要用到h5的api，兼容性不如hash，不过用户体验好）

`vue-router`提供了全局的组件`RouterLink`，它的渲染结果是一个`a`元素

```
<RouterLink to="/blog">文章</RouterLink>
<!-- mode:hash 生成 -->
<a href="#/blog">文章</a>
<!-- mode:history 生成 -->
<!-- 为了避免刷新页面，vue-router实际上为它添加了点击事件，并阻止了默认行为，在事件内部使用hitory api更改路径 -->
<a href="/blog">文章</a>
```