# vue的使用

## 一、VUE常用指令用法

### 循环语句：v-for的用法(列表)

v-for=变量名+in+列表名  {{变量名}}

```html
<h1>VUE列表展示案例</h1>
    <div id = "app2">
        <ul>
            <li v-for="(item,index) in movies">{{index}}-{{item}}</li>
        </ul>
    </div>
<script src="js/vue.js"></script>
 <script >
 var app2 = new Vue({
        el: '#app2',
        data: {
            movies: ['星际穿越','大话西游','盗梦空间','环太平洋']
        }
    });
</script>
```

效果图：

![1601194912573](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601194912573.png)

### 监听属性：v-on的用法(计数器)

语法糖写法（简写）：@

v-on:click="add" =  @click = “add”

```html
<h1>VUE计数器案例</h1>  <!--view-->
    <div id = "app1">
        <h2>当前计数：{{count}}</h2>
        <button v-on:click="add">+</button>
        <button @click="sub">-</button>
    </div>
<script>
//计数器
    const obj = {
        count: 0
    } //model
    const app1 = new Vue({  //modelview
        el: '#app1',
        data: obj,
        methods: {
            add: function(){
                console.log("count++执行")
                this.count++
            },
            sub: function (){
                console.log("count--执行")
                this.count--
            }
        }
    });
</script>
```

效果图：

![1601194978268](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601194978268.png)

### 条件判断：v-if,v-else-if,v-else的用法

```html
<div id="app">
        <a>当前分数：{{score}}</a>
        <h1 v-if="score>80">优秀</h1>
        <h1 v-else-if="score>600">良好</h1>
        <h1 v-else>差</h1>
    </div>

 <script src="../js/vue.js"></script>
 <script >
    var app = new Vue({
        el: '#app',
        data: {
            message: "helloworld",
            score: 100
        }
    })

</script>
```

效果图：

![1601274487975](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601274487975.png)

### 显示：v-show的用法以及和v-if的区别

v-if：当条件为false时，包含v-if指令的元素不会存在dom中

v-show：当条件为false时，会给元素添加一个行内样式：display：none

开发中选择：当需要显示和隐藏之间很频繁时，使用v-show，当只有一次切换时，使用v-if

```html
 <div id="app">
        <a>当前分数：{{score}}</a>
        <h1 v-if="isshow" id="aaa">优秀</h1>
        <h1 v-show="isshow" id="bbb">优秀</h1>
    </div>
```

效果图：

![1601275928065](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601275928065.png)

### v-model的用法

实现双向绑定，当修改input值，后台的值也跟着改变，反之也如此

```html
<div id="app">
    <input type="text" v-model="first">{{first}} <!--动态绑定-->
</div>
```

效果图：

![1601282119845](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601282119845.png)

v-model其实是一个语法糖，背后本质是包括以下两个操作：

- v-bing绑定一个value属性

- v-on给当前元素绑定input事件

  ```html
  <div id="app">
      <input type="text" v-model="first">{{first}} <br><!--动态双向绑定-->
      <input type="text" v-bind:value="first" v-on:input="onchange">{{first}}
      <h1>{{first}}</h1>
  </div>
  
  <script src="../js/vue.js"></script>
  <script >
      var app = new Vue({
          el: '#app',
          data: {
              first: 'winjay'
          },
          methods:{
              onchange(event){
                  this.first=event.target.value;
              }
          }
      })
  </script>
  
  ```

#### v-model结合radio类型

```html
<div id="app">
    <label for="a">	<!--label的好处是把点击文字也可选中，要定义id-->
        <input type="radio" v-model="sex" value="男" id="a">男
    </label>
    <label for="b"> <!--使用v-model定义后可以省略name的定义-->
        <input type="radio" v-model="sex" value="女" id="b">女
    </label>
    <h1>你选择的性别为：{{sex}}</h1>
</div>

<script src="../js/vue.js"></script>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            sex: '男' //默认选中为男
        }
    })
</script>
```

效果图：

![1601284280699](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601284280699.png)

#### v-model结合checkbox类型

