---
title: vue3.0 + Ts 入门
tags: vue
date: 2022-12-29 00:00:00
category: 前端
---

#### 1.vue3.0与vue2.0区别
- vue2中执行顺序 beforeCreate=>created=>beforeMount =>mounted=>beforeUpdate =>updated=>beforeDestroy=>destroyed
- vue3中执行顺序 setup=>onBeforeMount=>onMounted=>onBeforeUpdate=>onUpdated=>onBeforeUnmount=>onUnmounted

生命周期对应关系,后者为vue3.0

- beforeCreate->setup
- created -> setup
- beforeMount -> onBeforeMount
- mounted -> onMounted
- beforeUpdate -> onBeforeUpdate
- updated -> onUpdated
- beforeDestroy -> onBeforeUnmount
- destroyed -> onUnmounted
##### vue2和vue3双向数据绑定原理发生了改变
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663904865779-1bb94091-0394-4895-83bf-920d1152c32f.png#clientId=u5bfd2b6e-665d-4&from=paste&height=448&id=u4c589252&name=image.png&originHeight=448&originWidth=763&originalType=binary&ratio=1&rotation=0&showTitle=false&size=56813&status=done&style=none&taskId=ucf4a588c-58bd-45b8-837f-85723980a08&title=&width=763)

vue3.0响应式数据创建
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663910266341-36c3fcbe-e8a8-4ca2-af4d-5bf038cc62ee.png#clientId=u5bfd2b6e-665d-4&from=paste&height=662&id=ud371deb3&name=image.png&originHeight=662&originWidth=771&originalType=binary&ratio=1&rotation=0&showTitle=false&size=28330&status=done&style=none&taskId=u849c2c25-faa6-43aa-a4d6-1a0b2aa0ac6&title=&width=771)
通过引入ref和reactive
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663909949848-0e709893-bfae-4843-a604-1271a73180d8.png#clientId=u5bfd2b6e-665d-4&from=paste&height=519&id=u662b930c&name=image.png&originHeight=519&originWidth=724&originalType=binary&ratio=1&rotation=0&showTitle=false&size=32924&status=done&style=none&taskId=ua45f404f-a483-41fb-9c8b-0122f246a69&title=&width=724)
ref针对单个响应式数据,reative针对的是对象
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663910383029-cc170a65-57ee-4e7d-a8b9-95c141f0b916.png#clientId=u5bfd2b6e-665d-4&from=paste&height=215&id=u9f77da41&name=image.png&originHeight=215&originWidth=868&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9574&status=done&style=none&taskId=ue8965c0c-93a1-47e7-b06b-0213370b361&title=&width=868)

# TS和JS的一些区别
typescript 特点

ts是js的超集，即你可以在ts中使用原生js语法。
ts需要静态编译，它提供了强类型与更多面向对象的内容。
ts最终仍要编译为弱类型的js文件，基于对象的原生的js，再运行。故ts相较java/C#这样天生面向对象语言是有区别和局限的
ts是由微软牵头主导的，主要来自C#
TypeScript和JavaScript的对比：
TypeScript是一个应用程序级的JavaScript开发语言。（这也表示TypeScript比较牛逼，可以开发大型应用，或者说更适合开发大型应用）
TypeScript是JavaScript的超集，可以编译成纯JavaScript。这个和我们CSS离的Less或者Sass是很像的，
我们用更好的代码编写方式来进行编写，最后还是有好生成原生的JavaScript语言。
TypeScript跨浏览器、跨操作系统、跨主机、且开源。由于最后他编译成了JavaScript所以只要能运行JS的地方，都可以运行我们写的程序，设置在node.js里。
TypeScript始于JavaScript，终于JavaScript。遵循JavaScript的语法和语义
TypeScript可以重用JavaScript代码，调用流行的JavaScript库。

