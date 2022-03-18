#  Vue

**官方文档**：[Vue.js (vuejs.org)](https://cn.vuejs.org/)

**教程视频：**https://www.bilibili.com/video/BV1Zy4y1K7SH

### 1.安装开发者工具

Edge：直接去扩展商店里面搜：Vue.js devtools 即可 安装完毕后记得开启允许url访问

### 2.基础挂载使用

插值语法和指令语法

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <!-- 上面引入Script文件 -->
        <script src="js/vue.js"> </script>
        <title>Test</title>
    </head>
    <body>
        <!-- 通过div 创建容器 id为唯一标识符（不能重复使用） 参数设置{{}} 表达式，也可以使用方法调用-->
        <div id="headTitle">
            <!-- 插值语法  写标签内 -->
            <h1>Hello {{name}},{{est.age}},{{Date.now()}}</h1>
            <hr/>
            <!-- 指令语法 写标签属性 绑定 当做el表达式去指定 -->
            <a v-bind:href="url">指令语法</a>
            <!-- 简写  就剩：了-->
            <!-- <a :href="url">指令语法</a> -->
        </div>
    </body>
    <!-- 下面编写Script代码 测试使用  应该是拆分成两个文件 -->
    <script>
        // 创建Vue常量来管理容器  el为指定容器id  一个容器只能被一个Vue接管  data里为容器内参数指定
        const x =new Vue({
            //内部绑定ID
            el:'#headTitle',
            //对象式
            data:{
                name:"Test",
                url:"http://happyeleven.net",
                // 可以套变量 j进行层级分类
                est:{
                    age:100,
                }
            }
            //函数式
            // data:function(){
            //     return{
            //         name:"Test"
            //     url:"http://happyeleven.net",
            //     // 可以套变量 j进行层级分类
            //     est:{
            //         age:100,
            //     }
            // }
        })
        //外部绑定ID
        // x.$mount("#headTitle"),
    </script>
</html>
```

### 3.模块实现 

#### 1.普通数据模块 **data**

```javascript
//数据属性模块
data:{
    firstName:"张",
    lastName:"三"
},
```

#### 2.函数模块 **method**

```javascript
//在其他模块中，需要调用Vue内其他模块的变量的话，需要+this
//函数属性模块
methods:{
    getLastName(){
        //此处的this是vm
        return this.lastName.slice(0,3)
    }
```

#### 3.计算属性模块 **computed**

```javascript
//计算属性模块
computed:{
    fullName:{
        get(){
            //此处的this是vm
            //get什么时候调用？初次调用时，改变时，所以会带缓存，不像函数属性模块，每次都要调用
            return this.firstName+"-"+this.lastName
        },
            //set什么时候调用，修改的fullName的时候调用
            //默认值 可省略 
            set(value){
                //请注意：修改的是fullName 并不是frisName和lastName
                //但是fullName的Get是这两个变量组成的，所以必须修改这两个变量才会使得Get时让fullNameReturn出去的值发生改变
                const arr=value.split('-')
                //最终修改的还是get内方法所依赖的两个变量
                this.firstName=arr[0]
                this.lastName=arr[1]
            }
    }
    //简写 只读不写情况下（只有get） 配置成函数的形式 但是在上面调用的时候，还是按数据的方式调用（不带()）
    // fullName(){
    //     return this.firstName+"-"+this.lastName
    // }
}
```

#### 4.监视模块  **watch**

```javascript
//监视模块 可以监视任何模块内的值
watch:{
    isSuccess:{
        immediate:true,//初始化时调用一次handler
            //在属性方式改变的时候调用
            //参数 改变后的新值，改变前的旧值
            handler(newValue,oldValue){
            console.log("isSuccess被修改了",newValue,oldValue)
        }
    },
        //检测数据模块里数据类里的变量
        'numbers.a':{
            handler(newValue,oldValue){
                console.log("a被修改了",newValue,oldValue)
            }
        },
            //深度检测数据类里的变量是否改变（所有变量都会被检测）
            numbers:{
                //开启深度检测，如果不开启，只会在numbers增删新的变量才回被监测
                //开启后，只要这个numbers里的变量发生变化，就都会被检测到了
                deep:true,
                    handler(){
                    console.log("numbers被修改了",newValue,oldValue)
                }
            }
    //简写 不带其他设置 只有handler
    // numbers(newValue,oldValue){
    //     console.log("numbers被修改了",newValue,oldValue)
    // }
}            
})
// vmm对象外监视
// vmm.$watch('isSuccess',{
//     immediate:true,//初始化时调用一次handler
//     //在属性方式改变的时候调用
//     //参数 改变后的新值，改变前的旧值
//     handler(newValue,oldValue){
//     console.log("isSuccess被修改了",newValue,oldValue)
//     }
// })
//简写
// vmm.$watch('isSuccess',function(newValue,oldValue){
//     console.log("isSuccess被修改了",newValue,oldValue)
// })
```

#### 案例

```html
<!--  各种模块实现方式 -->
<div id="mustacheImpl">
    各种模块实现方式<br/>
    <!-- 双向绑定 -->
    姓<input type="text" v-model="firstName"><br/>
    名<input type="text" v-model="lastName"><br/>
    <!-- 添加函数获取想要的结果 尽量不要在模板里面添加太多的表达式，最好全部都在JavaScript里处理完后再传过来  -->
    <!-- 函数模块 -->
    姓名:<span>{{firstName.slice(0,3)}}-{{getLastName()}}</span>
    <!-- 计算属性模块 -->
    计算属性姓名:<span>{{fullName}}</span><br/>
    <!-- 监视模块 -->
    <button @click="changeInfo">监视模块：{{isSuccessNow}}</button>
