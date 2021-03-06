### 介绍
Vue Router是Vue.js的官方路由。
### 作用
本质就是管理Vue.js中的路由与组件的映射关系。在传统的前端项目中，每个页面对应一个HTML文件，每次切换路由则加载对应的HTML文件。而在Vue项目中，一般只有一个HTML文件，切换路由改变的则是组件，无需请求新的HTML文件。
### 安装
- 直接下载/CDN

  访问 [https://unpkg.com/vue-router@4](https://unpkg.com/vue-router@4) 下载

- npm

  `npm install vue-router@4`

### 基本概念

- SPA

  单页Web应用（single page web application，SPA）： SPA 是一种特殊的 Web 应用，是加载单个 HTML 页面并在用户与应用程序交互时动态更新该页面。所有的活动都在于一个 Web 页面中进行，仅在该 Web 页面初始化时加载相应的 HTML 、 JavaScript 、 CSS 。一旦页面加载完成， SPA 不会因为用户的操作而进行页面(HTML)的重新加载或跳转，而是利用 JavaScript 动态的变换 HTML（采用的是 div 切换显示和隐藏），从而实现UI与用户的交互。在 SPA 应用中，应用加载之后就不会再有整页刷新。相反，展示逻辑预先加载，并有赖于内容Region（区域）中的视图切换来展示内容。

  **简单来说，SPA就是一个页面中，通过js来控制展示的内容，而不需要重新加载新的HTML。**

- 路由表

  路由与组件的对应关系表，每个路由对应一个或多个组件，在切换路由时根据路由表来查找匹配的组件后进行渲染。
  
- router-view

  路由的占位符，会显示当前路由对应的组件，且可以嵌套。

  - 如下图所示，假如我们把router-view放在**section**中，那么切换路由时，**section**中的组件会根据路由表进行切换，且**header**与**sidebar**不变。

    ![](..\assets\img\VueRouter\vueRouter_01.png)

  - 实例

    1. Home.vue代码&效果图：

       ```html
       <template>
         <div id="app">
           <div class="header">
             <h1>header</h1>
           </div>
           <div class="sidebar">
             <h1>sidebar</h1>
           </div>
           <div class="section">
             <router-view />
           </div>
         </div>
       </template>
       ```

       <img src="..\assets\img\VueRouter\vueRouter_03.png" alt="vueRouter_03"  />

    2. router.js代码，(此时**路由表constantRoutes**中没有内容，因此router-view无法进行渲染，因为判断不了当前路由应该渲染哪个组件)

       ```js
       import Vue from 'vue'
       import Router from 'vue-router'
       Vue.use(Router)
       
       const constantRoutes = [
         
       ];
       
       const router = new Router({
         mode: 'hash',
         scrollBehavior: () => ({ y: 0 }),
         routes: constantRoutes
       })
       
       export default router
       ```

    3. 为**路由表constantRoutes**添加路由组件映射关系

       ```js
       // 引入组件写法1
       import section01 from '@/views/section_01/index'
       
       // section01组件对应两个路由，即当路由为'/'或'/section01'时都会渲染section01组件
       // 引入组件有两种写法，区别在于写法2只会在访问该路由时加载(只会加载一次)，即路由懒加载。
       const constantRoutes = [
         {
           path: '/',
           component: section01
         },
         {
           path: '/section01',
           component: section01
         },
         {
           path: '/section02',
            // 引入组件写法2
           component: () => import('@/views/section_02/index')
         }
       ];
       ```

    4. 添加了路由表后的效果图

       路由为'/'：

       <img src="..\assets\img\VueRouter\vueRouter_04.png" alt="vueRouter_04" style="zoom: 25%;" />

       路由为'/section01':

       <img src="..\assets\img\VueRouter\vueRouter_05.png" alt="vueRouter_05" style="zoom: 25%;" />

       路由为'/section02':

       <img src="..\assets\img\VueRouter\vueRouter_06.png" alt="vueRouter_06" style="zoom:25%;" />

- router-link

  与\<a>标签功能相同，但**不会重新加载页面**