```html
<div id="app">

    <input type="checkbox" v-model="sport" value="篮球">篮球
    <input type="checkbox"  v-model="sport" value="游泳" >游泳
    <input type="checkbox"  v-model="sport" value="乒乓球" >乒乓球
    <input type="checkbox"  v-model="sport" value="羽毛球" >羽毛球

    <h1>你选择的运动有：{{sport}}</h1>
</div>

<script src="../js/vue.js"></script>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            sport: []
        }
    })
</script>

```

效果图：

![1601284450704](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601284450704.png)

#### v-model结合select类型

```html

    <select v-model="fruit"> <!--加入multiple可多选-->
        <option value="苹果" >苹果</option>
        <option value="西瓜" >西瓜</option>
        <option value="雪梨" >雪梨</option>
        <option value="香蕉" >香蕉</option>
    </select>
    <h1>你选择的水果是：{{fruit}}</h1>
</div>

<script src="../js/vue.js"></script>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            fruit: '香蕉' //默认选中香蕉
        }
    })
</script>

```

效果图：

![1601284810065](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601284810065.png)

#### v-model,v-for,v-bind联合使用

```html
 <label v-for="item in hobbies" :for="item">
        <input type="checkbox" :id="item" v-model="onhobby" :value="item" >{{item}}
    </label>
    <h1>你选择的爱好有：{{onhobby}}</h1>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            hobbies: ['篮球','游泳','看书'],
            onhobby: []
        }
    })
</script>
```

效果图：

![1601286095548](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601286095548.png)

#### v-model修饰符

- lazy修饰符：v-model.lazy可以让数据失去焦点或者回车时才会提交更新数据
- number修饰符：v-model.number可以过滤掉除了数字以外的字符，只留下number
- trim修饰符：v-model.trim可以自动去除字符两边的空格

```html
    <!--lazy修饰符-->
    <input type="text"  v-model.lazy="message">
    <h2>{{message}}</h2>
    <!--number修饰符-->
    <input type="number"  v-model.number="age">
    <h2>{{age}}</h2>
    <!--trim修饰符-->
    <input type="text"  v-model.trim="message">
    <h2>{{message}}</h2>
```



### 插值指令的使用

#### v-once的使用

- 该指令表示元素和组件只渲染一次，不会随着书记的改变而改变

- 该指令后面不需要跟任何表达式

  效果图如下：

  ![1601188562748](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601188562748.png)

![1601188857808](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601188857808.png)

#### v-html的使用

v-html可以把数据以html风格展示

```html
<div id = "app3">
    <a v-for="url"></a>
</div>
<script>
    const app3 = new Vue({  //modelview
        el: '#app3',
        data: {
            url: '<a href="www.baidu.com">进入winjay的博客</a>'
        }
    });
</script>
```

效果图：

![1601189591231](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601189591231.png)

#### v-text的使用

使用v-text会覆盖掉后面的陈述，一般不使用

```html
    <div id="app">
        <a>{{message}},winjay</a><br>
        <a v-text="message">,winjay</a>
    </div>
```

效果图：

![1601189860615](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601189860615.png)

#### v-pre的使用

定义v-pre后将不会解析语句，原封不动的显示原有的数据

```html
    <div id="app">
        <a>{{message}},winjay</a><br>
        <a v-pre>{{message}}</a>
    </div>
```

效果图：

![1601190028486](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601190028486.png)

#### v-cloak的使用

vue解析之前，div会有一个v-cloak属性，可用style定义；

vue解析之后，div中的v-cloak会被删除（很少使用，效果图不放了）

### 绑定属性v-bind的用法

语法糖写法（简写）： **:**

**v-bind ：src = ： src**

```html
 <!--v-bind-->
    <div id = "app4">
        <img : src="imgurl"> <!--使用语法糖写法-->
        <a v-bind:href='ahref'>进入我的博客</a>
    </div>
<script>
    var app4 =new Vue({
        el: '#app4',
        data: {
            message: '这是一个菜鸟教程的微信图'
            imgurl: 'https://www.runoob.com/wp-content/themes/runoob/assets/images/qrcode.png'
            ahref: 'http://www.winjay.top'
        }
    })
</script>
```

效果图：

![1601191334754](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601191334754.png)

#### v-bind动态绑定class（对象语法）

