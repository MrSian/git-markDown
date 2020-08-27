### 目的

- 统一团队的 Git 工作流，包括分支使用、tag 规范、issue 等
- 统一团队的 Git Commit 日志标准，便于后续代码 review,版本发布以及日志自动化生成

### git 工作流

- git flow 工作流：

  - master 为主分支，属保护分支，不能直接在此进行代码修改和提交。
  - develop 为日常使用分支。
  - feature 新功能分支，当完成一个功能并测试通过后进行合并到 develop 分支中。
  - hotfix 线上紧急漏洞修复分支，从 master 分支拉取创建，修复完 bug 后合并到 master 和 develop 分支中。

- gitlab flow 工作流（最大原则叫做"上游优先"（upsteam first），即只存在一个主分支 master，它是所有其他分支的"上游"。只有上游分支采纳的代码变化，才能应用到其他分支）：

master->pre-production->production

- master 开发环境分支
- pre-production 预发环境分支
- production 生产环境分支

### git commit 规范

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
复制代码
```

```
占位标签解析：
type:代表某次提交的类型，比如是修复一个bug还是增加一个新的feature。所有的type类型如下：
scope:scope说明commit影响的范围。scope依据项目而定，
		例如在业务项目中可以依据菜单或者功能模块划分，
		如果是组件库开发，则可以依据组件划分。
subject:是commit的简短描述
body:提交代码的详细描述
footer:如果代码的提交是不兼容变更或关闭缺陷，则Footer必需，否则可以省略。

feat[特性]:新增feature
fix[修复]: 修复bug
docs[文档]: 仅仅修改了文档，比如README, CHANGELOG, CONTRIBUTE等等
style[格式]: 仅仅修改了空格、格式缩进、都好等等，不改变代码逻辑
refactor[重构]: 代码重构，没有加新功能或者修复bug
perf[优化]: 优化相关，比如提升性能、体验
test[测试]: 测试用例，包括单元测试、集成测试等
chore[工具]: 改变构建流程、或者增加依赖库、工具等
revert[回滚]: 回滚到上一个版本
复制代码
```

#### 示例：

```
特性:添加头像功能
特性:添加收藏功能
修复:在android机器上传崩溃问题解决
文档:修改README,增加了使用说明
优化:首页图片加载缓慢优化
重构:对头像功能进行封装重构
复制代码
```

### Git 标签打包规范

\*\*Tag 版本号：\*\*Tag 包括 3 位版本，前缀使用 v。比如 v1.2.31。  
Tag 命名规范：  
1.新功能开发使用第 2 位版本号，bug 修复使用第 3 位版本号  
2.首版本号是全新的功能类，功能模块上线才做的调整

\*\*标题格式：**项目名-日期  
**内容格式：**\<分类>---\<内容>  
**\<分类>：\*\*新功能、bug 修复、优化、依赖升级、重构、漏洞\&补丁

#### 示例：

此图片引用自:[我们的 GIT 工作流](https://juejin.im/post/6844903619662200839)

![163f2c20f375472a.jpeg](https://user-gold-cdn.xitu.io/2019/6/16/16b5f2edc4e19a45?imageView2/0/w/1280/h/960/format/png/ignore-error/1)

### Git Commit 格式校验

- 准备 commitlint/cli 用于格式校验
- 准备 husky 用于 git 提交代码时触发校验

1.      全局安装commitlint/cli

```
npm install -g @commitlint/cli @commitlint/config-conventional
复制代码
```

2.在项目根目录创建配置文件 commitlint.config.js，可以使用以下命令创建

```
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
复制代码
```

3.在配置文件中定义提交规范，可使用以下配置：

```
"module.exports = {extends: ['@commitlint/config-conventional']}"

module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', [
      "feat", "fix", "docs", "style", "refactor", "test", "chore", "revert"
    ]],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never']
  }
};
复制代码
```

4.项目添加 husky，进行 git 提交触发校验，安装如下：

```
npm install husky --save-dev
复制代码
```

5.安装完成后在 package.json 中配置如下信息

```
"scripts": {
    "commitmsg": "commitlint -e $GIT_PARAMS",
 },
 "config": {
    "commitizen": {
      "path": "cz-customizable"
    }
 },
复制代码
```

6.经过以上步骤，git commit 的规范校验已经完成。可以进行代码提交了。

```
不规范提交>git commit -m "添加新功能"
提示：
⧗   input: 添加新功能
✖   subject may not be empty [subject-empty]
✖   type may not be empty [type-empty]

规范提交>git commit -m "feat: 添加新功能"
复制代码
```

### 汉化与自定义校验规则

1.当前项目安装 commitlint-config-cz，如下

```
npm install commitlint-config-cz --save-dev
复制代码
```

2.commitlint 校验规则配置添加如下设置：

```
module.exports = {
  extends: [
    'cz'
  ]
};
复制代码
```

3.下载官方配置文件进行修改。[官方配置文件 cz-config-EXAMPLE.js](https://github.com/leonardoanalista/cz-customizable/blob/master/cz-config-EXAMPLE.js)。修改示例如下：

```
'use strict';

module.exports = {

  types: [
    {value: '特性',name: '特性:    一个新的特性'},
    {value: '修复',name: '修复:    修复一个Bug'},
    {value: '文档',name: '文档:    变更的只有文档'},
    {value: '格式',name: '格式:    空格, 分号等格式修复'},
    {value: '重构',name: '重构:    代码重构，注意和特性、修复区分开'},
    {value: '性能',name: '性能:    提升性能'},
    {value: '测试',name: '测试:    添加一个测试'},
    {value: '工具',name: '工具:    开发工具变动(构建、脚手架工具等)'},
    {value: '回滚',name: '回滚:    代码回退'}
  ],

  scopes: [
    {name: '用户模块'},
    {name: '订单模块'},
    {name: '社区模块'},
    {name: '商品模块'}
  ],

  // it needs to match the value for field type. Eg.: 'fix'
  /*
  scopeOverrides: {
    fix: [
      {name: 'merge'},
      {name: 'style'},
      {name: 'e2eTest'},
      {name: 'unitTest'}
    ]
  },
  */
  // override the messages, defaults are as follows
  messages: {
    type: '选择一种你的提交类型:',
    scope: '选择一个scope (可选):',
    // used if allowCustomScopes is true
    customScope: 'Denote the SCOPE of this change:',
    subject: '简要说明:\n',
    body: '详细说明，使用"|"换行(可选)：\n',
    breaking: '非兼容性说明 (可选):\n',
    footer: '关联关闭的issue，例如：#31, #34(可选):\n',
    confirmCommit: '确定提交?'
  },

  allowCustomScopes: true,
  allowBreakingChanges: ['特性', '修复'],

  // limit subject length
  subjectLimit: 100

};
复制代码
```

### 生成 changelog

1.安装 conventional-changelog，可以快速生成提交日志

```
npm install -g conventional-changelog-cli
npm install -g cz-conventional-changelog
复制代码
```

2.项目根目录下添加 `.czrc` 配置文件,文件内容如下

```
{ "path": "cz-conventional-changelog" }
复制代码
```

3.在 package.json 中的 scripts 项增加如下指令

```
"version": "conventional-changelog -p angular -i CHANGELOG.md -s -r 0 && git add CHANGELOG.md"
复制代码
```

4.执行 npm run version 即可在当前目录生成 changelog 日志了。
