# shopping-cart

最近在学vue.js，看了官网的教程，然后找了案例来练手，此次案例使用vue2.0+vue-router+axios+ES6语法实现购物车和地址选配功能。这里主要讲下此次案例的流程和一些实现方法，希望对想入门的同学有所帮助。

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

此案例使用了vue-cli自带的webpack来构建项目，如果你想自己创建项目，可以下载vue-cli来构建：

      npm install -g vue-cli   //全局安装vue-cli

      vue init webpack Vue-Project      //webpack初始项目



#### 需要用到的知识
[vue2.0](https://cn.vuejs.org/v2/guide/)

[vue-router](https://router.vuejs.org/zh-cn/)

[axios](https://www.kancloud.cn/yunye/axios/234845)

[ES6](http://es6.ruanyifeng.com/)

#### 一、获取商品列表
此次用到的数据是存储在本地的json格式数据，因为vue-resource已经停止更新，所以这里用到的是axios库。在案例中将axios封装为一个AXIOS类：

```
export class AXIOS {
  constructor() {
  }
  //获取已授权列表
  get(param) {
    config.data.strSQL = param.param;
    return axios.get(param.api,{},config);
  }
  post(param) {
    config.data.strSQL = param.param;
    return axios.post(param.api,{},config);
  }
  reqSuccess(obj,msg){}
  
  reqFail(obj,msg){}
}
export default AXIOS;
```
请求配置文件为config.js文件，具体配置可以参考[axios](https://www.kancloud.cn/yunye/axios/234845)
文档里面的**请求配置**给出的详细信息

#### 二、定义所需要数据
```
export default {
   name: 'address',
   data(){               
     return {
       productList: [],       //购物车商品列表
       totalMoney: 0,         //总金额
       checkAllFlag: false,     //是否全选
       delFlag: false,        //是否删除该商品
       curProduct: ''         //要删除的删除的商品
     }
   }
}
```
#### 三、获取商品列表
配置好axios后，使用axios来获取数据，定义getCartList方法：

```
getCartList(){           //获取购物车商品列表
   let params = {           //这是axios的参数
       api: 'static/axios/cartData.json',
       param: {}
   };
   ax.get(params)
     .then(res => {
       this.productList = res.data.result.list;
     })
     .catch(err => {
       console.log(err);
     });
},
```
然后用v-for指令将productList渲染到页面：
```
<li v-for="item in productList">
```

#### 四、商品选择和商品数量的改变
使用单选按钮选择商品，定义selectProduct()方法改变按钮的属性以达到改变按钮展示效果：

```
selectedProduct(product){
  if(typeof product.checked == 'undefined'){
      this.$set(product, "checked", true);
  }else{
      product.checked = !product.checked;
  }
},
```
selectProduct()方法改变了按钮的class属性，以此达到改变按钮展示效果，
```
 <a v-bind:class="{'check': item.checked}" @click="selectedProduct(item)"></a>
```
定义changeQuantity()方法改变商品数量

```
changeQuantity(product, type){           //改变商品数量
     if(type > 0){
           product.productQuantity++;
       }
       else{
           product.productQuantity>1 ? product.productQuantity--:product.productQuantity=1;
       }
       this.calcTotalMoney();
   }
```
然后将方法绑定到html元素上：

```
<a href="javascript:;" @click="changeQuantity(item,-1)">-</a>
<input type="text" :value="item.productQuantity"  disabled>
<a href="javascript:;" @click="changeQuantity(item,1)">+</a>
```
##### 五、全选、取消全选和商品总额
定义checkAll、calcTotalMoney方法：

```
checkAll(flag){                     //此方法改变checkAllFlag的值
    this.checkAllFlag = flag;
    this.productList.forEach((product, index) => {
        if(typeof product.checked == 'undefined'){
            this.$set(product, "checked", this.checkAllFlag)
        }else{
            product.checked = this.checkAllFlag;
        }
    });
    this.calcTotalMoney();
},
calcTotalMoney(){             //计算总金额
    this.totalMoney = 0;
    this.productList.forEach((product, index) => {
        if(product.checked){
            this.totalMoney += product.productPrice * product.productQuantity
        }
    });
},
```
同样绑定到相关html元素上。

还有地址选配功能实现逻辑也是差不多，这里就不再赘述。

[原始代码](https://github.com/qq326943819/Original_Shop_Car)<br>
[教学视频](http://www.imooc.com/learn/796)

此案例的代码有详细的注释，直接看代码也可以。<br>
原始代码是没有添加vue代码的，可以自己实现vue代码以此来练习vue。<br>

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
