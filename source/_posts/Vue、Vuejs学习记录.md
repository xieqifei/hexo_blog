---
title: Vue、Vuejs学习记录
top: false
cover: false
toc: true
mathjax: false
date: 2020-02-15 17:31:51
password:
summary: Vue.js（/vjuː/，或简称为Vue）是一个用于创建用户界面的开源JavaScript框架，也是一个创建单页应用的Web应用框架。 2016年一项针对JavaScript的调查表明，Vue有着89%的开发者满意度。在GitHub上，该项目平均每天能收获95颗星，为Github有史以来星标数第3多的项目。
tags: Vuejs
categories: 学习笔记
---

# 0：ES6补充

## 0.1 块级作用域

```html
<script>
	//变量作用域：变量在什么范围内可用
    {
        var name = 'why';
        console.log(name);
    }
    console.log(name);//在大括号外也能打开。
    
    //没有块级作用域会导致什么问题
    var btns = document.getElementsByTagName('button');
    for(var i = 0; i<btns.length;i++){
        btns[i].addEventListener('click',function(){
            console.log('第'+i+'个按钮被点击')
        })
    }
    //显示的按钮被点击不准确。使用闭包解决此问题    
    for(var i = 0; i<btns.length;i++){
        (function(num){
            btns[num].addEventListener('click',function(){
            console.log('第'+num+'个按钮被点击')
        	})
        })(i)
        
    }
</script>
```



# 1：Vue初体验

## 1.1 认识Vuejs

作为时下流行的前端框架，Vuejs是很多中小型企业开发项目的首选框架。

- Vue是一个渐进式的框架。
  - 渐进式意味着可以将Vue作为应用的一部分嵌入进去，带来更丰富的交互体验。

- Vue特点和Web开发中的高级功能
  - 解耦视图和数据
  - 可复用的组件
  - 前端路由
  - 状态管理
  - 虚拟DOM

- 学习前提
  - 不需要Angular、React、jQuery
  - 需要HTML、CSS、JavaScript基础

## 1.2 Vue.js安装

方式一：直接CDN引入

方式二：下载和引入

- 安装教程 https://cn.vuejs.org

- 开发环境 https://vuejs.org/js/vue.js
- 生产环境 https://vuejs.org/js/vue.min.js

方式三：NPM安装

- 通过webpack和CLI

## 1.3 Hello Vuejs

Vue的响应式

```html
<body>

    <div id="app">
        <h1>
            {{name}}
        </h1>
        {{message}}
    </div>
    <script src="../js/vue.js"></script>
    <script>
    	//let(变量)/const(常量)
        //编程范式：声明式编程
        const app = new Vue({
            el:'#app',//用于挂在要管理的元素
            data:{//定义数据
                message:'你好啊！',//在div中显示message内容
                name:'coderwhy'
            }
        })        

        //原始js的做法（编程范式：命令式编程）
        //1.创建div元素，设置id属性
        //2.定义一个变量叫message
        //3.将message变量放在div元素中显示
        //4.修改messge的数据
        //5.将修改后的数据替换到div元素
    </script>   
</body>
```

## 1.4 Vue列表显示

```html
<body>
    <div id="app">
        <ul>
            <li v-for="item in movies">{{item}}</li>
        </ul>
    </div>
    <script src="../js/vue.js"></script>
    <script>
    	const app = new Vue({
            el:'#app',
            data:{
                message:'你好啊'，
                movies:['海王','大话西游','星际穿越','少年派']
            }
        })
    </script>
</body>
```

`app.movies.push('盗梦空间')//添加数据`

- 使用v-for指令遍历数组元素

**案例展示：计数器**

<script src="/js/vue.min.js"></script>
<div id="app">
    当前计数：0</br>
        <button v-on:click="counter++">+</button>
        <button v-on:click="counter--">-</button>
</div>
<script>
        //语法糖：简写
    	const app = new Vue({
            el:'#app',
            data:{
                counter:0
            }
        })
</script>

```html
<body>
    <script src="../js/vue.js"></script>
    <div id="app">
        <h2>
            当前计数：{{counter}}
        </h2>
        <!--<button v-on:click="counter++">+</button>-->
        <!--<button v-on:click="counter--">-</button>-->
        <button v-on:click="add">+</button>
        <button @click="min">-</button>
    </div>
    <script>
        //语法糖：简写
    	const app = new Vue({
            el:'#app',
            data:{
                counter:0
            },
            methods:{
                add:function(){
                    console.log('add被执行')；
                    this.counter++;
                },
                min:function(){
            		console.log('min被执行')；
					this.counter--;
        		}
            }
        })
        
        //原始js
        //1.拿buttom元素
        //2.添加监听时间
    </script>
</body>
```

