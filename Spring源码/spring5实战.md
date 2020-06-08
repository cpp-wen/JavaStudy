## 1.2 初始化spring引用

sts（spring tools start ）和idea 个人倾向于直接使用idea 

mvnw：maven包装包的脚本 没有安装maven也可以直接构建项目

properties：指定配置属性的地方

jar打包是为了配合云部署 传统使用war包打包

@spring boot Applicaiton 是一个组合注解 包括 

- springBootConfiguration 申明为配置类
- enableAutoConfiguraion 自动配置
- componentscan 组件扫描

@Runwith() junit 注解

## 1.3编写spring应用 

spring boot习惯将所有它需要的东西放在一起，没有必要将其部署到某种应用服务器中

dev tools

- 应用自动重启 监控代码变化
- 应用程序 被加载两个类加载器中  一个监控库文件 一个监控src文件
- 禁用模板缓存（开发环境作用较大）配合liveload 插件可以快速刷新



web库函数引入