## 前言

**网上五花八门的配置过旧，内容不详**。本文力争做到以下几点：

- eslint核心概念及规则详解
- prettier常见配置详解
- vs code配置普通node/js项目规则校验及格式化
- vs code配置vue项目规则校验及格式化
- 常见配置问题分析及解决

配置基于**vscode 1.52.1版本**。

## ESLint

### 是什么

它是一个**插件化**的JavaScript**代码检测工具**；它的目标是**保证代码的一致性和避免错误**。

> 代码检查是一种静态的分析，常用于查找有问题的代码，并且不依赖于具体的编码风格。对大多数编程语言来说都会有代码检查，一般来说编译程序会内置检查工具。
>
> JavaScript 是一个动态的弱类型语言，在开发中比较容易出错。因为没有编译程序，为了寻找 JavaScript 代码错误通常需要在执行过程中不断调试。像 ESLint 这样的可以让程序员在编码的过程中发现问题而不是在执行的过程中。
>
> ESLint 的初衷是为了让程序员可以创建自己的检测规则。ESLint 的所有规则都被设计成可插入的。ESLint 的内置规则与其他的插件并没有什么区别。为了便于使用，ESLint 内置了一些规则，当然，你可以在使用过程中自定义规则。
>
> **ESLint 并不推荐任何编码风格。**

