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
//最终效果就是返回值，无法使用异步（定时器），会吃掉它的返回值
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
    },
        /*简写 只读不写情况下（只有get） 配置成函数的形式 但是在上面调用的时候，还是按数据的方式调用（不带()）
                 fullName(){
                     return this.firstName+"-"+this.lastName
                */
        isSuccessNow(){
            return this.isSuccess
        }
},
```

#### 4.监视模块  **watch**

**Computed和Watch之间的区别:**

1.computed能完成的功能, watch都可以完成。

2.watch能完成的功能, computed不一定能完成,例如:watch可以进行异步操作(定时器)。

**两个重要的小原则:**

1.所被Vue管理的函数,最好写成普通函数,这样this的指向才是vm或组件实例对象。

2.所有不被Vue所管理的函数(定时器的回调函数、ajax的回调函数等),最好写成箭头函数这样this的指向才是vm或组件实例对象。

```javascript
//监视模块 可以监视任何模块内的值 可使用异步（定时器） 因为它改的是变量
watch:{
    isSuccess:{
        immediate:true,//初始化时调用一次handler
            //在属性方式改变的时候调用
            //参数 改变后的新值，改变前的旧值
            handler(newValue,oldValue){
            //定时器  延迟1000毫秒启动（1秒）
            setTimeout(()=>{
                console.log("isSuccess被修改了",newValue,oldValue)
            },1000)
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
}
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

### 7.样式绑定  

不同的情况使用不同的样式绑定，ID（唯一的）一般是用于绑方法，Class（可重复的）用于绑样式

```html
<!-- 这里使用class（.） 先定义好样式再套div 一般用于多套相同的div 用id的说明它是唯一的 -->
<!-- 正常使用方式 id绑定方法 class绑定样式 因为方法一般都是唯一的，只为它一个div服务，而样式可能会服务多个div -->
<div id="cssStyleFunction" >
    <!-- :class 绑定方式 会去data里面找相应的class 适用于样式的类名不确定。需要动态指定-->
    <div class="cssStyle" :class="a">{{name}}</div>
    <!-- 绑定数组，这样就把数组里的所有值都绑定进去了，多样式绑定,适用于要绑定的样式个数不确定，名字也不确定-->
    <div class="cssStyle" :class="classArr">{{name}}</div>
    <!-- 绑定对象，这样就把对象里的所有值都绑定进去了，再用在对象中设置boolean值进行开关，适用于要绑定的样式个数确定，名字确定，但是要看情况是否开关-->
    <div :class="classObj">{{name}}</div>
    <!-- Style绑定 -->
    <div class="cssStyle" :style="{fontSize:fSize+'px'}">{{name}}</div>
    <!-- 当然最好还使用对象绑定，设置好相应的Style然后绑定进去  也可以使用数组，这里不再赘述-->
    <div class="cssStyle" :style="StyleObj">{{name}}</div>
</div>
```

```javascript
//样式绑定
new Vue({
    el:'#cssStyleFunction',
    data:{
        name:"样式绑定",
        a:"cssStyleBackground",
        //数组，绑定多个样式
        classArr:['cssStyleBackground'],
        classObj:{
            //通过boolean值进行开关样式，可多个
            cssStyle:true,
            cssStyleBackground:false
        },
        //设置变量，用于Style字体大小设定
        fSize:40,
        //使用对象绑定样式
        StyleObj:{
            fontSize:'40px',
            color:'red'
        }
    }
})
```

### 8.条件渲染 Template

​	页面刚加载完毕时，template的内容是不会被渲染的（就算里面有内容），只有在后面你进行语法操作（这里是将n取反）后，它才会进行一次反应，不然你开始就为true它也不会渲染，因为你并没有对它进行一次操作。

```html
<!-- 条件渲染 -->
<div id="templateRoot">
    <h2 v-show="true">条件渲染</h2>
    <button @click="n=!n">点我渲染template</button>
    <!-- template 模板标签 可以在不影响结构的情况下将内容包裹起来一起设定（没有id的div）如果用div的话可能会导致外层div无法拿到内层div -->
    <!-- template只能v-if  在页面中不会显示这个结构 只用于操作包含在里面的DOM -->
    <!-- 重点！页面刚加载完毕时，template的内容是不会被渲染的（就算里面有内容），只有在后面你进行语法操作（这里是将n取反）后，它才会进行一次反应，不然你开始就为true它也不会渲染，因为你并没有对它进行一次操作 -->
    <template v-if="n">
        <!-- 隐藏单元 还存在于控制台（F12）中 -->
        <h2 v-show="true">条件渲染</h2>
        <!-- 删除单元 不存在于控制台中 -->
        <h2 v-if="true">条件渲染</h2>
        <h2>条件渲染</h2>
        <!-- 可以在DOM中写原生语言 不允许被打断 不建议使用！还是在Script操作完再在DOM中操作 -->
        <!-- <h2 v-else-if="true">条件渲染</h2> -->
    </template>
</div>
```

```javascript
//条件渲染
new Vue({
    el:'#templateRoot',
    data:{
        n:false
    }
})
```

### 9.列表渲染 Key

![image-20220328174934228](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220328174934228.png)

```html
<!-- 列表渲染 -->
    <div id="listRoot">
        <!-- 数组渲染 -->
        <ul>
            <!-- 使用key绑定数据，防止后面添加数据时候数组的索引错乱导致数据错乱，还会导致效率问题，添加删除的时候会再次操作虚拟DOM，如果没有key，就无法复用，要重新加载一遍，这边绑定了ID  视频p30-->
            <li v-for="p in persons" :key="p.id">姓名：{{p.name}}-年龄:{{p.age}}</li>
        </ul>
        <!-- 对象渲染 -->
        <ul>
            <li v-for="(value,key) of language">{{key}}-{{value}}</li>
        </ul>
    </div>
```

```javascript
//列表渲染
new Vue({
    el:'#listRoot',
    data:{
        persons:[
            {id:'001',name:'张一',age:18},
            {id:'002',name:'陈二',age:19},
            {id:'003',name:'李三',age:20}
        ],
        language:{
            name:'Java',
            difficulty:'normal',
            money:'8000'
        }
    }
})
```

### 10.表单收集

![image-20220330154815490](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220330154815490.png)

```html
<!-- 表单收集 -->
<div id="formCollection">
    <!--submit 提交事件 绑定方法sub prevent阻止默认事件（跳转页面）-->
    <form @submit.prevent="sub">
        <!--自动省略前后的空格（字符串中间的空格不会省略）-->
        账号：<input type="text" v-model.trim="account"> <br/><br/>
        密码：<input type="password" v-model="password"> <br/><br/>
        <!-- typeNumber控制前端数据类型 v-m.number控制后端数据类型，默认都是字符串，需转化再传入后端 -->
        年龄：<input type="number" v-model.number="age"> <br/><br/>
        性别：
        <!-- 设置name确认为单选组 -->
        <!-- 设置value值告诉后端数据-->
        男<input type="radio" name="sex" value="男" v-model="sex">
        女<input type="radio" name="sex" value="女" v-model="sex">
        爱好：
        学习<input type="checkbox" value="学习" v-model="hobby">
        打游戏<input type="checkbox" value="打游戏" v-model="hobby">
        吃饭<input type="checkbox" value="吃饭" v-model="hobby">
        <br/><br/>
        所属校区
        <select v-model="city">
            <option value="">请选择校区</option>
            <option value="beijing">北京</option>
            <option value="shanghai">上海</option>
            <option value="fujian">福建</option>
        </select>
        其他信息
        <!-- lazy  只有失去焦点的时候才收集数据 提升性能 -->
        <textarea v-model.lazy="userInfo.other"></textarea>
        <input type="checkbox" v-model="agree">阅读并接受<a href="http://happyeleven.net">用户协议</a>
        <button>提交</button>
    </form>
</div>
```

```javascript
//表单收集
new Vue({
    el:'#formCollection',
    data:{
        name:'表单收集',
        account:'请输入你的账号',
        password:'',
        age:'',
        sex:'女',//可以设置默认值，在html中会自动匹配相应选项并勾选
        hobby:[], //多选请设置成数组!
        city:'beijing',
        agree:'true',
        //最好还是套一层类别，不要直接访问data 这里就不做
        userInfo:{
            other:'随便写点什么',
        }
    },
    methods:{
        sub(){
            alert("提交！"),
                //json格式输出 也可以直接输出data
                console.log(JSON.stringify(this._data))
            console.log(JSON.stringify(this.userInfo))
        }
    }
})
```