# TypeScript 安装
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663910987900-fc836973-f199-4ef1-b1cb-2d9549d49656.png#clientId=u5bfd2b6e-665d-4&from=paste&height=42&id=u387e6a2b&name=image.png&originHeight=42&originWidth=371&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1323&status=done&style=none&taskId=u94eba5fa-a0c7-47ff-bad4-c455b07f9c9&title=&width=371)
创建一个变量,定义str类型,其他类型同理
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911004817-1b528b82-051e-4474-bdeb-5d97bd6075bb.png#clientId=u5bfd2b6e-665d-4&from=paste&height=58&id=ufec52a65&name=image.png&originHeight=58&originWidth=338&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3187&status=done&style=none&taskId=u02fe5b9d-1763-487a-b86a-9e4e942da0d&title=&width=338)
声明一个变量为数组
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911121571-3aca6c3c-e3fe-4beb-bd88-1618d709f12d.png#clientId=u5bfd2b6e-665d-4&from=paste&height=136&id=u3b3b7c5c&name=image.png&originHeight=136&originWidth=358&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4574&status=done&style=none&taskId=uee554cd0-7c3c-4b38-9520-818fdd01195&title=&width=358)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911430412-91ca3555-de27-411f-812c-be15408d764d.png#clientId=u5bfd2b6e-665d-4&from=paste&height=75&id=ua846b125&name=image.png&originHeight=75&originWidth=386&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3208&status=done&style=none&taskId=u54822c4f-0050-43cf-bc85-2d10b83fa8f&title=&width=386)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911438771-8e4eb638-6da9-4f00-baac-39624e9a8ca8.png#clientId=u5bfd2b6e-665d-4&from=paste&height=59&id=u5a1e9e1e&name=image.png&originHeight=59&originWidth=409&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3229&status=done&style=none&taskId=u3237b36b-2094-4196-b460-50e428200f8&title=&width=409)
## Any 类型
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911169557-7faa7de8-f138-4c07-98bb-5abd8c65ddfd.png#clientId=u5bfd2b6e-665d-4&from=paste&height=96&id=ue4bf930d&name=image.png&originHeight=96&originWidth=383&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4080&status=done&style=none&taskId=u06a7fdbc-33e9-48b4-81f4-79b568eff4d&title=&width=383)
# TypeScript 接口
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911210710-63d95bd2-261e-4fbf-b9cd-cf7ba192096d.png#clientId=u5bfd2b6e-665d-4&from=paste&height=72&id=u5f1c5027&name=image.png&originHeight=72&originWidth=264&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1630&status=done&style=none&taskId=uee01c37b-bc2c-4922-94d3-b62a76c2695&title=&width=264)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911231068-aa528b30-1e31-46ea-8b51-b868d418e45f.png#clientId=u5bfd2b6e-665d-4&from=paste&height=480&id=uca3672b5&name=image.png&originHeight=480&originWidth=570&originalType=binary&ratio=1&rotation=0&showTitle=false&size=29221&status=done&style=none&taskId=ua21e4351-c7e3-47f1-8f41-67a74a60434&title=&width=570)
# TypeScript 对象
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911317900-32e85b22-56ab-4900-b80c-6338f9f9e1e2.png#clientId=u5bfd2b6e-665d-4&from=paste&height=164&id=ubcbec9b6&name=image.png&originHeight=164&originWidth=396&originalType=binary&ratio=1&rotation=0&showTitle=false&size=7035&status=done&style=none&taskId=u67dafebd-2f9e-48c0-bbd8-ae90c4da2ef&title=&width=396)
# vue3+typescript创建项目详细步骤
1、新建一个文件夹；

2、vue/cli全局安装：cnpm install -g @vue/cli 或者 npm install -g @vue/cli；
3、创建项目名称：vue create 项目名称；
4、配置文件（根据个人喜好选择）：
如图：
1）、第一个选项是预设配置项，第一个只包含babel，eslint，要选第二个，可以自由配置。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911559248-dcd4417e-7f61-4ca5-bde7-e627fe4438b9.png#clientId=u5bfd2b6e-665d-4&from=paste&id=ub53752cc&name=image.png&originHeight=178&originWidth=315&originalType=url&ratio=1&rotation=0&showTitle=false&size=35578&status=done&style=none&taskId=u45f26637-326f-4562-b060-fe8ca53de4a&title=)

选择第二项，出现如图配置（空格搭配上下键选择）：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911559274-6512b9ee-3f56-4a4f-a1cb-b87b992d1c0a.png#clientId=u5bfd2b6e-665d-4&from=paste&id=uee65d25c&name=image.png&originHeight=300&originWidth=379&originalType=url&ratio=1&rotation=0&showTitle=false&size=89240&status=done&style=none&taskId=uf6d41c82-5046-4aa7-9050-439a58a6eb3&title=)

babel是用来适配一些js的新特性，浏览器还不支持的，但是可以通过其它方式实现它的语法
typescript就是启用typescript支持
PWA这个是渐进式web应用，如果用不上，不需要选
Router是vue-router
css pre-processors是用来支持css预处理器的，less，sass这些
linter是用来做代码检验的，如代码缩进，规范合不合格之类的，都是它来校验
2）、这2个是自动化测试框架，可以不选；测试代码有没有bug，符不符合预期用的

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911559230-50b3372e-902d-4567-beb5-f91cf8426248.png#clientId=u5bfd2b6e-665d-4&from=paste&id=ua94b05b1&name=image.png&originHeight=48&originWidth=188&originalType=url&ratio=1&rotation=0&showTitle=false&size=1608&status=done&style=none&taskId=ua4a93531-7d00-4adf-ac10-77f3f9876c2&title=)

