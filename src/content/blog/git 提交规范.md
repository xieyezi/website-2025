---
title: git代码提交规范
---

# git 代码提交规范

平时自己写代码的时候，每当需要提交代码到 GitHub 的时候，我一般的操作是直接`git commit 'xxxx'`，然后直接就推送了，后来项目变得稍微有点大了，还有一些其他小伙伴加入了项目的开发，他们给我项目仓库提交代码，长时间之后去看他们的提交，甚至都不知道他们干了啥，这就很尴尬了。所以 git 的提交必须要规范才行，尤其是在多人协作的项目中，如果 Git 的提交说明精准，在后期协作以及后期 review 会变得有据可查，项目的开发可以根据规范的提交说明快速生成开发日志，从而方便开发者或用户追踪项目的开发信息和功能特性。 <!-- more -->

## commit message

我们每一次提交必定是有特殊的行为，或是开发新功能、或是修复 bug 等等。我们针对不同的操作有如下的分类：

- type: commit 的类型
- feat: 开发新的功能
- fix: 修复 bug
- refactor: 代码重构
- docs: 文档修改
- style: 代码格式修改, 注意不是 css 修改
- test: 测试用例修改
- perf: 改善性能
- build: 变更项目构建或外部依赖（例如 scopes: webpack、gulp、npm 等）
- chore: 其他修改, 比如构建流程, 依赖管理.
- revert: 代码回退

而 commit 的格式也有标准格式:

- scope: commit 影响的范围, 比如: route, component, utils, build...
- subject: commit 的概述
- body: commit 具体修改内容
- footer: 一些备注, 通常是 BREAKING CHANGE 或修复的 bug 的链接.

## AngularJS 提交规范

Angular 的提交规范是目前业界内认可度和普及度最高的，我们采用`commitizen`和`cz-customizable`来实现规范提交。这两个工具可以根据你的选择自动生成提交信息，然后愉快的提交。
安装`commitizen`和`cz-customizable`:

```js
 npm install -D commitizen
 npm install -D cz-customizable
```

在`package.json`中配置 config.commitizen 字段，配置 cz 工具的适配器路径:

```json
  "config": {
   "commitizen": {
     "path": "node_modules/cz-customizable"
   }
 }
```

在项目根目录新建`.cz-config.js`:

```js
   'use strict'
   module.exports = {
   types: [
       {
       value: 'feat✨'
       name: '✨  feat:     A new feature'
       },
       {
       value: 'fix🐞',
       name: '🐞  fix:      A bug fix'
       },
       {
       value: 'refactor🛠',
       name:
           '🛠  refactor: A code change that neither fixes a bug nor adds a feature'
       },
       {
       value: 'docs📚',
       name: '📚  docs:     Documentation only changes'
       },
       {
       value: 'test🏁',
       name: '🏁  test:     Add missing tests or correcting existing tests'
       },
       {
       value: 'chore🗯',
       name:
           "🗯  chore:    Changes that don't modify src or test files. Such as updating build tasks, package manager"
       },
       {
       value: 'style💅',
       name:
           '💅  style:    Code Style, Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)'
       },
       {
       value: 'revert⏪',
       name: '⏪  revert:   Revert to a commit'
       }
   ],

   scopes: [],

   allowCustomScopes: true,
   allowBreakingChanges: ['feat', 'fix']
   }
```

对应的中文版本：

```js
module.exports = {
  types: [
    { value: "feat✨", name: "特性: 一个新的特性" },
    { value: "fix🐞", name: "修复: 修复一个Bug" },
    { value: "docs📚", name: "文档: 变更的只有文档" },
    { value: "style💅", name: "格式: 空格, 分号等格式修复" },
    { value: "refactor🛠", name: "重构: 代码重构，注意和特性、修复区分开" },
    { value: "perf🐎", name: "性能: 提升性能" },
    { value: "test🏁", name: "测试: 添加一个测试" },
    { value: "revert⏪", name: "回滚: 代码回退" },
    { value: "chore🗯", name: "工具:开发工具变动(构建、脚手架工具等)" }
  ],
  messages: {
    type: "选择一种你的提交类型:",
    customScope: "请输入修改范围(可选):",
    subject: "短说明:",
    body: '长说明，使用"|"换行(可选)：',
    footer: "关联关闭的issue，例如：#31, #34(可选):",
    confirmCommit: "确定提交说明?"
  },
  allowCustomScopes: true,
  allowBreakingChanges: ["特性", "修复"],
  subjectLimit: 100
}
```

接下来我们就可以使用 `git cz`来进行提交。我们再优化一下，我们在`script`配置一下脚本命令:

```json
  "scripts": {
   "commit": "git-cz"
  }
```

至此我们已经可以愉快的使用`npm run commit`进行提交。

## 继续优化

假设现在有两个程序猿，程序猿甲某一天开发了一个新的功能，然后愉快利用如上的步骤提交了，过了几天，程序猿乙在他的基础上修改了代码，然后又进行了提交，又过了几天，程序猿乙因为跟产品经理吵了一架，愤然离去。接着这个功能模块只能由程序猿甲来进行迭代维护。于是在一个美丽的周一早晨，程序猿甲打开 vscode，拉取了项目代码，本想愉快的进行开发，但是他发现他原来的代码，被改得面目全非:“我这里原来没有分号啊...”、“这里原来都是双引号啊...”。他陷入了对人生的思考。通过上面的简短故事，我们了解到在进行团队协作开发的时候，由于每个人的代码都不相同，一千个哈姆雷特就出来了，代码变得越来越难以维护。所以我们需要一些“规则”来约束团队成员，达到一致的风格。
下面就是一套“降龙十八掌”:

## eslint

第一式: `eslint` : `npm install -D eslint`  
 `eslint` : 提供编码规范，提供自动检验代码的程序，并打印检验结果：告诉你哪一个文件哪一行代码不符合哪一条编码规范，方便你去修改代码，可以通过配置文件来加以配置。比如:

```js
  //.eslintrc.js
   {
   "extends": ["airbnb", "plugin:prettier/recommended"], // eslint扩展规则
   "parserOptions": {
       "ecmaVersion": 7,
       "sourceType": "module",
       "ecmaFeatures": {
       "jsx": true
       }
   },
   "parser": "babel-eslint",// 解决ES6 improt会报错
   "env": { // eg如果不配置browser，window就会被eslint报undefined的错
       "es6": true,
       "browser": true,
       "node": true
   },
   "plugins": ["react", "jsx-a11y", "import"],
   "rules": {
       "class-methods-use-this": 0,
       "import/no-named-as-default": 0,
       "react/jsx-filename-extension": [
       "error",
       {
           "extensions": [".js", ".jsx"]
       }
       ]
   }
}
```

在 eslint 里面，你可以约束文件的行数、单行代码的长度，甚至是函数的复杂度等等。

## prettier

第二式:`prettier` : `npm install -D prettier`  
 `prettier` : 代码格式化。
利用`prettier`可以进行统一的代码格式化。从此再也没有要不要分号的纷争了。例如:

```js
//.prettierrc
    {
    "printWidth": 120, // 一行最大多少字符
    "tabWidth": 2, // tab占用的字符数
    "useTabs": false, // 是否使用tab代替空格
    "semi": true, // 是否每句后都加分号
    "singleQuote": true, // 是否使用单引号
    "jsxSingleQuote": false, // jsx是否使用单引号
    "trailingComma": "all", // 数组尾逗号。
    "bracketSpacing": false, // {foo: xx}还是{ foo: xx }
    "arrowParens": "always" //箭头头函数参数是否使用（）
    }
```

那有了如上两个掌法，我们已经可以很好的实现代码格式统一，但是又衍生了一个新的问题:  
 开发的时候，什么时候进行代码的规则校验和格式化统一呢？相信很多小伙伴想到了解决方法:在进行`commit`的时候进行校验和格式化。

此时就用到了第三式:`husky`  
 `husky` : [husky](https://github.com/typicode/husky)是一个 npm 包，安装后，可以很方便的在 package.json 配置 git hook 脚本。
我们在这里要用到一个主要的钩子:`pre-commit`,这个钩子会在你`commit`的时候执行你想要的操作。例如:

```json
  "husky": {
   "hooks": {
     "pre-commit": "npm run lint"
   }
```

以上代码，当你在后续的每一次`git commit` 之前，都会执行一次对应的 hook 脚本 npm run lint 。
所以这个时候，我们把`eslint`和`prettier`的操作都放在`pre-commit`钩子里面就可以了:

```json
  "husky": {
   "hooks": {
     "pre-commit": "lint-staged"
   }
 },
 "lint-staged": {
   "*.{json,css,md}": [
     "npm run format",
     "git add"
   ],
   "*.scss": [
     "npm run lint:scss",
     "npm run format",
     "git add"
   ],
   "*.{js,vue,ts,tsx}": [
     "npm run lint",
     "npm run format",
     "git add"
   ]
 }
```

如上，在每一次提交之前，我们都进行了 `eslint` 的代码规则校验和 `prettier` 的代码格式化。
但是现在有一个问题还没解决，待我细细道来。现在我们再到我们项目的根目录下，我们再次利用`git add .` 和 `git commit -m xxx` 来提交，结果发现提交仍然通过了，那我们现在既可以用`npm run commit`来提交，也可以用原来的命令来提交，那我们原来搞的这一套不就是白费了吗？我的意思是我们应该对`commit`进行校验，如果校验不能通过，那我们就不允许他`commit`。那我们继续往下操作吧。

## commitlint 和 commitlint-config-cz

安装`commitlint`和`commitlint-config-cz`:

```js
  npm install -D @commitlint/cli
  npm install -D commitlint-config-cz
```

在项目的根目录下新建 `commitlint.config.js`:

```js
module.exports = {
  extends: ["cz"],
  rules: {
    "type-empty": [2, "never"], //不允许提交的类型为空
    "subject-empty": [2, "never"] //不允许提交的内容为空
  }
}
```

```json
"husky": {
   "hooks": {
     "commit-msg": "commitlint -E HUSKY_GIT_PARAMS", // 在husky的配置里面加入这一句，意思为了在提交前进行校验
     "pre-commit": "lint-staged"
   }
 }
```

然后你再去用原来的`git comiit -m xxx`就会发现提交不通过了，我们达到了校验的目的。至此我们就完成前端提交代码的规范啦～

## 总结

我们先来看看提交的流程图:

![未命名文件.png](https://user-gold-cdn.xitu.io/2019/12/8/16ee4a036735c758?w=1086&h=594&f=png&s=25699)

通过`commitizen`、`cz-customizable`、`eslint`、`prettier`、`husky`、`commitlint`我们完整的约束了提交的流程，保证了代码的统一，提高了代码的健壮性和可维护性。为后期的工作节省了很多时间，又可以省出很多时间去勾搭小姐姐了 😏