语法：v-bind:class="{key（类名）: value（boolean类型）}" 

ps：key引号可加可不加，value加引号访问的是对象本身，不加引号则访问对象的值

```html
方法一： 
<style>
        .active{
            color: red;
        }
    </style>
<div id="app">
    <h2 class="active">{{message}}</h2>  <!--普通绑定-->
    <h2 :class="active">{{message}}</h2> <!--动态绑定-->
    <h2 :class="{active: isactive,line: isline}">{{message}}</h2>
    <!--<h2 :class="getclass()">{{message}}</h2>-->
    <button @click="btn">点我</button>
</div>

<script src="../js/vue.js"></script>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            message: "helloworld",
            active: 'active',
            isactive: 'true',
            isline: 'true'
        },
        methods: {
            btn: function(){
                this.isactive = ! this.isactive
            }
           //getclass: function(){
               // return {red: this.isred,line: this.isline}
            //} 
        }
    });
</script>
```

效果图：

![1601192897664](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601192897664.png)

#### v-bind动态绑定class（数组语法）(了解)

class加引号后访问的是对象本身，没有引号访问的是对象的值，即变量

```html
   <style>
        .red{
            color: red;
        }
    </style>
</head>
<body>
<div id="app">
    <h2 :class="[red,line]">{{message}}</h2>
<!--    <h2 :class="getclass()">{{message}}</h2>-->
</div>

<script src="../js/vue.js"></script>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            message: "helloworld",
            red: 'red',
            line: 'bbbbb'
        },
        methods: {
            btn: function(){
                this.isred = ! this.isred
            },
         /*   getclass: function(){
                return [this.red,this.line]
            }*/
        }
    });
</script>
```

效果图：

![1601194475808](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601194475808.png)

![1601194451587](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601194451587.png)

![1601194519108](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601194519108.png)

![1601194591959](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601194591959.png)

#### v-bind动态绑定style（对象语法）

语法：v-bind:style="{key（style名）: value（boolean类型）}" 

ps：key引号可加可不加，value加引号访问的是对象本身，不加引号则访问对象的值

```html
<div id="app">
    <h2 >{{message}}</h2> <!--动态绑定-->
    <h2 v-bind:style="{fontSize: finalsize}" >{{message}}</h2> <!--动态绑定-->
</div>

<script src="../js/vue.js"></script>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            message: "helloworld",
            active: 'active',
            finalsize: '200px'
        }
    });
</script>
```

效果图：

![1601197628168](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601197628168.png)

#### v-bind动态绑定style（数组语法）（了解）

class加引号后访问的是对象本身，没有引号访问的是对象的值，即变量

```html
<div id="app">
    <h2 >{{message}}</h2> <!--动态绑定-->
    <h2 v-bind:style="[finalcolor,finalsize]">{{message}}</h2> <!--动态绑定-->
</div>

<script src="../js/vue.js"></script>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            message: "helloworld",
            active: 'active',
            finalcolor: {backgroundColor: 'green'},
            finalsize: {fontSize: '50px'}
        }
    });
</script>
```

效果图：

![1601198023649](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601198023649.png)

### 计算属性：computed的用法

 计算属性在处理一些复杂逻辑时是很有用的。

```html
<div id="app">
    <h2 >总价：{{totalprice}}</h2> <!--动态绑定-->
</div>

<script src="../js/vue.js"></script>
<script >
    var app = new Vue({
        el: '#app',
        data: {
            book: [
                {id: 1 ,name:'格林童话',price: 100 },
                {id: 2 ,name:'皇帝的新装',price: 120 },
                {id: 3 ,name:'海的女人',price: 82 },
                {id: 4 ,name:'杀死比尔',price: 124 },
                ]
        },
        computed: {
            totalprice() {
                let result = 0
                for(let i=0;i<this.book.length;i++){
                    result += this.book[i].price;
                }
                return result
            }
        }
    });
</script>
```

 效果图：

![1601199754251](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601199754251.png)

#### 计算属性setter与getter（了解）

 computed 属性默认只有 getter ，不过在需要时你也可以提供一个 setter  

