#Vue.js

***
**框架和库的区别：**

* 框架：是一套完整的解决方案，对代码的**侵入性较大**。项目如果需要更换框架，需要重构整个项目。
* 库（插件）：提供某一功能，对代码的**侵入性较小**，如果有其他需求，可以切换到其他库实现需求。


##MVVM:
* Model：模型
* View:视图
* ViewModel:动态绑定

##语法：
###Vue实例：
**在一个Vue实例内，如果想获取data中的变量（数据），或者调用methods中的方法，需要使用this.变量或this.方法的方式。this代表当前Vue实例的引用。**

```js
var vm = new Vue(){

	el:被控制的元素； // 该Vue实例控制的页面的区域
	data:{
		msg1:'hello';// 变量（模型），可创建多个变量
		msg2:'你好';
	},
	methods:{
		show1:function(){
			// 
		}，
		// 或者直接方法名
		show2(){
		
		}
	}
}
```

**一个实例会监听自己data中数据(model)的变动，并立即更新到视图（view）中**

**`this`**:

	this代表当前Vue实例的引用
	当methods中，定义的方法A，需要使用data中的变量时,如变量msg，需要使用this.msg变量来访问。
	当A方法中，又嵌套了一个方法B，此时B中this代表的是方法A的引用，再使用this.msg,就会出错,解决方式：
		1.在方法A中，将this赋值给一个变量，然后，就可以在方法B中使用该变量，来访问Vue实例
		2.使用==>
​					![1554473364386](assets/1554473364386.png)





![1554473165198](assets/1554473165198.png)

###常用标签：

* **{{}}:**插值表达式：使用占位符的方式，替换data

   功能：可以展示data中的数据
   	备注：在网速较慢的情况下，{{}会出现闪烁问题，解决方式：
   		1.v-cloak:设置css样式
   			不会覆盖元素默认的值
   			<p v-cloak>{{data}}</p>
   			[v-cloak]{
   				display = none;
   			}
   		2.v-text:直接绑定model
   			<p v-text='data'></p>
   			会覆盖元素默认的值

* **v-bind:**属性绑定机制

   功能：用来绑定标签的属性
   	备注：
   		1.可以简写为 --> :绑定的属性
   		v-bind会将绑定的内容当做JS代码执行（可以写合法的JS表达式）

* **v-on:**事件绑定机制

   功能：用来绑定各种事件
   	备注：
   		1.可简写为:--> @事件
   	常用事件：
   		

   	事件修饰符：
   		stop：阻止冒泡
   		prevent:阻止事件的默认行为（Vue实例中的方法不会阻止）：如<a>的跳转，<from>的提交
   		capture:实现触发捕获事件的机制（先外后内）
   			在没有使用事件修饰符的情况下，多个嵌套事件，会由内-->外触发
   			使用capture修饰符可以从外-->内触发
   		self：只有 【直接】使用了该事件，才会触发，冒泡和捕获都不会触发被其修饰的事件
   		once:事件只会执行一次
   	tips：
   		事件修饰符可以串连使用：
   			如：@click.prvent.once			

* **v-for:**遍历循环：

  数组：`items` 是源数据数组并且 `item` 是数组元素迭代的别名。  

  ​	参数：

  ​		固定:index（当前项的索引。 ）

  ​		自定义：item（代表数组中每一个元素）

  ​	`tips：`

  ​		元素是对象时，需要item.属性，来获取对象的值

  对象：通过过一个对象的属性来迭代。 

  ​	参数：

  ​		固定：index（当前项的索引。 ）

  ​		固定：key （代表对象的属性名）

  ​		自定义:value(代表key对应的属性值)

  数字

  ​	

  `tips:`

  ​	1.可以使用 of 代替in

  ​	2.在 `v-for` 块中，我们拥有对父作用域属性的完全访问权限。即在循环中，可以访问循环数据的兄弟数据

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>模板</title>
      <script src="js/vue.js"></script>
      <style>
          /* td{
              border: 1px solid black;
          } */
  
      </style>
  </head>
  <body>
      <div id="app">
              <table border="1px">
                      <thead>
                          <td>value</td>
                          <td>index</td>
                          <td>key</td>
                      </thead>
                      <tr v-for="(item,index) in list1">
                          <td>
                              {{item}}
                          </td>
                          <td>
                              {{index}}
                          </td>
                      </tr>
              </table>
              <table border="1px">
                      <thead>
                          <td>name</td>
                          <td>age</td>
                          <td>index</td>
                      </thead>
                      <tr v-for="(item,index) in list2">
                          <td>
                              {{item.name}}
                          </td>
                          <td>
                              {{item.age}}
                          </td>
                          <td>
                              {{index}}
                          </td>
                      </tr>
              </table>
              <table border="1px">
                      <thead>
                          <td>index</td>
                          <td>key</td>
                          <td>value</td>
                      </thead>
                      <tr v-for="(item,key,index) in object">
                          <td>
                              {{index}}
                          </td>
                          <td>
                              {{key}}
                          </td>
                          <td>
                              {{item}}
                          </td>
                      </tr>
              </table>
      </div>
      <script>
          var vm = new Vue({
              el:"#app",
              data:{
                  list1:[1,2,3,4,5],
                  list2:[
                      {name:"李四",age:15},
                      {name:"王五",age:13}
                  ],
                  object:{
                      name:"张三",
                      gender:"male"
                  }
              }
          });
      </script>
  </body>
  </html>
  ```

  

  ![1554473265557](assets/1554473265557.png)

数组更新检测（数组的操作）：

Vue 包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

###切换class和style

v-bind:切换class

```js
`<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>VueDemo</title>
<script src="js/vue.js"></script>
<style>
    .color{
        color: red
    }    

    .size{
        font-size: 10em
    }
</style>
</head>
<body>
<!-- 1.原生方式 -->
<!-- <div id='app'>
     <h1 class="color size">{{msg}}</h1>
</div> -->
<!-- 2.Vue-->
<div id='app'>
        <!--0.数组  -->
        <h1 :class="['color','size']">{{msg}}</h1>
        <!--1.数组内不加引号，会当做变量处理,
            提示Property or method "color" is not defined on the instance  -->
        <h1 :class="[color,size]">{{msg}}</h1> 
        
        <!-- 2.三元表达式 -->
        <h1 :class="['color',flag?'size':'']">{{msg}}</h1>
        
        <!-- 3.数组内嵌套对象 -->
        <h1 :class="['color',{'size':flag}]">{{msg}}</h1>
        
        <!-- 4.直接使用对象-->
        <h1 :class="{'color':false,size:flag}">{{msg}}</h1>
        
        <!-- 5.也可以将数组或者对象定义成，变量，然后再引用 -->
        <h1 :class="css1">{{msg}}</h1>
        <!-- 6.如果，有多个，可以使用数组 -->
        <h1 :class="[css1]">{{msg}}</h1>
</div>

<script>
    var vm = new Vue({
        el:'#app',
        data:{
            msg: '一级标题',
            flag:true,
            css1:{color:true,size:true}
        }
    })
</script>
</body>
</html>`
```

***

列表





###生命周期钩子

>每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，这给了用户在不同阶段添加自己的代码的机会。 



* create:一个Vue实例被创建之后执行代码
* mounted:
* updated
* destroyed

`tips:`

​	生命周期钩子的     `this` 上下文指向调用它的 Vue 实例。 



​	





