具体详细介绍可以查看：[关于ESLint](https://eslint.org/docs/about/)。

ESLint主要解决以下问题：

1. 找出语法错误，避免低级bug；

2. 提示删除多余的代码；

3. 确保代码遵守最佳实践；

4. 统一团队代码风格。

### 怎么用

具体安装和使用，不针对任何编辑器以及IDE，仅介绍ESLint本身的规则和用法，后面章节会介绍如何集成到vs code中使用。

#### 安装

1. 创建一个新项目，如果在已有项目中使用可跳过该步骤

```bash
mkdir eslint-learn
cd eslint-learn
npm init -y
```

2. 在项目中安装eslint

```bash
npm install eslint --save-dev
```

3. 初始化eslint的配置文件

```bash
npx eslint --init
```

初始化过程中的选项如下：

![](https://raw.githubusercontent.com/swift-fs/imgs/master/docs/20210225174137.png)

一步一步完成后，会在项目的根目录下生成**.eslintrc.js文件**：

```javascript
module.exports = {
    "env": {
        "browser": true,
        "es6": true,
        "node": true
    },
    "extends": "eslint:recommended",
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly"
    },
    "parserOptions": {
        "ecmaVersion": 2018,
        "sourceType": "module"
    },
    "rules": {
    }
};
```

4. 新建一个`index.js`文件，写入如下代码：

```javascript
// 一共两个问题：变量声明了未被使用；语句多出一个分号
var name = "eslint-learn";;
```

然后，命令行通过`npx eslint`来检测`index.js`文件：

![](https://raw.githubusercontent.com/swift-fs/imgs/master/docs/20210225181754.png)

可以看到，通过eslint检测后，问题出现的位置以及原因被指出，然后可以通过`npx eslint --fix`尝试进行错误和警告修复：

![](https://raw.githubusercontent.com/swift-fs/imgs/master/docs/20210225181904.png)

eslint其他可用命令选项参考：[eslint命令行用法](https://eslint.org/docs/user-guide/command-line-interface)

运行后，多余分号已被去除，但是依旧存在一个错误：**部分错误eslint并无法自动修复**，详情参考：[eslint规则列表](https://eslint.org/docs/rules/)

### 配置参数解释

- `parser`，指定eslint使用的语法解析器，默认使用的`espree`。可选的解析器有：`esprima`，`@babel/eslint-parser`，`@typescript-eslint/parser`，具体可在npm仓库搜索查看使用。
- `parserOptions`，解析器配置参数。默认选项如下：

```javascript
 "parseOptions": {
    // 代码类型：script(默认), module
    "sourceType": "script",
    // es 版本号，默认为 5，也可以是用年份，比如 2015 (同 6)
    "ecamVersion": 6,
    // es 特性配置
    "ecmaFeatures": {
        "globalReturn": true, // 允许在全局作用域下使用 return 语句
        "impliedStrict": true, // 启用全局 strict mode 
        "jsx": true // 启用 JSX
    },
```

- `globals`，声明全局变量。eslint会检测未声明的变量，并报错：

```javascript
{
  "globals": {
  // 声明一个名为age的全局变量
    "age": false // true表示该变量为 writeable，而 false 表示 readonly，也可以直接使用writeable和readonly
  }
}
```

- `env`，引入一个环境中所有的全局变量可用的环境参考：[可用的环境](https://eslint.org/docs/user-guide/configuring/language-options#specifying-environments)。
- `rules`，配置校验规则，可用规则参考：[规则列表](https://eslint.org/docs/rules/)。

> 每一个规则格式为：`规则名:[错误级别,附加选项]`或者`规则名:错误级别`
>
> eslint有三种错误级别：
>
> - "off" 或 0：关闭规则
> - `"warn"` 或 `1` ： 警告，不会导致代码退出
> - "error" 或 2：开启规则，当被触发的时候，程序会退出

简单例子如下：

```javascript
"rules": {
  // 语句后面必须带分号，否则报错
  "semi": ["error", "always"],
  // 字符串必须是双引号，否则报错  
  "quotes": ["error", "double"]
}
```

- `extends`，继承已存在的规则。

```javascript
{
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "eslint-config-standard",
  ]
}
```

> `eslint:` 开头的是 ESLint 官方的扩展，一共有两个：[`eslint:recommended`](https://github.com/eslint/eslint/blob/v6.0.1/conf/eslint-recommended.js) 启用内置推荐规则、[`eslint:all`](https://github.com/eslint/eslint/blob/master/conf/eslint-all.js)启用内置所有规则，不推荐使用。
>
> `plugin:` 开头的是规则存在插件中，格式为`plugin:包名/配置名`，也可以直接在 `plugins` 属性中进行设置，后面会讲到。
>
>  `eslint-config-` 开头，来自单独的npm配置包，使用时可以省略`eslint-config`，如上面的可以简写为`standard`（共享配置包需要安装才能使用）

- `root`，eslint从当前目录开始搜索配置文件，若遇到root参数为`true`的配置文件，会停止搜索，一般为true。

### 常用规则

常用基础规则集合

- [eslint-config-standard](https://www.npmjs.com/package/eslint-config-standard)，[JavaScript Standard Style 风格指南](https://github.com/standard/standard/blob/master/docs/RULES-zhcn.md#javascript-standard-style)
- [eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)，[Airbnb JavaScript 风格指南](https://github.com/lin-123/javascript)

一般情况下，上面两个规则集一起使用。

### 插件

官方提供的规则只能检测标准的JavaScript代码，如果写的是Vue单文件组件，eslint的规则就没办法了。**eslint通过插件的机制，来定制自己的规则进行检查**。eslint的插件和扩展一样有固定的命名格式，以`eslint-plugin-`开头，使用时可省略。

例如，需要支持Vue语法的检测：

```bash
npm install eslint-plugin-vue --save-dev
```

在`.eslintrc.js`文件中添加：

```javascript
module.exports = {
	"plugins":[
		"vue",
	]
}
```

添加后可支持Vue单文件语法的检测。

## Prettier

### 是什么

prettier是一个支持多种编程语言的代码格式化工具。其格式化原理非常简单：**prettier把代码（无论什么风格样式）转成抽象语法树（AST，不含任何代码样式），在AST的基础上重新格式化代码。prettier注重的是代码风格样式，不关注语法问题。**

### 怎么用

仅演示如何使用prettier，和eslint的结合使用，后文会讲到。

#### 安装

1. 在上面演示项目中，直接安装：

```bash
npm install prettier --save-dev
```

2. 创建配置文件

```bash
echo {} > .prettierrc.json
```

3. 创建忽略文件`.prettierignore`，当使用prettier进行格式化时会自动跳过，语法规则和`.gitignore`相同：

```
touch .prettierignore
```

#### 使用

命令很简单：

```
npx prettier --write 需要格式化的文件
```

例如，格式化`index.js文件`：

```bash
npx prettier --write ./index.js
```

格式化项目下所有文件:

```bash
npx prettier --write .
```

格式化指定文件夹下的文件：

```bash
npx prettier --write ./app/
```

#### 配置

prettier仅支持很少的配置项，力争做到一致性，在配置文件`.prettierrc.json`文件中进行配置，以下是可用的所有配置：

- `printWidth`指定代码每行长度，超出后会换行，默认为`80`
- `tabWidth`缩进时的宽度，默认为`2`
- `useTabs`是否使用制表符代替空格，默认为`false`
- `semi`语句是否有分号，默认`false`
- `singleQuote`字符串是否使用单引号，默认`false`，`jsx`中此属性不生效
- `jsxSingleQuote`在`jsx`中是否使用单引号，默认`false`
- `bracketSpacing`大括号两边是否有空格，默认`true`
- `jsxBracketSameLine`在jsx语法中标签闭合位置，默认为`false`，独占一行
- `arrowParens`箭头函数参数的圆括号，可选值`always|avoid`，默认`always`总是添加圆括号

简单常用配置：

```json
{
  "printWidth": 120,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "jsxSingleQuote": true,
  "bracketSpacing": true,
  "arrowParens": "always"
}

```

## VSCode配置ESLint

### 安装

安装插件：[vscode-eslint插件](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

### 配置

前提：项目中必须已经安装过`eslint包`。

- 保存时使用已安装可用插件修复代码，包括eslint插件

```json
"editor.codeActionsOnSave": {
    "source.fixAll": true
 }
```

- 保存时仅使用eslint插件修复代码

```json
"editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
}
```

- 配置需要验证的语言标识符，默认如下，**一般不用配置**：

```json
"eslint.probe": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "html",
    "vue",
    "markdown"
  ]
```

新版本配置非常简单，配置之后就可以通过项目下的`eslint包`和`.eslintrc.json`发现和修复代码，同时有语法错误的地方会有红色下划波浪线提示。

## VSCode配置Prettier

### 安装

安装插件：[vscode-prettier插件](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

### 配置

前提：项目中已经安装过`prettier包`。

- 设置默认格式化的插件为`prettier`：

```json
"editor.defaultFormatter": "esbenp.prettier-vscode"
```

- 打开保存时自动格式化

```json
"editor.formatOnSave": true,
```

**小结**：通过以上配置后，vscode既可以自动修复语法问题又可以格式化样式。但是，eslint同时也有代码风格检测的部分规则，会和prettier格式化后产生冲突。现在，只需要eslint负责语法部分，prettier负责格式问题就能解决冲突。

## ESLint和Prettier的结合使用

通过[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)配置，可以去除eslint自带的风格检测，进而启用prettier的格式化风格。

### 安装

```shell
npm install eslint-config-prettier --save-dev
```



### 配置

在`.eslintrc.js`下配置规则继承：

```js
{
	extends: ['eslint:recommended', 'prettier'],
}
```

现在，再保存时，会默认使用eslint进行语法检测，prettier进行代码格式化，两者互不冲突。