```html
   computed: { 
            //计算属性一般没有set方法，只读属性
            fullname: {
                set: function (newValue){
                    const names =newValue.split(' ');
                    this.first = names[0];
                    this.last = names[1];
                },
                get: function (){
                    return this.first+' '+this.last
                }
            }  }
```

#### computer与methods的区别

 我们可以使用 methods 来替代 computed，效果上两个都是一样的，但是<a style="color:red">computed是基于它的依赖缓存，只有相关依赖发生改变时才会重新取值</a>。<a style="color:red">而使用 methods ，在重新渲染的时候，函数总会重新调用执行。</a> 可以说使用 computed 性能会更好，但是如果你不希望缓存，你可以使用 methods 属性。

## 二、var,let和const的区别

- **var是全局变量，let、const都是块级局部变量**,顾名思义，就是只在当前代码块起作用 , const 的特性和 let 完全一样，不同的只是
  const声明时候必须赋值
- ![1601273213026](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601273213026.png)
- const只能进行一次赋值，即声明后不能再修改 ，否则会编译报错



![1601273222593](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601273222593.png)

-  同一作用域下let和const不能声明同名变量，而var可以 

## 三、vue组件

### 组件使用的基本语法

自定义创建组件构造器对象——注册组件——使用组件

全局组件在对象实例外部，可全局使用，局部组件在实例内部注册

```html
    <!--3.使用组件-->
<div id="app">
    <cpnc></cpnc>
</div>
<script src="../js/vue.js"></script>
<script >
    //1.创建组件构造器对象
    const cpncc = Vue.extend({
        template:'<div>' +
            '<h2>我是内容哦</h2>' +
            '</div>'
    })
    //2.注册组件（全局组件）
    Vue.component('cpnc',cpncc)
   
    const app = new Vue({
        el : '#app',
        data: {message: 'hello' },
        componnents:{  //局部组件
  <!--组件标签名--> cpn: cpncc <!--父组件名-->
        }
    })
</script>
```

效果图：![1601288065261](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601288065261.png)

### 父组件与子组件的区分

父组件中可放入子组件，在父组件中注册子组件，在实例中注册父组件即可解析父组件及子组件下的内容，但是子组件由于实例没有注册所以不能被单独解析使用

```html
<div id="app">
    <cpnc2></cpnc2>
</div>

<script src="../js/vue.js"></script>
<script >
    //子组件
    const cpncc1 = Vue.extend({
        template:'<div>' +
            '<h2>我是内容1哦</h2>' +
            '</div>'
    })
    //父组件
    const cpncc2 = Vue.extend({
        template:'<div>' +
            '<h2>我是内容2哦</h2>' +
            '<cpnc1></cpnc1></div>',
        components: {
            cpnc1: cpncc1  //注册子组件
        }
    })
    //3.使用组件
    var app = new Vue({
        el: '#app',
        data: {
            message: 'hello'
        },
        components:{
           cpnc2:cpncc2
        }
    });
</script>

```

效果图：![1601298742318](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601298742318.png)

### 组件注册语法糖实现

```html
<div id="app">
    <cpnc1></cpnc1>
    <cpnc2></cpnc2>
</div>

<script src="../js/vue.js"></script>
<script >
    //全局注册语法糖
    Vue.component('cpnc1',{
        template:'<div>' +
            '<h2>我是全局注册语法糖</h2>' +
            '</div>'
    })
    var app = new Vue({
        el: '#app',
        data: {
            message: 'hello'
        },
        components:{
           cpnc2: {
               template:'<div>' +
                   '<h2>我是局部注册语法糖</h2>' +
                   '</div>',

           }
        }
    });
</script>
```

效果图：![1601299441511](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601299441511.png)

### 组件模板抽离

```html
<div id="app">
    <cpnc1></cpnc1>
</div>
<template id="cpn">
    <div>
        <h1>我是被抽离出来的template</h1>
    </div>
</template>
<script src="../js/vue.js"></script>
<script >
    //全局注册语法糖
    Vue.component('cpnc1',{
        template: '#cpn'
    })
    const app = new Vue({
        el:'#app',
        data:{
            message:'hello'
        }
    })
</script>
```

### 父子组件通信——父传子props

