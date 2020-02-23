# vue-live2d-plugin

vue 看板娘

## 一、功能介绍
可以快速安装在vue项目中，实现板娘的显示和交互
此为学习项目
以https://github.com/evgo2017/vue-live2d 为对象学习

## 二、 项目使用

```
npm install vue-live2d-plugin

// 组件中引入：
import live2d from 'vue-live2d-plugin'
import 'vue-live2d/dist/vue-live2d.css'

// 目前可配置项
<live2d :apiPath="" :model="" size="">
```


## 三、配置参数

| 配置项   | 类型   | 含义                           | 默认                          |
| -------- | ------ | ------------------------------ | ----------------------------- |
| apiPath  | String | 更换模型的请求地址             | https://live2d.fghrsh.net/api |
| model    | Array  | 默认显示的模型，[编码，衣服号] | () => [4, 77]                 |
| size     | Array  | 默认显示模型大小，[长，宽]   |  () => [300, 300]                 |

##　四、参考资料
https://github.com/evgo2017/vue-live2d