3）、class-style component syntax，像react的风格，是否启用类作为组件模型，这个建议开启。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911559241-e0b0114f-800b-4ca1-98bf-9e59355e22bf.png#clientId=u5bfd2b6e-665d-4&from=paste&id=u4fe58782&name=image.png&originHeight=84&originWidth=422&originalType=url&ratio=1&rotation=0&showTitle=false&size=47054&status=done&style=none&taskId=uc74dfba9-180c-4cc4-ab3f-3672590d6e3&title=)

4）、配置babel对ts，jsx的转义，要开启

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911559362-ff5ffe3a-3621-4b4d-ac6e-1e860eee5d28.png#clientId=u5bfd2b6e-665d-4&from=paste&id=u859e6e6e&name=image.png&originHeight=32&originWidth=506&originalType=url&ratio=1&rotation=0&showTitle=false&size=11155&status=done&style=none&taskId=u3e1b4970-f128-4427-8118-b3593cf01b4&title=)

5）、路由管理模式，Y是HTML5 history，N是hash模式

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911559723-e9957012-7d67-4305-aefb-ca5e755fc5bc.png#clientId=u5bfd2b6e-665d-4&from=paste&id=u4a5131a7&name=image.png&originHeight=37&originWidth=479&originalType=url&ratio=1&rotation=0&showTitle=false&size=11580&status=done&style=none&taskId=uf4b0bbfc-40d7-41f4-bd23-f37a2efeff4&title=)

6）、预处理器，根据自己习惯选择；

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911559967-0378bf0d-9c4e-4144-9607-761984e5a8f8.png#clientId=u5bfd2b6e-665d-4&from=paste&id=u48507214&name=image.png&originHeight=198&originWidth=461&originalType=url&ratio=1&rotation=0&showTitle=false&size=84586&status=done&style=none&taskId=u90939516-03aa-432f-9627-86ed1fbc2e2&title=)

7）、这个是代码风格约束的选择项;

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911560102-5da75acb-52cc-4735-89ef-ed06c31ab5eb.png#clientId=u5bfd2b6e-665d-4&from=paste&id=ua3783cca&name=image.png&originHeight=135&originWidth=547&originalType=url&ratio=1&rotation=0&showTitle=false&size=79429&status=done&style=none&taskId=u0636e733-b09d-42d4-b9b3-e269e22d72f&title=)

8）、对git提交时候的风格校验；第一个是保存的时候，第二个是commit的时候推荐2个都开启

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911560167-01cb141b-a74f-4e70-a7fd-bb071e18085c.png#clientId=u5bfd2b6e-665d-4&from=paste&id=u4aa23197&name=image.png&originHeight=86&originWidth=367&originalType=url&ratio=1&rotation=0&showTitle=false&size=21791&status=done&style=none&taskId=u28a6e631-5baa-40bd-a308-24b8b781914&title=)

9）、置配置文件的管理路径，建议选第一个，每一个单独生成配置文件，第二个都会堆在package.json，

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911560392-324275bb-c592-4e76-b35e-c5e6432ca0ee.png#clientId=u5bfd2b6e-665d-4&from=paste&id=u5658ca60&name=image.png&originHeight=100&originWidth=567&originalType=url&ratio=1&rotation=0&showTitle=false&size=56897&status=done&style=none&taskId=u50fa05cb-21e7-4091-af1a-386b165341f&title=)

10）、把这些配置是否保存为模板，选择N

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911560394-e1a1020c-8863-471b-98fb-9aed7ae5d6b7.png#clientId=u5bfd2b6e-665d-4&from=paste&id=ue4c1a515&name=image.png&originHeight=63&originWidth=373&originalType=url&ratio=1&rotation=0&showTitle=false&size=27037&status=done&style=none&taskId=uf5e42449-fd60-44cd-a898-47731d84764&title=)

11）、成功创建项目；

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1663911560996-1354937e-351a-48b0-805c-c4eed1e6b776.png#clientId=u5bfd2b6e-665d-4&from=paste&id=uc0e4eb30&name=image.png&originHeight=109&originWidth=457&originalType=url&ratio=1&rotation=0&showTitle=false&size=57206&status=done&style=none&taskId=u20e03c0a-0480-40b1-a87f-a0ed18406bb&title=)