```html
<div id="app">
    <cpn1 v-bind:cmovies="movies" :cmessage="message"></cpn1>
</div>
<!--子组件模板-->
<template id="cpn">
    <div>
        <ul>
            <li v-for="item in cmovies">{{item}}</li>
        </ul>
        <h2>{{cmessage}}</h2>
    </div>
</template>
<script src="../js/vue.js"></script>
<script >
    //父传子props
    //子组件
    Vue.component('cpn1',{
        props:['cmovies','cmessage'],
        template:'#cpn'
    })
    //父组件实例
    const app = new Vue({
        el:'#app',
        data:{
            message:'hello',
            movies:['海贼王','游戏王','狮子王']
        }
    })
</script>
```

效果图：![1601302041670](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601302041670.png)

### 父子组件通信——子传父（自定义事件）

什么时候用到自定义事件

- 当子组件需要向父组件传递数据时，就要用到自定义事件

自定义事件的流程

- 使用 `$on(eventName)` 监听事件
- 使用 `$emit(eventName)` 触发事件

#### 父子组件通信---结合双向绑定

```html
<div id="app">
    <cpn :number1="num1"
         :number2="num2"
         @num1change="num1change"
         @num2change="num2change"
    />
</div>
<!--子组件模板-->
<template id="cpn">
    <div>
        <!--父子组件相互通信后数值同时改变-->
        <h2>prpos:{{number1}}</h2>
        <h2>data:{{dnumber1}}</h2>
        <input type="text" v-model="dnumber1" @input="num1input">

        <h2>prpos:{{number2}}</h2>
        <h2>data:{{dnumber2}}</h2>
        <input type="text" v-model="dnumber2" @input="num2input">
    </div>
</template>
<script src="../js/vue.js"></script>
<script >
    const app = new Vue({
        el:'#app',
        data:{
            num1: 1,
            num2: 2
        },
        methods: {
            //2.父组件接收子组件的事件并重新赋值
            num1change(value){
                this.num1= parseInt(value)
            },
            num2change(value){
                this.num2= parseInt(value)
            }
        },
        components:{
            cpn:{
                template:'#cpn',
                props:{
                    number1: Number,
                    number2: Number
                },
             data() {
                    return{
                        dnumber1: this.number1,
                        dnumber2: this.number2
                    }
             },
                methods:{
                    //1.把dnumber1的数据通过num1change发送一个事件
                    num1input(){
                        this.$emit('num1change',this.dnumber1)
                    },
                    num2input(){
                        this.$emit('num2change',this.dnumber2)
                    }
                }
            }
        }
    })
</script>
```

效果图：![image-20200930143807392](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200930143807392.png)

#### 父子组件的访问

##### 父访问子：$children，$refs

```html
<div id="app">
    <cpn></cpn>
    <cpn ref="aa"></cpn>
    <button @click="btn">点击我</button>
</div>
<!--子组件模板-->
<template id="cpn">
    <div>
        我是子组件
    </div>
</template>
<script src="../js/vue.js"></script>
<script >
    const app = new Vue({
        el:'#app',
        data:{
        },
        methods: {
            btn(){
                console.log(this.$children)
                //$refs是一个对象类型，默认为空对象 ref= ‘aa'
                console.log(this.$refs)
            }
        },
        components:{
            cpn: {
                template: '#cpn'
            }
        }
    })
</script>
```

效果图：![image-20200930151603654](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200930151603654.png)

##### 子访问父：$parent （用的少）

```html
<script >
    const app = new Vue({
        el:'#app',
        data:{
            message: 'hello'
        },
        components:{
            cpn: {
                template: '#cpn',
                methods: {
                    btn(){
                        //子访问父组件 $parent
                        console.log(this.$parent)
                        //子访问根组件 $root
                        console.log(this.$root.message)
                    }
                }
            }
        }
    })
</script>
```

效果图：![image-20200930152552999](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200930152552999.png)

## 四、插槽：slot的使用

特点：

- 组件的插槽是为了让我们封装的组件更具扩展性
- 让使用者决定组件内部的一些内容到底展示什么

### 插槽基本使用

如果内部有标签则覆盖slot，否则默认使用slot的标签

