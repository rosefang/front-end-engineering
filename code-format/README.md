# 代码规范
当一个项目不断迭代，随着人员的增多，项目中会出现越来越多的风格各异的代码。所以从项目的一开始，就去约束代码规范。或者采用渐进式的方案去规范，会更有益于项目的代码发展。
我们可以从3个方面来考虑。

## 1. 敲代码之前：统一编辑器风格

每个开发人员根据不同喜好用不同的编辑器，这个时候，如果有一个统一的编辑器配置，去覆盖开发人员原本的编辑器配置，在敲代码之前，就做了一些规范的统一，岂不快哉。

推荐使用editorconfig。


如何使用editorconfig呢？

1. 安装editorconfig插件。

   如果你的编辑器是vscode，要使editorconfig生效，vscode必须安装EditorConfig for VS Code。如果发现editorconfig没有生效，很有可能是没有安装插件。

2. 在项目跟目录下新建.editorconfig文件。editorconfig推荐配置如下：

```
root = true
[*]
charset = utf-8
end_of_line = lf
indent_size = 4
indent_style = space
insert_final_newline = true
trim_trailing_whitespace = true

# Tab indentation (no size specified)
[Makefile]
indent_style = tab

[*.md]
trim_trailing_whitespace = false

[{package.json,.travis.yml}]
indent_style = space
indent_size = 2
```



## 2. 保存代码之后：统一代码格式

推荐使用prettier。

