Git 的一些实用基本操作, 这里就不作复述了. 主要和大家分享如何更优雅的使用 Git

## 写好 commit message

Git 每次提交代码, 都要写 Commit message, 否则提交不了. 我们不仅要写 commit message, 而且要写得清晰明了, 以说明本次提交的目的.

写好 commit message 好处有很多, 如:

- 统一项目组的 Git commit 日志风格
- 便于日后 code review
- 便于收集 Change log

## Commit 提交规范

目前业界比较流行的 commit 规范, 主要包括三部分:

- header
- body
- footer 完整格式如下:

```
<type>(<scope>): <subject>
<blank line>

<body>
<blank line>
<footer>
复制代码
```

### type

提交的 commit 类型, 包括以下几种:

- feat: 新功能
- fix: 修复问题
- docs: 修改文档
- style: 修改代码格式, 不影响代码逻辑
- refactor: 重构代码, 理论上不影响现有功能
- perf: 提升性能
- test: 增加修改测试用例
- chore: 修改工具相关\(包括但不限于文档, 代码生成等\)

### scope

修改文档的范围, 比如: 视图层, 控制层, docs, config, plugin

### subject

subject 是 commit 目的的一个简短描述, 一般不超过 50 个字符

### body

补充说明 subject 的, 可以分成多行, 适当增加原因, 目的等相关因素, 也可以不写.

### footer

- 当有非兼容修改时必须在这里描述清楚
- 关闭 issue 或是链接到相关文档, 如: Closes #111923, Closes #93201

## validate-commit-msg ghooks

validate-commit-msg [Github](https://github.com/conventional-changelog-archived-repos/validate-commit-msg)于检查项目的 Commit message 是否符合格式。 ghooks [Github](https://github.com/ghooks-org/ghooks) Simple git hooks

### 安装

```
npm install --save-dev validate-commit-msg`
npm install ghooks --save-dev
复制代码
```

### 配置

在项目的根目录下, 新建一个名为`.vcmrc`的文件, 并输入以下内容:

```
{
  "types": ["feat", "fix", "docs", "style", "refactor", "perf", "test", "build", "ci", "chore", "revert"],
  "scope": {
    "required": false,
    "allowed": ["*"],
    "validate": false,
    "multiple": false
  },
  "warnOnFail": false,
  "maxSubjectLength": 100,
  "subjectPattern": ".+",
  "subjectPatternErrorMsg": "subject does not match subject pattern!",
  "helpMessage": "",
  "autoFix": false
}
复制代码
```

然后, 在`package.json`中添加如下配置

```
{
  …
  "config": {
    "ghooks": {
      "pre-commit": "gulp lint",
      "commit-msg": "validate-commit-msg",
      "pre-push": "make test",
      "post-merge": "npm install",
      "post-rewrite": "npm install",
      …
    }
  }
  …
}
复制代码
```

可以根据项目的需要来添加, 例如:

```
  "config": {
    "ghooks": {
      "commit-msg": "validate-commit-msg"
    }
  }
复制代码
```

### 验证

每次 git commit 的时候，这个脚本就会自动检查 Commit message 是否合格。如果不合格，就会报错。

```
$ git add -A
$ git commit -m "edit markdown" INVALID COMMIT MSG: does not match "<type>(<scope>): <subject>" ! was: edit markdown
复制代码
```

## 使用 commitizen

工具 commitizen 可以帮忙我们写出规范的 commit message. [Github](https://github.com/commitizen/cz-cli)

### 全局安装

```
> npm install -g commitizen
复制代码
```

#### 初始化及使用

···

> commitizen init cz-conventional-changelog \--save-dev \--save-exact

````

以上命令将执行以下3个动作:
* 安装cz-conventional-changelog依赖包
* 保存依赖包信息到package.json的dependencies七devDependencies中
* 添加config.commitizen字段到package.json中, 可能的配置如下:
``` json
  "config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
  }
复制代码
````

::: tip 如果以前使用过 cz-conventional-changelog, 在末尾添加`--force`参数, 强制更新 package.json 中的配置. 更多信息可以通过命令`commitizen help`获得 :::

接下来我们就可以愉快的使用`git cz`命令来代替`git commit`命令了.