```html
<div id="app">
    <!--如果内部有标签则覆盖slot，否则默认使用slot的标签-->
    <cpn><button>我覆盖了</button></cpn>
    <cpn></cpn>
    <cpn></cpn>
    <cpn><a>我覆盖了</a></cpn>
</div>
<!--子组件模板-->
<template id="cpn">
    <div>
        我是子组件
        <slot><button>我是默认的slot</button></slot>
    </div>
</template>
```

效果图：![image-20200930154306518](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200930154306518.png)

### 具名插槽的使用

一个不带 `name` 的 `<slot>` 出口会带有隐含的名字“default”。

在向具名插槽提供内容的时候，我们可以在一个 `<template>` 元素上使用 `v-slot` 指令，并以 `v-slot` 的参数的形式提供其名称：

```html
<div id="app">
    <!--如果内部有标签则覆盖slot，否则默认使用slot的标签-->
    <cpn><span>内容</span></cpn>
    <cpn><span slot="center">内容</span></cpn>
</div>
<template id="cpn">
    <div>
        <slot name="left">左边</slot>
        <slot name="center">中间</slot>
        <slot name="right">右边</slot>
    </div>
</template>
```

效果图：![image-20200930155322215](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200930155322215.png)

### 作用域插槽的使用

父组件替换插槽的标签，但是内容由子组件来提供

```html
<!--父组件模板-->
<div id="app">
    <!--如果内部有标签则覆盖slot，否则默认使用slot的标签-->
    <cpn></cpn>
    <cpn>
       <template v-slot:default="aa">
           <span v-for="item in aa.data">{{item}} - </span>
       </template>
    </cpn>
</div>
<!--子组件模板-->
<template id="cpn">
    <div>
        <slot v-bind:data="planguage">
            <ul>
                <li v-for="item in planguage">{{item}}</li>
            </ul>
        </slot>

    </div>
</template>
```

效果图：![image-20200930162458264](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200930162458264.png)

## 五、ES模块化的导入和导出

导出：export；导入：import

```html
<script>//aa.js文件
    //导出方法一：
    var name = 'winjay'
    var flag = true
    export{name,flag}
    //导出方法二：
    export var name = 'winjay'
    export var flag = true
    export function(){}
</script>
<script>//bb.js文件
    import {name,flag} from "aa.js";
    if(flag){
        console.log('winjay is good');
    }
</script>
<script>
    export default name = 'winjay' //一个js只能定义一个default
	import a from "aa.js"  //default标识的可以自定义名称，不需要加大括号
</script>
```

## vue小案例

### vue小案例（随机对象）

使用v-for,v-bind,v-on实现列表中点击单个对象，对象颜色会变红

```html
 <div id = "app2">
        <ul>
            <li v-for="(item,index) in movies"
                :class="{red: currentindex === index}"
                @click="btn(index)">
                {{index}}-{{item}}</li>
        </ul>

    </div>
<script>
var app2 = new Vue({
        el: '#app2',
        data: {
            movies: ['星际穿越','大话西游','盗梦空间','环太平洋'],
            currentindex: 0
        },
        methods: {
            btn: function (index){
                this.currentindex = index
            }
        }

    });
</script>
```

效果图：

![1601196433060](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601196433060.png)

### vue小案例（用户登陆切换）

使用v-if实现用户登陆类型切换：如账号登陆切换到邮箱登陆

```html
    <div id="app">
        <span v-if="isshow">账号登陆:<input type="text" placeholder="username" 			           name="username"></span>  <!--使用key或者name修饰可以取消value的复用-->
        <span v-else>邮箱登陆:<input type="text" placeholder="email" name="email"></span>
        <span >密码:<input type="password" placeholder="password"></span>
        <button @click="isshow = ! isshow">切换类型</button>
    </div>

 <script src="../js/vue.js"></script>
 <script >
    var app = new Vue({
        el: '#app',
        data: {
            isshow: true
        }
    })
</script>
```

效果图![1601275069614](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601275069614.png)

![1601275082079](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601275082079.png)

### vue小案例（图书购物车界面）