prettier据官方介绍，是指"An opinionated code formatter"，意思就是，Prettier 是一个 Opinionated 的代码格式化工具。所以要掌握 Prettier 的精髓就是要理解这个单词。opinionated具体怎么理解呢，推荐看下[这篇文章](https://zhuanlan.zhihu.com/p/81764012)，已经解释得很清楚。这里不再赘述，只是提供一个最佳实践～

如何配置prettier呢？

1. 项目中安装prettier

   ```javascript
   npm install prettier --save-dev --save-exact
   ```

2. 根目录下配置prettier文件，有如下几种方式：

   - A `"prettier"` key in your `package.json` file.
   - A `.prettierrc` file written in JSON or YAML.
   - A `.prettierrc.json`, `.prettierrc.yml`, `.prettierrc.yaml`, or `.prettierrc.json5` file.
   - A `.prettierrc.js`, `.prettierrc.cjs`, `prettier.config.js`, or `prettier.config.cjs` file that exports an object using `module.exports`.
   - A `.prettierrc.toml` file.

   例如新建.prettierrc.js。内容推荐如下：

   ```javascript
   module.exports = {
   	useTabs: true,
   	tabWidth: 4,
   	semi: true,
   	trailingComma: 'es5',
   	bracketSpacing: true,
   	singleQuote: true,
   	endOfLine: 'lf',
   };
   ```

3. 运行命令进行格式化。或者使用插件。

   运行命令如下：

   ```
   // 在xxx/src目录下创建index.js文件，然后自己写一些JS代码。JS代码用上文那个超长的foo(......)就可以，自己也可以改的更乱一些，但必须符合JS语法。
   
   npx prettier --write src/index.js
   // 在看格式化之后的index.js，已经重新输出成固定格式了。
   ```

   如果你使用的是vscode，也可以安装插件Prettier - Code formatter，通过使用快捷键进行格式化：

   （1）Mac：CMD + Shift + P -> Format Document ；

   （2）WIndows：Ctlr + Shift + P -> Format Document

   

## 3. 提交git之前：统一代码质量

推荐使用eslint。

目前有3个主流方案。分别是Airbnb、Standard、Google。严格程度对比是Airbnb > Google > Standard。很多大公司都会形成自己的规范，但大部分也是基于这3个的标准再形成自己的规范。
如果是中小公司等前端人员不是太多的团队，推荐使用选择一个适合自己的标准规范即可。

笔者这里推荐的是"eslint:recommended+针对自己项目的配置"。

如果是vue项目，推荐的.eslintrc.js如下：

```
module.exports = {
  root: true,
  env: {
    node: true
  },
  extends: ["plugin:vue/essential", "eslint:recommended", "@vue/prettier"],
  parserOptions: {
    parser: "babel-eslint"
  },
  rules: {
    "no-console": process.env.NODE_ENV === "production" ? "warn" : "off",
    "no-debugger": process.env.NODE_ENV === "production" ? "warn" : "off"
  },
  overrides: [
    {
      files: [
        "**/__tests__/*.{j,t}s?(x)",
        "**/tests/unit/**/*.spec.{j,t}s?(x)"
      ],
      env: {
        jest: true
      }
    }
  ]
};
```



## 总结

editorconfig起作用是在敲代码之前，prettier起作用是在保存代码之后。如果说editorConfig和prettier保证了你的风格，那eslint助你提高品位。eslint是代码静态分析工具，用于发现不符合规范的代码。通过这3个步骤，完美帮你进行代码规范和约束。

前面第三步有提到，提交git之前，统一代码质量，其实，在git提交之前，就需要对前面3个约束都进行检查，这样才能真正意义保证代码规范的落地。下面就介绍如何将代码规范与git提交进行绑定起来。

# git提交约束

## 1. 提交前的代码预检查

推荐使用husky和lint-staged。

`husky`，你没猜错就是哈士奇的英文。二哈在这里的作用就是咬住git的hook不放。我们这里只关心 pre-commit 这一个 hook。

`lint-staged`，概念就是在git中暂存的文件上运行已配置的linter（或其他）任务。`lint-staged`总是将所有暂存文件的列表传递给任务，忽略任何文件都应该在任务本身中配置。

怎么使用呢？

第一步当然是要安装这2个包啦，当然也有npx mrm lint-staged的方式，但是生成出来的package.json配置不一定准确，所以还是更推荐去npm i，流程可以更能把控，当然这个仁者见仁智者见智啦。

```javascript
npm install husky
npm install lint-staged
```

第二步，在package.json中添加如下配置：

```javascript
"husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{vue,js,jsx,ts,tsx,html,css,less,saas}": [
      "prettier --config .prettierrc.js --write",
      "eslint"
    ]
  },
```

这个配置的意思是：

在commit之前，执行lint-staged的配置。lint-staged的配置意思是：

（1）使用prettier的配置进行代码格式化

（2）使用eslint的配置进行代码格式检查，如果检查不通过，则阻止提交。



## 2. 提交时的commit msg检查

好啦，当eslint通过之后，我们还要卡一步，就是commit msg的提交，很多人在提交代码的时候，不说明内容或者填写内容混乱。后期在查看git历史的时候，对应不上，这个时候约束commit msg就很重要了。

推荐使用commitlint。

步骤如下：

1. 项目中安装@commitlint/cli、@commitlint/config-conventional 

   ```
   npm install @commitlint/cli @commitlint/config-conventional
   ```

2. 在根目录下新建commitlint.config.js文件。

   ```
   module.exports = {
   	extends: ['@commitlint/config-conventional'],
   	rules: {
   		'type-enum': [
   			2,
   			'always',
   			[
   				'feat',
   				'fix',
   				'docs',
   				'style',
   				'refactor',
   				'perf',
   				'test',
   				'build',
   				'ci',
   				'chore',
   				'revert',
   			],
   		],
   	},
   };
   
   ```

   

3. 与husky结合。

   在package.json中添加配置：

   ```javascript
   "husky": {
       "hooks": {
         "commit-msg": "commitlint -e $GIT_PARAMS"
       }
     },
     "commitlint": {
       "extends": [
         "./commitlint.config.js"
       ]
     }
   ```

   结合第一步，最终我们的package.json会如下所示：

   ```javascript
   {
     "name": "FE-demo",
     "version": "0.1.0",
     "private": true,
     "scripts": {
     },
     "dependencies": {
     },
     "devDependencies": {
       "@commitlint/cli": "^11.0.0",
       "@commitlint/config-conventional": "^11.0.0",
       "eslint": "^6.7.2",
       "eslint-plugin-prettier": "^3.1.3",
       "eslint-plugin-vue": "^6.2.2",
       "husky": "^4.3.6",
       "lint-staged": "^10.5.3",
       "prettier": "^1.19.1",
     },
     "husky": {
       "hooks": {
         "commit-msg": "commitlint -e $GIT_PARAMS",
         "pre-commit": "lint-staged"
       }
     },
     "lint-staged": {
       "*.{vue,js,jsx,ts,tsx,html,css,less,saas}": [
         "prettier --config .prettierrc.js --write",
         "eslint"
       ]
     },
     "commitlint": {
       "extends": [
         "./commitlint.config.js"
       ]
     }
   }
   ```

   

# 项目demo

demo目录是附上本篇文章的项目例子，是使用vue初始化的一个项目。