# create-nuxt-app 源码初探

# 目录结构

主要执行文件结构

```bash
.
├── cli.js
├── prompts.js
├── saofile.js
├── template
```

### 文件说明

- cli.js: 主要提供命令行参数处理
- prompts.js: 交互回答问题的配置 基本就是一个json文件
- saofile.js: 针对交互回答, 进行文件的add modify move等操作
- template: 各框架所需的模板代码 

templete 目录结构

```bash
.
├── template
│   ├── frameworks
│   │   ├── adonis
│   │   ├── ant-design-vue
│   │   ├── ava
│   │   ├── buefy
│   │   ├── element-ui
│   │   ├── express
│   │   ├── fastify
│   │   ├── feathers
│   │   ├── framevuerk
│   │   ├── hapi
│   │   ├── iview
│   │   ├── jest
│   │   ├── koa
│   │   ├── micro
│   │   └── vuetify
│   └── nuxt
│       ├── assets
│       ├── components
│       ├── layouts
│       ├── middleware
│       ├── pages
│       ├── plugins
│       ├── static
│       └── store
```

# 原理

- cli接收各种参数
- sao 进行模板文件处理
 - 设置问题与答案
 - 针对用户的回答, 在template文件夹存放好各种回答需要的框架代码, 进行不同的action add , move, modify等


# 依赖分析

- cac: 命令行与JS交互框架 https://www.npmjs.com/package/cac
- sao: 互动回答式
- chalk: 多种颜色 输出在控制台
- validate-npm-package-name: 验证包名是否正确

# 单元测试 

- 遍历prompts问题的所有选项, 然后sao提供了sao.mock方法 不实际生成文件, 会给出类似的结构数据
- 结构数据生成snasphots

是典型的白盒功能测试


