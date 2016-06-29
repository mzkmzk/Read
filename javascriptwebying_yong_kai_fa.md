# JavaScriptWeb应用开发

# 1. 构建过程

你可以逐渐改进代码基,重构你接触到的代码,为重构后的功能编写测试,把过时的代码包装在接口里,以后再重构.--这样做能不断提升项目中代码的平均质量

# 2. 编写构建任务,指定流程

介绍了下Grunt下使用less,jslint,babel编译等

# 3. 精通环境配置和开发流程

一般有三种构建

1. 本地
2. 测试环境
3. 正式站点

LiveReload: 自动刷新浏览器

# 4. 发布、部署和监控

优化图片使用`grunt-contrib-imagemin`,能实现隔行扫描图像

使用更改日志`grunt-conventional-changelog`

提交git时,使用`fix:`,`feat: `,`BREAKING`,为commit的开头,该插件能自动生成changelog

最好使用Travis托管的CI,获取github上的标识

## 4.5 监控和诊断

### 4.5.1 日志和通知

使用winston处理日志

### 4.5.2调试弄得

node-inspector

### 4.5.3分析性能

node: nodetime-register

