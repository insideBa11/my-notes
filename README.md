# 组件注入

**导出需要抽离的数据**

```javascript
export default {
	data() {
		return {
			isLoading: true,
			data: null
		}
	},
	async created() {
		// 具体的组件中，需要提供一个获取远程数据的方法 fetchData
		this.data = await this.fetchData();
		this.isLoading = false;
	}
}
```

为了提高代码的复用性，可以在抽离的数据中将`data`赋值为：

```javascript
this.data = await this.fetchData();
```

这样做的好处是，当有组件需要用到注入的组件时，只需要在`methods`中声明一个一个`fetchData`函数即可：

```vue
async fetchData() {
return await getBanners()
}
```

当远程数据获取之后，就会复制到`data`上。 同理，如果是在Blog组件中，也能用同样的方式书写：

```vue
async fetchData(){
return await getBlogs()
}
```

# 为什么要组件混入

有的时候，许多组件有着类似的功能，这些功能代码分散在组件不同的配置中。

![image-20210105161811637](http://mdrs.yuanjin.tech/img/20210105161811.png)

于是，我们可以把这些配置代码抽离出来，利用**混入**融合到组件中。

![image-20210105162109015](http://mdrs.yuanjin.tech/img/20210105162109.png)

# 具体做法

```js
// 抽离的公共代码
const common = {
  data(){
    return {
      a: 1,
      b: 2
    }
  },
  created(){
    console.log("common created");
  },
  computed:{
    sum(){
      return this.a + this.b;
    }
  }
}

/**
 * 使用comp1，将会得到：
 * common created
 * comp1 created 1 2 3
 */
const comp1 = {
  mixins: [common] // 之所以是数组，是因为可以混入多个配置代码
  created(){
    console.log("comp1 created", this.a, this.b, this.sum);
  }
}
```

混入并不复杂，更多细节参见[官网](https://cn.vuejs.org/v2/guide/mixins.html)