- 新属性：methods，该属性用于Vue对象中定义方法。

- 新指令：@click，该指令用于监听某个元素的点击事件。

## 1.5 Vue中的MVVM

什么是MVVM？

- Model View ViewModel

## 1.6 创建Vue实例传入的options

目前掌握的选项

- el：
  - 字符型String|HTMLElement
  - 决定Vue管理哪一个DOM

- data：
  - Object|Funtion()**（组件当中必须是一个函数）**
  - Vue实例对应的数据对象。

- methods：
  - {[key:string] Function}
  - 定义属于Vue的一些方法，可以在其他地方调用，也可以在指令中使用

## 1.7 Vue的生命周期

生命周期：事物从诞生到消亡的整个过程

```html
<script>
	new Vue({
        el:'#app',
        data:obj,
        methods:{},
        created:function(){},
        mounted:function(){}
    })
</script>
```

## 1.8 Vue的生命周期函数

```html
<script>
	new Vue({
        el:'#app',
        data:obj,
        methods:{},
        beforeCreate:function(){},//Hook:钩子函数
        created:function(){},
        mounted:function(){}
    })
</script>
```

**生命周期函数：**`beforeCreate,create,beforeMounte,mounted,beforeUpdate,updated,beforDestroy,destroyed`

# 2：插值的操作

## 2.1 Mustache语法，插值的操作

Mustache语法：双大括号//mustache胡须

```html
<div>
    <h2>
        {{message}}
    </h2>
    <h2>
        {{firstName+' '+lastName}}
    </h2>
     <h2>
        {{firstName}} {{lastName}}
    </h2>
    <h2>{{counter*2}}</h2>
</div>
<script>
	new Vue({
        el:'#app',
        data:{
            message:'nihao',
            firstName:'kobe',
            lastName:'bryant',
            counter:100
        },
        
    })
</script>
```

mustache语法不仅可以直接写变量，还可以写语法。

## 2.2 插值操作--其他指令使用

- **v-once**: 不想改变DOM值，使DOM变成非响应式。

```html
<h2 v-once>
    {{message}}<!--message不会随数据更新而改变-->
</h2>
```

- **v-html**:直接展示html代码，而不是展示其字符串

```html
<div>
    <h2 v-html="url"></h2>
</div>
<script>
    new Vue({
            el:'#app',
            data:{
                url='<a href="https://www.baidu.com">百度一下</a>'
            }
        })
</script>
```