图书购物车界面及功能实现，使用了v-for循环列表，v-bind动态绑定disable事件，v-on监听按钮增减，v-if判断书籍长度是否为0，若真则显示购物车为空，使用computed计算属性计算图书总价，methods方法实现数量增减，filters过滤器展示小数点后两位及货币符号

```html
<div id="app">
    <table>
        <thead>
        <tr>
            <th></th>
            <th>书籍名称</th>
            <th>出版日期</th>
            <th>价格</th>
            <th>购买数量</th>
            <th>操作</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(item,index) in book">
            <td>{{item.id}}</td>
            <td>{{item.name}}</td>
            <td>{{item.date}}</td>
            <td>{{item.price | showprice}}</td>
            <td><button @click="add(index)">+</button> {{item.count}} <button  v-bind:disabled="item.count <= 1" @click="sub(index)">-</button></td>
            <td><button @click="remove">删除</button></td>
        </tr>
        </tbody>
    </table>
    <div v-if="book.length">
        <h1>总价：{{totalprice | showprice}}</h1>
    </div>
    <div v-else>
        <h1>购物车为空</h1>
    </div>
</div>

```

```js
    var app = new Vue({
    el: '#app',
    data: {
    first: 'winjay',
    last: 'peter',
    book: [
{id: 1 ,name:'格林童话',date:'2006-10',price: 100 ,count:'1'},
{id: 2 ,name:'皇帝的新装',date:'2016-10',price: 120,count:'1' },
{id: 3 ,name:'海的女人',date:'2008-11',price: 82 ,count:'1'},
{id: 4 ,name:'杀死比尔',date:'2017-02',price: 124 ,count:'5'},
    ]
},
    computed: {
    totalprice() {
    let result = 0
    for(let i=0;i<this.book.length;i++){
    result += this.book[i].price*this.book[i].count;
}
    return result
}
},
        methods:{
            add(index){
                this.book[index].count++
            },
            sub(index){
                this.book[index].count--
            },
            remove(index){
                this.book.splice(index,1)
            }
        },
           filters:{
            showprice(price){
                return '￥'+ price.toFixed(2)
            }
        }
});

```

```css
table{
    border:1px solid #e9e9e9;
    border-collapse: collapse;
    border-spacing: 0;
}
th,td{
    padding: 8px 16px;
    border:1px solid #e9e9e9;
    text-align: left;
}
th{
    background: #f7f7f7;
    color: #5c6b77;
    font-weight: 600;
}
```

![1601280227902](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601280227902.png)



# Webpack安装和使用(省略)

 ## 什么是webpack

Webpack 是一个前端资源加载/打包工具。它将根据模块的依赖关系进行静态分析，然后将这些模块按照指定的规则生成对应的静态资源。 

![1601442334736](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601442334736.png)

 从图中我们可以看出，Webpack 可以将多种静态资源 js、css、less 转换成一个静态文件，减少了页面的请求。 

从本质上讲，webpack是一个现代的JavaScript应用的静态**模块打包**工具

## webpack的安装

```shell
cnpm install webpack -g #-g表示全局使用
cnpm install webpack-cli -g #-g表示全局使用
```

# vue CLI脚手架的使用和安装

- 如果需要开发大型项目，必然需要使用Vue CLI
  - 开发大型项目时，需要考虑代码目录结构，项目结构及部署，热加载，代码单元测试等
  - 如果每个项目都手动完成，效率必然会低，所以要使用一些工具来帮助完成

- 使用vue-cli可以快速搭建Vue开发环境以及对应的webpack配置
- vue-cli是官方发布的vue.js项目脚手架

## vue-cli的安装

**安装node.js**

  下载地址：https://nodejs.org/zh-cn/

   最好下载稳定版本：下载完之后（安装程序可以直接next step）

![img](https://img-blog.csdnimg.cn/20190405150922241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NDSUVKb2huX3pob3U=,size_16,color_FFFFFF,t_70)

**安装node.js淘宝镜像加速器**

```shell
npm install cnpm -g #-g表示全局使用
```

**安装vue-cli**

```shell
cnpm install vue-cli -g #-g表示全局使用
```

**测试vue是否安装成功**

```shell
vue list
```

![image-20201002144827427](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201002144827427.png)