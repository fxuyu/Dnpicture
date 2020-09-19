# dnpicture
Project name:dnpicture

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run dev:mp-weixin
```

---
### 下面是我学习的笔记 记载的很乱 


如果发现在vscode编辑代码时，微信开发者工具不能实时编译页面，请在vscode中打开终端(CTRL+~【tab上面的那个键】) 然后在终端中输入<br>
npm run dev:mp-weixin<br>
等待命令执行完成 在微信开发者工具中就可以看到页面的实时编译情况了  

创建项目 不知道为什么在桌面才可以运行成功<br>
vue create -p dcloudio/uni-preset-vue dnpicture

定义数据的时候 对象用{} 数组用[]

运行项目
npm run dev:mp-weixin

有时候不知道为啥安装了sass 运行新项目还是不行 要重新安装 进入到创建的项目里 进行安装<br>
cnpm install sass-loader node-sass

uni-ui 的分段器 引入组件 注册组件 组件需要数据 <br>

数组对象需要用上 map方法

小程序在js的data中不需要return 但是vue需要return <br>
生命周期 onload是页面初始化 onReady才是页面渲染完

目录结构 
D:\uniapp\my-project\src\pages\index  是主页

static 是一些静态文件 图片之类的

D:\uniapp\my-project\src 里面的App.vue 相当于 微信小程序里面的 APP.js 里面有生命周期函数等等 main.js是整个项目的入口文件 <br>
pages.json 类似微信小程序的app.json 里面新增文件

由于国内的一些原因 用npm无法下载 下载不成功 可以用 cnpm 试下。

切换国内镜像

$ npm install -g mirror-config-china --registry=http://registry.npm.taobao.org

安装sass-loader、node-sass（-D 是 --save-dev 的简写）

$ npm install sass-loader node-sass -D

切换为国内镜像后，安装只需少量时间

```
onLoad(){
        //源生的微信小程序的api
        wx.request({
            url:"https://api-hmugo-web.itheima.net/api/public/v1/home/swiperdata",
            success(res){
                console.log(res);
            }
        })

        //2 uni-api 他返回状态是个数组 第(索引)0个值 是表示成功与失败 如果是成功的话是null
        uni.request({
            url:"https://api-hmugo-web.itheima.net/api/public/v1/home/swiperdata",
        })
        .then(res=>{
            console.log(res);
        })
    }
```

es6 模版字符串 是这个`` 不是这个'' 模版字符串和$符号是结合一起使用的

路径 * @/ 是webpack设置的路径别名，代表什么路径，要看webpack的build文件夹下webpack.base.conf.js里面对于@是如何配置 

更多的时候 @代表是src这个文件夹的路径

数组和索引确定在一起 就能成为一个对象

const解构赋值 但是解构出来的数据 不能被改变
```
<style lang="scss" scoped>分散式（参考大部分的后台系统）
分散式是vue官网推荐的一种方式
就是每个模块是一个独立的.vue文件
里面包含template模版，js，css，这三种都用标签封装起来，成为一个vue实例，
实例解析的时候逐步解析每个标签的内容，所以这个vue文件里面的sass是局部的，
只有这个实例界面生效，一般在标签上面加scoped来局部化，去掉scoped就会变成全局样式。
```

分段器的使用

父组件可以改变子组件的值

子组件也可以改变父组件的值，子组件可以获取父组件的元素 但是不推荐 建议使用单向数据流

项目文档:

https://www.showdoc.cc/414855720281749?page_id=3678621017219602

---
PS：有些模块没有接口就没有做了.所以有些模块是空的


![截图1](https://github.com/fxuyu/Dnpicture/blob/master/image/0.PNG)

![截图2](https://github.com/fxuyu/Dnpicture/blob/master/image/0-1.PNG)

![截图3](https://github.com/fxuyu/Dnpicture/blob/master/image/0-2.PNG)

![截图4](https://github.com/fxuyu/Dnpicture/blob/master/image/1.PNG)

![截图5](https://github.com/fxuyu/Dnpicture/blob/master/image/1-1.PNG)

![截图6](https://github.com/fxuyu/Dnpicture/blob/master/image/2.PNG)

![截图7](https://github.com/fxuyu/Dnpicture/blob/master/image/3.PNG)

![截图8](https://github.com/fxuyu/Dnpicture/blob/master/image/3-1.PNG)

![截图9](https://github.com/fxuyu/Dnpicture/blob/master/image/3-2.PNG)

![截图10](https://github.com/fxuyu/Dnpicture/blob/master/image/4.PNG)

![截图11](https://github.com/fxuyu/Dnpicture/blob/master/image/4-1.PNG)

![截图12](https://github.com/fxuyu/Dnpicture/blob/master/image/4-2.PNG)

![截图13](https://github.com/fxuyu/Dnpicture/blob/master/image/4-3.PNG)
