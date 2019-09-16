##### 1、[安装mpvue](http://mpvue.com/mpvue/#_2)
``` bash
# 全局安装 vue-cli
$ npm install --global vue-cli

# 创建一个基于 mpvue-quickstart 模板的新项目
$ vue init mpvue/mpvue-quickstart my-project

# 安装依赖
$ cd my-project
$ npm install
# 启动构建
$ npm run dev
# 用微信开发工具打开项目
```
###### 2、[安装微信小程序里的腾讯云](https://cloud.tencent.com/document/product/619/11447)
- 下载腾讯云里的node服务端demo
- 拷贝server文件放到mpvue项目中，并修改`project.config.json`
```
"miniprogramRoot": "./dist/",
"qcloudRoot": "./server/",
```
##### 3、node本地开发环境
- 修改server/config里
```
serverHost: 'localhost',
tunnelServerUrl: 'http://tunnel.ws.qcloud.la',
tunnelSignatureKey: '27fb7d1c161b7ca52d73cce0f1d833f9f5b5ec89',
  // 腾讯云相关配置可以查看云 API 秘钥控制台：https://console.cloud.tencent.com/capi
qcloudAppId: '1257146503',
qcloudSecretId: 'AKIDsfIoBNhEEVefVAv3kPtCKG0nTyKYspxw',
qcloudSecretKey: '20oRt9FB3yppOm0KZigT5W2dAYzQhnkW',
wxMessageToken: 'weixinmsgtoken',
networkTimeout: 30000,
```
##### 4、启动本地数据库
```
// 启动数据库
mysql -uroot -p
// 创建数据库
create database bookmanager;
```
##### 5、初始化node项目
```
// 初始化数据库
node tools/initdb.js
```
##### 6、安装`nodemon`
```
// 自动更新node代码
npm i -g nodemon
```
##### 7、安装获取微信uuid插件
```
npm i wafer2-client-sdk --save
```
##### 8、mysql使用
``` js
show databases;  // 查看数据库
use databaseName; // 使用数据库
show tables; // 查看表
desc tableName; // 查看表结构
select * from tableName; // 查看表数据
drop table tableName; // 删除表
```
##### 9、自动规范`eslint`
``` bash
// 1、 package.json
"lint": "eslint --fix --ext .js,.vue src"
// 2、npm run lint
```
##### 10、为表添加字段
```
alter table books add column count int default 0;
```
##### 11、mpvue获取query传值
``` js
this.bookid = this.$root.$mp.query.id
```
