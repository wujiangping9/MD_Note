# Vue学习笔记

## 一.新版Vue-cli项目配置mock数据

1.最新的vue-webpack-template 中已经去掉了dev-server.js ，如何进行本地数据操作？

2.dev-server.js 改用webpack-dev-conf.js代替，新版webpack.dev.conf.js配置如下：
​	

（1）在const portfinder = require(‘portfinder’)后面添加

```js
    const express = require('express')//获取express对象
    const app = express()
    var meetsData = require('./../mock/meets.json')//加载本地数据文件
    var routes = express.Router()//获取服务端路由
    app.use('/meets', routes)
```

*注：在编写时我只添加以下一句也可以获取 json数据*

```js
var meetsData = require('../mock/goods.json')//加载本地数据文件
```

（2）找到devServer,在里面添加

```js
before(app) {
    app.get('/meets', (req, res) => {
    res.json(meetsData)//接口返回json数据
      })
    }
```

（3）在vue组件中导入axios，然后使用axios获取json数据

```js
export default{
		data(){
	            return {
	                meetsList: []
	
	            }
	        },
		mounted:function () {
	    	this.getMeetsList();
	      },
		methods:{
		      getMeetsList(){
		          axios.get("/meets").then((result)=>{
		              var res = result.data;
		              this.meetsList = res.result;
		          })
		      }
		}
	}
```

## 二.<ul>标签起到撑开<div>标签的目的

```html
<div class="list-wrap">
        <ul>
          <li v-for="(item,index) in meetsList" class="layout-float">
              <div>
                <a href="#"><img class="context_img" v-lazy="'/static/logo/'+item.picturePath" alt=""/></a>
              </div>
              <div class="block_detail">
                <div>
                  <router-link :to="{name:'meeting_detail',params:{name:item.meetName}}" class="link-nameStyle">会议名字：{{item.meetName}}</router-link>
                </div>
                <div class="link-nameStyle">会议内容：{{item.meetContext}}</div>
                <div class="link-nameStyle">参加人员：{{item.joinPeople}}</div>
              </div>
          </li>
        </ul>
      </div>
```

```css
 .list-wrap{
    border: solid red 1px;
  }
  .list-wrap ul::after{
    clear: both;
    content: '';
    height: 0px;
    display: block;
    visibility: hidden;
  }
```

## 三. router-link传递参数并获取

```vue
 <router-link :to="{name:'meeting_detail',params:{name:item.meetName}}">会议名字：{{item.meetName}}</router-link>
```

```js
name:this.$route.params.name
```

