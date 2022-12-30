# Angular Cliz中替换包管理器 npm - yarn

## 全局替换Angualr CLI的包管理工具

执行以下的命令

```bash
ng config -g cli.packageManager yarn
```

后续使用ng new, ng init等命令时，会使用yarn作为包管理工具



## 在单个项目中使用Yarn

在用new命令构建项目时，添加–skip-install选项，避免在项目构建之初就使用npm安装依赖包

```bash
ng new --skip-install hello-world-app
```

建立angular项目的时候不安装包，后面用yarn安装