</div>
```

```javascript
new Vue({
    el:"#mustacheImpl",
    //数据属性模块
    data:{
        firstName:"张",
        lastName:"三"
    },
    //在其他模块中，需要调用Vue内其他模块的变量的话，需要+this
    //函数属性模块
    methods:{
        getLastName(){
            //此处的this是vm
            return this.lastName.slice(0,3)
        }
    },
    //计算属性模块
    computed:{
        fullName:{
            get(){
                //此处的this是vm
                //get什么时候调用？初次调用时，改变时，所以会带缓存，不像函数属性模块，每次都要调用
                return this.firstName+"-"+this.lastName
            },
            //set什么时候调用，修改的fullName的时候调用
            //默认值 可省略 
            set(value){
                //请注意：修改的是fullName 并不是frisName和lastName
                //但是fullName的Get是这两个变量组成的，所以必须修改这两个变量才会使得Get时让fullNameReturn出去的值发生改变
                const arr=value.split('-')
                //最终修改的还是get内方法所依赖的两个变量
                this.firstName=arr[0]
                this.lastName=arr[1]
            }
        }
        //简写 只读不写情况下（只有get） 配置成函数的形式 但是在上面调用的时候，还是按数据的方式调用（不带()）
        // fullName(){
        //     return this.firstName+"-"+this.lastName
        // }
    }
```



### 4.单双相绑定

容器部分

```html
<div id="bind">
    <!-- 单项绑定 后端改变前端改变，前端改变，后端不改变 ，这样不会影响其他容器 简写   :value-->
    单项绑定：<input type="text" v-bind:value="name">
    <!-- 单项绑定 后端改变前端改变，前端改变，后端改变 ,影响所有使用这个参数（name）的容器 简写  v-model-->
    双项绑定：<input type="text" v-model:value="name">
</div>
```
绑定部分
```javascript
new Vue({
    el:"#bind",
    data:{
        name:"Eleven",
    }
})
```

### 5. Object.defineProperty

https://www.bilibili.com/video/BV1Zy4y1K7SH P11

给定义好的对象追加变量的方法  

```javascript
new Vue({
    el:"#bind",
    data:{
        name:"Eleven",
    }
})
let  person={
    name:"张三",
    sex:"男",
}

let number=18
//额外添加变量，但是默认不接受枚举，无法被修改
Object.defineProperty(person,'age',{
    // value:16,//注意小写，Value无法识别
    enumerable:true,//设置是否能被枚举 默认是false
    // writable:true,//控制属性是否可以被修改 默认是false
    configurable:true,//控制属性是否可以被删除，默认是false
    //当有人读取person的age属性时，get函数就会被调用，且返回值就是age的值
    //用于映射age值，如果需要读取age属性，它会经过处理这个get函数处理后再给
    //在使用Object.defineProperty() 定义对象属性的时候，如果设置了 set 或 get, 就不能设置 writable 和 value 中的任何一个，否则就会报错
    get(){
        return number;
    },
    //当有人修改number性时，set函数就会被调用
    set(number){
        number=value;
    }  
})
```

如何看一个变量是固定的常量还是被其他数据所代理了？网页内查看控制台 看它的值是已经显示出来了 还是...  需要点击显示

### 6.事件处理

#### 事件修饰符

[vue中的事件修饰符有哪些，以及如何使用 - 简书 (jianshu.com)](https://www.jianshu.com/p/ff19cb0cccfd)

1 .stop：防止事件冒泡，等同于JavaScript中的event.stopPropagation()
2 .prevent：防止执行预设的行为，等同于JavaScript中的event.preventDefault()
3.capture：捕获冒泡
4.self：将事件绑定到自身，只有自身才能触发
5.once：只触发一次
6.passive：不阻止事件的默认行为

#### 键盘事件  

键码值 [event.keyCode列表 - 水木 - 博客园 (cnblogs.com)](https://www.cnblogs.com/hsapphire/archive/2009/12/16/1625642.html)

Vue常用的按键别名 [Vue 中的按键别名（.enter）和修饰键（.ctrl）及 组合键的使用 - 简书 (jianshu.com)](https://www.jianshu.com/p/818226fe813f)

```html
<!-- 事件处理 -->
<div id="eventInfo">
    <!-- v-on: == @ -->
    <button v-on:click="showInfo">按钮事件处理</button>
    <!-- 可以传递参数 默认自带传event参数，但是如果你还有其他参数要传，那就必须要加上去了-->
    <button @click="showInfoNumber(66)">按钮事件处理(传参)</button>
    <!-- 事件修饰符  在@click修饰了一个prevent 阻止默认事件产生-->
    <a href="http://happyeleven.net" @click.prevent="showInfoPlus($event)">事件处理(修饰符)</a>
    <!-- 键盘事件 -->
    <!-- keyDown按下即触发 keyup 按下抬起触发(任意键)  加上修饰符即可过滤-->
    <input type="text" placeholder="按下回车提示输入" @keydown.enter="showInfoKeyboard">
</div>
```

```javascript
//Vue事件监视
new Vue({
    el:"#eventInfo",
    //配置方法  不支持重载
    methods: {
        showInfo(){
            alert("事件处理")
        },
        //从容器中获取参数
        showInfoNumber(number){
            alert(number)
        },
        showInfoPlus(event){
            //event.preventDefault()//阻止默认行为 也可以在html里加相应的函数阻止 详情看上面
            alert("事件修饰符")
        },
        showInfoKeyboard(){
            //使用键码值过滤 也可以在上面过滤
            if(event.KeyCode==13){
                console.log("按下按键"+event.target.value)
            }
        }
    },        
```