```
E:\github\coding_docs>git cz
cz-cli@3.1.1, cz-conventional-changelog@2.1.0

Line 1 will be cropped at 100 characters. All other lines will be wrapped after 100 characters.

? Select the type of change that you're committing: feat:     A new feature
? What is the scope of this change (e.g. component or file name)? (press enter to skip)
 gitCommit.md, package.json
? Write a short, imperative tense description of the change:
 add a new md file=> how to use git well
? Provide a longer description of the change: (press enter to skip)

? Are there any breaking changes? Yes
? Describe the breaking changes:
 how to use git well
? Does this change affect any open issues? No
复制代码
```

### 本地安装

> 本地安装可以确保项目的所有开发者都能执行相同的 commitizen 版本.

```
npm install commitizen --save-dev
npm commitizen init cz-conventional-changelog --save-dev --save-exact
复制代码
```

或者

```
yarn add commitizen --dev
yarn commitizen init cz-conventional-changelog --dev --save-exact
复制代码
```

### 添加配置\&使用

手动添加如下配置至 package.json 文件中:

```
  "script": {
    "commit": "git-cz"
  }
复制代码
```

当需要 commit 文件时, 执行`npm run commit`即可.

## 使用 gitmoji

gitmoji 和 commitizen 的作用都是帮助我们写出规范的 commit message，不过 gitmoji 有更好玩的 moji 表情。（ 用 moji 来表示 type ） [Github](https://github.com/carloscuesta/gitmoji-cli)

### 安装

```
npm install -g gitmoji-cli
复制代码
```

### 使用

```
gitmoji -c
复制代码
```

挑选个符合场景的 moji 提交本次更改:

![](https://user-gold-cdn.xitu.io/2019/11/30/16ebcc8e52393e5d?imageslim)

## 使用 Git hooks

与其他版本控制系统一样，当某些重要事件发生时，Git 可以调用自定义脚本，Git 有很多钩子可以用来调用脚本自定义 Git。在 .git \-> hooks 目录下可以看到示例。 例如：pre-commit 就是在代码提交之前做些事情。如果你打开了 hooks 目录里面的 \*.sample 文件，你可以看见里面写的 shell 脚本。但是我想用 Js 写 hooks 咋办？husky、pre-commit 就能满足你。

现在我们想实现一个提交代码时使用 Eslint 进行代码检查的功能

### pre-commit [GitHub](https://github.com/observing/pre-commit)

#### 安装

```
npm install --save-dev pre-commit
复制代码
```

#### 配置

在 package.json 中配置 pre-commit

```
"script": {
  "lint": "eslint [options] [file|dir|glob|*]"
},
"pre-commit": [
  "lint"
]
复制代码
```

#### 提交代码:

```
> git commit -m "test:Keep calm and commit"
复制代码
```

### husky [Github](https://github.com/typicode/husky)

#### 安装

```
> npm install husky@next --save-dev
复制代码
```

#### 配置

和 pre-commit 一样，还是在 package.json 中配置。但是处理 pre-commit 钩子它还可以做的更多。

```
{
  "scripts": {
    "lint": "eslint [options] [file|dir|glob]*"
  },
  "husky": {
    "hooks": {
      "pre-commit": "npm lint",
      "pre-push": "..."
    }
  }
}
复制代码
```

## 生成 change log

如果你的所有 Commit 都符合 Angular 格式，那么发布新版本时， Change log 就可以用脚本自动生成（例 1，例 2，例 3）。

生成的文档包括以下三个部分。

- New features
- Bug fixes
- Breaking changes.

每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。当然，生成的文档允许手动修改，所以发布前，你还可以添加其他内容。 conventional-changelog 就是生成 Change log 的工具，运行下面的命令即可。

```
$ npm install -g conventional-changelog
$ cd my-project
$ conventional-changelog -p angular -i CHANGELOG.md -w
复制代码
```

上面命令不会覆盖以前的 Change log，只会在 CHANGELOG.md 的头部加上自从上次发布以来的变动。

如果你想生成所有发布的 Change log，要改为运行下面的命令。

```
$ conventional-changelog -p angular -i CHANGELOG.md -w -r 0
复制代码
```

为了方便使用，可以将其写入 package.json 的 scripts 字段。

```
{
  "scripts": {
   "changelog": "conventional-changelog -p angular -i CHANGELOG.md -w -r 0"
}}
复制代码
```

以后，直接运行下面的命令即可。

```
$ npm run changelog
复制代码
```

<!---->
