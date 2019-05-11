

技术栈

vue2 + vuex + vue-router + webpack + ES6 + axios + sass + flex + svg + node + mongoDB

关于接口数据

接口项目地址 mall-api 使用的是 node + mongodb





关于部署

使用 nginx 做反向代理，解决跨域问题.

先在服务器安装 Nginx.
上传nodejs代码.
把上传通过各种方式(命令行或者ftp)上传到服务器 进入目录下安装node依赖(与本地开发并无区别)
使用pm2启动 此时会打开一个端口 假设 3333
此时node已运行在服务端
将打包后的前端静态文件dist目录上传到服务器
配置反向代理
找到Nginx配置文件，如果不知道在哪,可以上百度搜一下有命令提示
一般默认是在 /usr/local/nginx/conf/nginx.conf
修改配置 找到 server 如图
nginx配置

重启 /usr/local/nginx/sbin/nginx -s reload
项目运行



cd vue-mall

cnpm i

npm run dev

// 如果运行出现代理错误 请确保 config 文件下 index.js proxyTable代理正确

// 直接运行代理应为
http://mall.yucccc.com

// 通过运行node-api请求本地api代理应为
http://127.0.0.1:3333
说明





为了方便演示，这里提供了两个账号.当然可能存在多个用户同时操作一个账号.

 账号: admin  密码: admin
 账号: admin1 密码: 123
目标功能

 登陆、登出功能
 注册
 加入、删除、修改购物
 新增、修改、删除收货地址
 下单功能
 支付功能 -- 由于没权限申请到蚂蚁金服支付宝开发接口,因此只是模拟支付.
 商品详情
 个人中心
 订单列表
 更换头像 -- 头像上传到七牛云,由于免费的七牛云空间有限,希望各位大佬不要搞我..



项目布局

.
├── build                                       // webpack配置文件
├── config                                      // 项目打包路径
├── dist                                        // 打包文件
├── src                                         // 源码目录
│   ├── api                                     // 请求接口
│   ├── common                                  // 公共组件
│   ├── components                              // 组件
│   ├── page                                    // 页面
│   │   └── Cart                                // 购物车
│   │   └── Checkout                            // 提交订单
│   │   └── Goods                               // 商品
│   │       ├── goods                           // 商品列表
│   │       ├── goodsDetails                    // 商品详情
│   │   └── Home                                // 主页
│   │   └── Login                               // 登陆
│   │   └── Order                               // 订单
│   │       ├── order                           // 商品列表
│   │       ├── payment                         // 提交订单
│   │       ├── paysuccess                      // 提交成功
│   │   └── User                                // 个人中心
│   │       ├── children
│   │       │   ├── addressList                 // 地址列表
│   │       │   ├── information                 // 个人信息
│   │       │   └── order                       // 订单列表
│   │   └── index.vue                           // 主页
│   ├── store                                   // vuex的状态管理
│   │   ├── action.js                           // 配置actions
│   │   ├── index.js                            // 引用vuex，创建store
│   │   ├── modules                             // store模块
│   │   ├── mutation-types.js                   // 定义常量muations名
│   │   └── mutations.js                        // 配置mutations
│   ├── App.vue                                 // 页面入口文件
│   ├── main.js                                 // 程序入口文件，加载各种公共组件
├── favicon.ico                                 // 图标
├── index.html                                  // 入口html文件


// 表模型
const produtSchema = new mongoose.Schema({
    'productId': String,
    'salePrice': Number,
    'productName': String,
    'productImageSmall': Array,
    'productImageBig': String,
    'stock': Number,
    'sub_title': String,
    'limit_num': String,
    'productMsg': Object
})


const mongoose = require('mongoose')
const userSchema = new mongoose.Schema({
    "userId": String,
    "name": String,
    "avatar": String,
    "userName": String,
    "userPwd": String,
    "orderList": Array,
    "cartList": [
        {
            "productId": String,
            "productImg": String,
            "productName": String,
            "checked": String,
            "productNum": Number,
            "productPrice": Number
        }
    ],
    'addressList': [
        {
            "addressId": Number,
            "userName": String,
            "streetName": String,
            "tel": Number,
            "isDefault": Boolean
        }
    ]
})
module.exports = mongoose.model('User', userSchema)


文件上传方式的前端处理
var reader = new FileReader()
        reader.readAsDataURL(file)
        reader.onload = (e) => {
          this.option.img = e.target.result
        }


