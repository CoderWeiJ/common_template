# 前言

vue3+vite+ts+eslint+prettier+stylelint+husky+commilintrc，快速搭建一个项目

# 初始化项目

```shell
pnpm create vite <project-name> --template vue-ts
```

# 安装eslint

## 初始化

```shell
pnpm add -D eslint
pnpm eslint --init
```

```txt
(1) How would you like to use ESLint?
选择：To check syntax and find problems

(2) What type of modules does your project use?
选择：JavaScript modules (import/export)

(3) Which framework does your project use?
选择：Vue.js

(4) Does your project use TypeScript?
选择：Yes

(5) Where does your code run?
选择：Browser

(6) What format do you want your config file to be in?
选择：JavaScript

(7) Would you like to install them now?
选择：Yes

(8) Which package manager do you want to use?
选择：pnpm
```

## 修改配置文件

env中添加`node: true`，解决eslint找不到module的报错（自动生成的overrides里有则不用）

```js
// .eslintrc.cjs

module.exports = {
  // ...
  env: {
    // ...
    node: true
  }
};
```

在`package.json`中添加lint命令

```json
{
  "scripts": {
    // --ext 为指定lint哪些后缀的文件
    // --fix 开启自动修复
    "lint": "eslint . --ext .vue,.js,jsx,tsx --fix"
  }
}
```

## 安装vscode插件`Eslint`

保存时，自动执行`lint`命令修复代码

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll": false,
    "source.fillAll.eslint": true
  }
}
```

# 安装prettier

```shell
pnpm add prettier -D
```

```js
// .prettierrc.cjs
module.exports = {
  // 一行的字符数，如果超过会进行换行，默认为80
  printWidth: 120,
  // 一个tab代表几个空格数，默认为2
  tabWidth: 2,
  // 是否使用tab进行缩进，默认为false，表示用空格进行缩减
  useTabs: false,
  // 字符串是否使用单引号，默认为false，使用双引号
  singleQuote: true,
  // 行位是否使用分号，默认为true
  semi: true,
  // 是否使用尾逗号，有三个可选值"<none|es5|all>"
  trailingComma: 'none',
  // 对象大括号直接是否有空格，默认为true，效果：{ foo: bar }
  bracketSpacing: true
};
```

```json
// package.json
{
  "scripts": {
    "format": "prettier --write \"./**/*.{html,vue,ts,js,json,md}\""
  }
}
```

```json
// .vscode/settings.json
{
  // 保存的时候自动格式化
  "editor.formatOnSave": true,
  // 默认格式化工具选择prettier
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

## 解决eslint与prettier的冲突

eslint和prettier都有格式化代码的功能，为了两者不发生冲突，使用`eslint-config-prettier` + `eslint-plugin-prettier`

- eslint-plugin-prettier： 基于 prettier 代码风格的 eslint 规则，即eslint使用pretter规则来格式化代码。
- eslint-config-prettier： 禁用所有与格式相关的 eslint 规则，解决 prettier 与 eslint 规则冲突，确保将其放在 extends 队列最后，这样它将覆盖其他配置

```shell
pnpm add eslint-config-prettier eslint-plugin-prettier -D
```

```js
{
    extends: [
    'eslint:recommended',
    'plugin:vue/vue3-essential',
    'plugin:@typescript-eslint/recommended',
+    // 新增，必须放在最后面
+    'plugin:prettier/recommended'
  ],
}
```

# 安装stylelint

```shell
pnpm add stylelint postcss postcss-less postcss-html stylelint-config-prettier stylelint-config-recommended-less stylelint-config-standard stylelint-config-standard-vue stylelint-less stylelint-order -D
```

- stylelint: css样式lint工具
- postcss: 转换css代码工具
- postcss-less: 识别less语法
- postcss-html: 识别html/vue 中的<style></style>标签中的样式
- stylelint-config-standard: Stylelint的标准可共享配置规则，详细可查看官方文档
- stylelint-config-prettier: 关闭所有不必要或可能与Prettier冲突的规则
- stylelint-config-recommended-less: less的推荐可共享配置规则，详细可查看官方文档
- stylelint-config-standard-vue: lint.vue文件的样式配置
- stylelint-less: stylelint-config-recommended-less的依赖，less的- stylelint规则集合
- stylelint-order: 指定样式书写的顺序，在.stylelintrc.js中order/- properties-order指定顺序

```js
// .stylelintrc.js
module.exports = {
  extends: ['stylelint-config-standard', 'stylelint-config-prettier', 'stylelint-config-recommended-less', 'stylelint-config-standard-vue'],
  plugins: ['stylelint-order'],
  // 不同格式的文件指定自定义语法
  overrides: [
    {
      files: ['**/*.(less|css|vue|html)'],
      customSyntax: 'postcss-less'
    },
    {
      files: ['**/*.(html|vue)'],
      customSyntax: 'postcss-html'
    }
  ],
  ignoreFiles: ['**/*.js', '**/*.jsx', '**/*.tsx', '**/*.ts', '**/*.json', '**/*.md', '**/*.yaml'],
  rules: {
    'no-descending-specificity': null, // 禁止在具有较高优先级的选择器后出现被其覆盖的较低优先级的选择器
    'selector-pseudo-element-no-unknown': [
      true,
      {
        ignorePseudoElements: ['v-deep']
      }
    ],
    'selector-pseudo-class-no-unknown': [
      true,
      {
        ignorePseudoClasses: ['deep']
      }
    ],
    // 指定样式的排序
    'order/properties-order': [
      'position',
      'top',
      'right',
      'bottom',
      'left',
      'z-index',
      'display',
      'justify-content',
      'align-items',
      'float',
      'clear',
      'overflow',
      'overflow-x',
      'overflow-y',
      'padding',
      'padding-top',
      'padding-right',
      'padding-bottom',
      'padding-left',
      'margin',
      'margin-top',
      'margin-right',
      'margin-bottom',
      'margin-left',
      'width',
      'min-width',
      'max-width',
      'height',
      'min-height',
      'max-height',
      'font-size',
      'font-family',
      'text-align',
      'text-justify',
      'text-indent',
      'text-overflow',
      'text-decoration',
      'white-space',
      'color',
      'background',
      'background-position',
      'background-repeat',
      'background-size',
      'background-color',
      'background-clip',
      'border',
      'border-style',
      'border-width',
      'border-color',
      'border-top-style',
      'border-top-width',
      'border-top-color',
      'border-right-style',
      'border-right-width',
      'border-right-color',
      'border-bottom-style',
      'border-bottom-width',
      'border-bottom-color',
      'border-left-style',
      'border-left-width',
      'border-left-color',
      'border-radius',
      'opacity',
      'filter',
      'list-style',
      'outline',
      'visibility',
      'box-shadow',
      'text-shadow',
      'resize',
      'transition'
    ]
  }
};
```

# 安装husky和git hooks

## husky

```shell
pnpm add husky -D
npx husky add .husky/pre-commit "npx lint-staged"
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit $1'

```

## commitlint

```shell
pnpm i @commitlint/cli @commitlint/config-conventional -D
```