输出结果：[百度一下](https://www.baidu.com)

- **v-text**：和{{message}}作用相同，但会覆盖`<h2>`代码块中的字符会被覆盖掉，不灵活。

```html
<body>

    <div id="app">
        <h2 v-text="message"></h2>
        <h2>{{message}}</h2>
    </div>
    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el:'#app',//用于挂在要管理的元素
            data:{//定义数据
                message:'你好啊！'
            }
        })        
    </script>   
</body>
```

- **v-pre**：将例如大括号显示出来，不渲染数据

```html
<h2 v-pre>
    {{message}}
</h2>
```

输出结果：{{message}}

- **v-cloak**：当js延迟加载出来时，不显示dom渲染之前的值。{{message}}不会在渲染之前出现//cloak斗篷

```html
<!--<head>
    <style>
        [v-cloak]{
            display:none;
        }
    </style>
</head>-->
<div id="app" v-cloak>
    {{message}}
</div>
<script>
    ...
</script>
```

# 3：动态绑定属性

## 3.1 v-bind，动态绑定DOM属性

- v-bind的基本使用

```html
<body>

    <div id="app">
        <img v-bind:src="imgURL" alt="">
        <a v-bind:href="aHref"></a> 
        
        <!--语法糖的写法-->
        <img :src="imgURL" alt="">
        <a :href="aHref"></a> 
    </div>
    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el:'#app',//用于挂在要管理的元素
            data:{
                aHref="https://www.baidu.com",
				imgURL:'https://i0.hdslb.com/bfs/sycp/creative_img/202002/1e9bfa663a93374c8e10c3933cfc78f8.jpg'
            }
        })        
    </script>   
</body>
```

- v-bind简写/语法糖：`:`
- v-bind对象用法：

```html
<div>
    <h2 class="title" :class="{class1:isClass1,class2:isClass2}">{{message}}</h2>
</div>
<script>
    new Vue({
        data:{
            message:"showClassExample"
            isClass1:true,
            isClass2:false
        }
    })
</script>
```

**//title类固定，动态更改class1和class2**

`{类名:布尔值,类名：布尔值}`

- v-bind数组语法

```html
<div>
    <h2 class="title" :class="getClasses()">{{message}}</h2>
</div>
<script>
    new Vue({
        data:{
            message:"showClassExample",
            isClass1:true,
            isClass2:false
        }
        methods:{
        	getClasses:function(){
        		return{class1:this.isClass1,class2:this.isClass2}
    		}
    	}
    })
</script>
```

**注意：v-on中函数省略了()，但v-bind需要添加()**

```html
<div>
    <h2 class="title" :class="[class1,class2]">{{message}}</h2>
</div>
<script>
    new Vue({
        data:{
            message:"showClassExample",
            class1:'class1',
            class2:'class2'
        }
    })
</script>
```

## 3.2 作业，结合v-for和v-bind

作业：使用v-for显示一个电影名称列表，设置监听点击事件，当某个电影被点击，改变此电影名称字体颜色为红色。

答案：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .red{color:red}
    </style>
</head>
<body>
<script src="./js/vue.js"></script>
<div id="app">
    <ul>
        <li v-for="(m, index) in movies" @click="clickItem=index" :class="{red:clickItem==index}">{{m}}</li>
    </ul>
</div>
<script>
    	const app = new Vue({
            el:'#app',
            data: {
                movies: ['复仇者联盟', '生化危机', '星际大战', '钢铁侠'],
                clickItem:99,
            }
        })
</script>
</body>
</html>

```

## 3.3 v-bind动态绑定样式

- 样式的对象用法

```html
<div>
    <h2 :style="{fontSize:'50px',color:finalColor}">
        {{message}}
    </h2>
</div>
<script>
	new Vue({
        data:{
            message:'abc',
        	finalColor:'red'
        }
    })
</script>
```

> 注意：{fontSize:50px}为错误表达，50px会被解析为一个变量，50px必须加单引号

- 样式的数组用法

```html
<div>
    <h2 :style="[style1,style2]">
        {{message}}
    </h2>
</div>
<script>
	new Vue({
        el:'#app',
        data:{
            message:'abc',
        	style1:{fontSize:'100px'},
            style2:{color:'red'}
        }
    })
</script>
```

# 4：计算属性

## 4.1 计算属性的基本用法

```html
<div id="app">
    <h2>
        {{firstName+" "+lastName}}
    </h2>
    <h2>
        {{getFullName()}}
    </h2>
    <h2>
        {{fullName}}
    </h2>
</div>
<script>
	new Vue({
        el:'#app',
        data:{
        	firstName:'Lebron',
            lastName:'James'
        },
        computed:{
            fullName:function(){
                return this.fistName+' '+this.lastName;
            }
        },//计算属性
        methods:{
        	getFullName(){
                return this.fistName+' '+this.lastName;
            }
    	}
    })
</script>
```

**computed:计算属性的使用**

## 4.2 计算属性的复杂使用

```html
<div id="app">
    <h2>
        总价格：{{totalPrice}}
    </h2>
</div>
<script>
	new Vue({
        el:'#app',
        data:{
            books:{
                {id:110,name:'Unix编程',price:119},
            	{id:111,name:'代码大全',price:89},
        		{id:112,name:'现代操作系统',price:81}
            }
        },
        computed:{
            totalPrice:function(){
                for(let i=0;i<this.books.length;i++){
                    result += this.books[i].price;
                }
                /*
                for(let i in this.books){
                    result += this.books[i].price;
                }
                for(let book of this.books){
                    result += book.price;
                }
                */
                return result;
                
                
            }
        },//计算属性
    })
</script>
```

## 4.3 计算属性setter和getter

```html
<div id="app">
    <h2>
        {{fullName}}
    </h2>
</div>
<script>
	new Vue({
        el:'#app',
        data:{
            firstName:'Kobe',
            lastName:'Bryant'
        },
        computed:{
            /*
            fullName:{//fullName为一个对象
                //一般属性没有set方法，为只读属性
                set:function(){},//通过控制板，app.fullName='ABC'会调用此方法
                get:function(){
                    return this.firstName+' '+this.lastName;
                }
            }
            */
            //上述方法简写为
            fullName:function(){
        		return this.firstName+' '+this.lastName; 
    		}
        },//计算属性
    })
</script>
```

## 4.4 计算属性和methods对比

方法每次调用，程序都会重复执行；计算属性第一次计算出来，结果就保存在内存中。下次再调用，程序不会重复执行。

> 需要多次调用的计算结果，采用计算属性性能会更低。

## 4.5 跑马灯项目

一句文字实现，点击按钮，文字滚动播放，再点击文字停止滚动。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<script src="./js/vue.js"></script>
<div id="app">
    <button @click="lang">浪起来</button>
    <button @click="di">低调</button>
    <h4>{{msg}}</h4>
</div>
<script>
        //语法糖：简写
    	const app = new Vue({
            el:'#app',
            data: {
                msg:'猥琐发育。别浪~~！',
                intervalId:null
            },
            methods:{
                lang(){
                    //函数里的this.变量无法指向函数外部的变量
                    /*
                    setInterval(function(){
                        let start = this.msg.substring(0,1);//获取第一个字符
                        let end = this.msg.substring(1);//获取第一个字符后的所有字符
                        this.msg=end+start;//将第一个字符放到最后，实现跑马灯效果
                    },400)
                     */
                   //使用箭头函数解决函数内部this问题
                    if(this.intervalId!=null) return;
                    this.intervalId = setInterval(()=> {
                        let start = this.msg.substring(0,1);//获取第一个字符
                        let end = this.msg.substring(1);//获取第一个字符后的所有字符
                        this.msg=end+start;//将第一个字符放到最后，实现跑马灯效果
                    },400)

                },
                di(){
                    clearInterval(this.intervalId);
                    this.intervalId=null;//清楚定时器后需要重新把internalId设为null
                }
            }
        })
</script>
</body>
</html>

```



# 5: 常用指令

## 5.1 v-for的四种使用方式

- 循环数组

```html
<div id="app">
    <p v-for="item in list">
        {{item}}
    </p>
    <p v-for="(item,index) in list">
        {{item+"-----"+index}}
    </p>
</div>
<script>
	new Vue({
        el:'#app',
        data:{
            list:[1,2,3,4,5]
        },
        
    })
</script>
```

- 循环对象数组

```html
<div id="app">
    <p v-for="item in list">
        {{item.id}}--{{item.name}}
    </p>
   
</div>
<script>
	new Vue({
        el:'#app',
        data:{
            list:[
                {id: 1, name :'zs1'},
                {id: 2, name :'zs2'},
                {id: 3, name :'zs3'},
                {id: 4, name :'zs4'},
            ]
        },
        
    })
</script>
```

- 循环对象

```html
<div id="app">
    <p v-for="(val,key,index) in user">
        {{val}}--{{key}}--{{index}}
    </p>
   
</div>
<script>
	new Vue({
        el:'#app',
        data:{
            user:{
                id:1,
                name:'tuoni',
                gender:'nan'
            }
        },
        
    })
</script>
```

- 迭代数字

```html
<div id="app">
    <!--in后面可以使数组，对象数组，对象，还可以使数字。若为数字，count从1开始-->
    <p v-for="count in 10">
        第{{count}}次循环
    </p>
   
</div>
<script>
	new Vue({
        el:'#app',
        data:{},   
    })
</script>
```

**注意：count从1开始计数**

- v-for循环中key属性的使用

```html
<div id="app">
    <div>
        <label>Id:
        	<input type="text" v-model="id">
        </label>
        <label>Name:
        	<input type="text" v-model="name">
        </label>
        <button @click="add">添加</button>
    </div>
    <p v-for="item in list" :key="item.id">
        <input type = "checkbox" >
       {{item.id}}--{{item.name}}
    </p>
   
</div>
<script>
	new Vue({
        el:'#app',
        data:{
            id:'',
            name:'',
            list:[
                {id:1,name:'lisi'},
                {id:2,name:'yinzheng'},
                {id:3,name:'zhaogao'},
                {id:4,name:'hanfei'},
                {id:5,name:'kongzi'},
            ]
        },
        methods:{
            add(){
                this.list.push({id:this.id,name:this.name});
                //this.list.unshift({id:this.id,name:this.name});
            }
        }
    })
</script>
```

## 5.2 v-on指令

```html
<body>
    <script src="./js/vue.js"></script>
    <div id="app">
        <button v-on:click="show">按钮</button>
    </div>
    <script>
        //语法糖：简写
    	const app = new Vue({
            el:'#app',
            data:{
                counter:0
            },
            methods:{
                show(){
                    alert('Hello');
                }
            }
        })
        
        //原始js
        //1.拿buttom元素
        //2.添加监听时间
    </script>
</body>
```

- 事件修饰符
  - .stop 阻止冒泡//@click.stop设置再内部DOM。不会触发@click.stop外部DOM的点击事件
  - .prevent 阻止默认行为//@click.prevent。比如阻止a标签跳转到href地址
  - .capture 添加事件倾听器实现捕获模式//给外层DOM 添加@click.capture，会先处理外部点击事件。
  - .self 只当自身被点击才触发事件//给外部DOM添加@click.self，只有点击外部DOM时才能触发它。也可阻止冒泡
  - .once 只触发一次事件处理函数。

事件修饰符可以叠加使用 @click.prevent.once先后顺序无区别。

## 5.3 v-model

可以实现表单元素的model中的数据双向绑定。**v-model只能用在表单元素中**，input(redio, text, address, email...),select , checkbox, textarea

```html
<input type="text" v-model="msg">
```

- 简易计算器案例

```html
<div id="app">
    <input type="text" v-model="n1">
    <select v-model="opt">
        <option value="+">+</option>
        <option value="-">-</option>
        <option value="*">*</option>
        <option value="/">/</option>
    </select>
    <input type="text" v-model="n2">
    <button @click="calculate">=</button>
    <input type="text" v-model="result">
</div>
<script>
	const vm = new Vue({
        el:'#app',
        data:{
            n1:0,
            n2:0,
            result:0,
            opt:'+'
        }
        methods:{
        calculate(){
        switch(this.opt){
            case '+':
                this.result = parseInt(this.n1) + parseInt(this.n2);
                break;
            case '-':
                this.result = parseInt(this.n1) - parseInt(this.n2);
                break;
        	case '*':
                this.result = parseInt(this.n1) * parseInt(this.n2);
                break;
            case '/':
                this.result = parseInt(this.n1) / parseInt(this.n2);
                break;
         //this.result = eval(this.n1+this.opt+this.n2)
         //eval会解析字符串并执行
        }
    }
    }
    })
</script>
```

## 5.4 v-if和v-show的使用

```html
<body>
    <script src="./js/vue.js"></script>
    <div id="app">
        <button @click="flag=!flag">
            切换
        </button>
        <h3 v-if="flag">
            这是v-if控制的元素
        </h3>
        <h3 v-show="flag">
            这是v-show控制的元素
        </h3>
    </div>
    <script>
        //语法糖：简写
    	const app = new Vue({
            el:'#app',
            data:{
                flag:true
            },
            methods:{
                
            }
        })
        
        //原始js
        //1.拿buttom元素
        //2.添加监听时间
    </script>
</body>
```

- v-if:会删除或创建元素  //v-if消耗切换性能，如果元素设计频繁的切换不要使用。

- v-show：不会删除DOM，而是修改其可见属性dispaly:none //如果元素可能永远也不会显示出来，最好使用v-if

# 6：过滤器

**概念**：Vue.js中允许自定义过滤器，可以用作一些常见的文本格式化。过滤器可以用在两个地方：mustache插值和v-bind

```html
<!--过滤器调用格式     {{name | nameope}}-->
<script>
    //过滤器定义语法
    //过滤器中的function，第一个参数，永远是过滤器管道符（|）前面传过来的参数
	Vue.filter('过滤器名称',function(data){})
</script>
```

**注意:当全局和私有过滤器，使用相同名称时，优先调用私有过滤器**

## 6.1 定义全局过滤器

全局过滤器，就是所有的vm实例都能用

过滤器的**基本使用**

```html
<body>
<script src="./js/vue.js"></script>
<div id="app">
    <p>
        {{ msg | msgFormat('牛逼') | test}}
    </p>
</div>
<script>
    //定义一个Vue全局过滤器
    Vue.filter('msgFormat',function(msg, arg){//arg是msgFormat输入的参数。第一个为参数msg为默认的插值
        return msg.replace(/傻子/g,arg)//正则替换，g表示全部替换
    })
    
    //定义多个过滤器
    Vue.filter('test',function(msg){
        return msg+"这是第二个过滤器"
    })
    //语法糖：简写
    const app = new Vue({
        el:'#app',
        data:{
            msg:'你是真的傻子哦,人是傻子，脑袋笨'
        },
        methods:{

        }
    })

</script>
</body>
```

## 6.2 定义私有过滤器

```html
<body>
<script src="./js/vue.js"></script>
<div id="app">
    <p>
        {{ msg | msgFormat('牛逼') | test}}
    </p>
</div>
<script>
    //语法糖：简写
    const app = new Vue({
        el:'#app',
        data:{
            msg:'你是真的傻子哦,人是傻子，脑袋笨'
        },
        methods:{},
        filters:{
            msgFormat:function(msg, arg){
                return msg.replace(/傻子/g,arg)//正则替换，g表示全部替换
            }
        }
    })

</script>
</body>
```

# 7：自定义

## 7.1 自定义按键修饰符

```html
<label>Name：
<input type="text" v-model="name" @keyup.enter="add">
</label>
```

**@keyup**:绑定键盘

1. 内置修饰符.enter .tab .delete .esc .space .up .down .left .right

2. 其他的键可使用js定义的键盘码。比如**@keyup.113**表示F12 。

```html
<label>Name：
<input type="text" v-model="name" @keyup.f12="add">
</label>

<script>
	//自定义全局按键修饰符
    Vue.config.keyCode.f12 = 113
</script>
```

## 7.2 自定义指令

1. 定义全局指令

比如自定一个指令v-focus

```html
<script>
    //使用Vue.directive()定义全局的指令
    //其中：参数1：指令的名称，定义的时候，不要加v-前缀。但在调用的时候必须在指令名称前加v-前缀
    //参数2是一个对象，对象身上有一些指令相关的函数，这些函数可以在特定的阶段执行相关操作
	Vue.directive('focus',{
        bind:function(el){
            //每当指令被绑定到元素上时，会执行这个函数。只执行一次
            //第一个参数永远时el，表示被绑定了指令的那个元素，el是一个原生的JS对象。
            //在元素刚绑定了指令的时候，还没有插入到DOM中去，这时调用focus方法没有作用。
            //el.focus
            
        },
        inserted:function(el){
           //inserted表示元素插入到dom中时执行，只执行一次
            el.focus
        },
        updated:function(){},//当vNode更新的时候，会执行。可能多次触发
    })
</script>
```

**.focus**：JS方法，可以将焦点集中到DOM对象上，类似于，鼠标单击一次输入框。

使用钩子函数参数

- el：指令绑定的元素，用来直接操作DOM
- binding：一个对象，包含以下属性：
  - name:指令名称——color
  - value:指令的绑定值。会计算比如v-color="1+1"，binding.value=2
  - expression:指令绑定值，不会计算, binding.expression='1+1'

```html
<div v-color="'red'">
    
</div>
<script>
	Vue.directive('color',{
        bind:function(el,binding){
            el.style.color = binding.value
        }
    })
</script>
```

2. 定义私有指令

```html
<script>
	 const app = new Vue({
        el:'#app',
        data:{
            msg:'你是真的傻子哦,人是傻子，脑袋笨'
        },
        methods:{},
        filters:{},
        directives:{
            'color':{
                bind:function(el,binding){
                     el.style.color = binding.value
                }
            }
        }
    })

</script>
```

**简写：**

大多数情况下，旨在`bind`和`update`钩子上做重复动作，可以简写

```html
<script>
    directives:{
                'color':function(el,binding){//等同于把方法同时写到bind和update中
                         el.style.color = binding.value
                    }
            }
</script>
```

# 8：vue-resource实现get，post，jsonp请求

vue-resource依赖于vue，需要在vue.js后导入vue-resource.js包。

```html
<body>
    <script src="./js/vue.js"></script>
    <script src="./js/vue-resource.js"></script>
    
    <div>
        <button @click="getInfo">
            getInfo
        </button>
        <button @click="postInfo">
            getInfo
        </button>
        <button @click="jsonpInfo">
            getInfo
        </button>
    </div>  
    
    <script>
    	const app = new Vue({
        el:'#app',
        data:{
            msg:'你是真的傻子哦,人是傻子，脑袋笨'
        },
        methods:{
            getInfo(){      this.$http.get('http://vue.studyit.io/api/getlunbo').then(function(result){
                    //result是返回的结果，包括头部、数据等
                    console.log(result.body)
                }),
             postInfo(){
                 //手动发起的post请求没有表单数据
                 //通过post方法的第三个参数，设置提交内容类型为普通表单格式。
                 this.$http.post('http://vue.studyit.io/api/post',{},{emulateJSON:true}).then(result=>{
                     console.log(result.body)
                 }),
              jsonpInfo(){
                     this.$http.jsonp('http://vue.studyit.io/api/jsonp').then(result=>{
                         console.log(result.body)
                     })
                 }
             }     
            }
        }
        })
    </script>
</body>
```

