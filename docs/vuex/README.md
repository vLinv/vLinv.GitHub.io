### vuex的使用

- 使用
  - 引入Vuex
  - 通过new Vuex.Store(options)生成实例store
    - 如果不需要module，则直接在options中写入state、mutations、actions、getters
    - 如果需要module，则使用modules对象，对象中包含所有module
  - 将store实例挂载到vue实例上

- vuex结构
  - state：需要存储的数据
  - mutations：对数据进行的基本操作（如果直接修改state中的数据会导致无法记录且在异步操作中会出错）
  - actions：对数据的操作，可以是异步
  - getters：获取vuex中保存的数据
- 操作
  - state：通过键值对保存
  - mutations：mutations中的方法的第一个参数为state，通过state.xxx修改对应值
  - actions：actions中的方法的第一个参数为包含commit与state的对象，通过commit(mutations方法, 新值)修改
  - getters：通过vuex提供的mapGetters可以获取到需要的数据(mapGetters([多个state名称，需要在vuex实例中配置]))
- 外部操作
  - 外部想要修改state的值必须通过actions来执行，使用方式为：dispatch(actions方法名)
- module
  - 当vuex中数据过多时，需要对其进行分类，此时可以使用module
  - 在store文件夹下创建modules文件夹并创建多个module，每个module都会导出一个包含state、mutations、actions、namespaced（是否开启命名空间，防止冲突）的对象，并在生成vuex实例时合并



### vuex与storage区别

- vuex存储在内存中，当浏览器页面刷新后数据会丢失
- storage存储在本地，刷新页面无影响
  - localStorage为永久性存储，需要手动清除
  - sessionStorage当浏览器窗口页面关闭时清除
- vuex为响应式



### vuex与storage应用场景

- 对安全性有要求的数据应当存储在vuex中，例如路由、按钮权限等
- 需要响应式的数据应当存储在vuex中，例如用户信息（当用户修改其信息时，如果把用户信息存在storage中，则需要刷新浏览器才会更新。）或换肤等。



### 刷新页面vuex数据不丢失

- 当需要用到响应式存储时，storage方式不合适，但vuex会因为页面刷新而被清除数据，此时可以结合两者来实现刷新页面也不会导致vuex数据被清除
- 只需要在state中判断storage中是否保存了该数据，如果是则优先从storage中获取，同时在mutations中将数据更新到storage中以确保同步

