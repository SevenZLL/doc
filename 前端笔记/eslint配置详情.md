

————————————————
版权声明：本文为CSDN博主「几面」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_34178990/article/details/89703577

# 前言

最近在实习期间犯了几个很弱智的错，一方面是自己的不小心， 另一方面也是项目的代码规范性检查不好。为了实现项目代码的规范性，降低出错率。 自己特别提出在commit之前加上一个eslint的代码检查，以确包上线代码中不会出现很低级的错误。

# 开始

git有一种钩子机制， 例如这里我们即将用到的pre-commit.

首先我们需要安装pre-commit： npm install pre-commit, 此时.git/hooks文件夹中就会出现一个pre-commit的文件。 这就代表pre-commit安装已经完成。当我们在执行pre-commit时， 就会执行pre-commit钩子里注册的命令行代码。

那么如何注册命令行代码呢？

在前端中，我们一般都会有一个package.json的文件，当我们安装了pre-commit之后，只需要在package.json中加一个pre-commit字段即可, 如下：

```javascript
{
	 "scripts": {
   		 "lint": "eslint --fix --ext .js,.vue ./frontend/src ./backend/"
  	},
	"pre-commit": [
		"lint"
	]
}

```

上面是package.json文件中两个字段， 当执行`git commit -m "test"`之后,就会找到package文件中的pre-commit字段， 然后执行注册的script任务， 即npm run lint。

# eslint 配置

上面的内容已经可以在git commit时还行eslint检查了，但是eslint相关配置并没有。接下来就需要配置好eslint。

```
ESLint最初是由Nicholas C. Zakas 于2013年6月创建的开源项目。它的目标是提供一个插件化的javascript代码检测工具。
```

怎么安装eslint请看：https://cn.eslint.org/docs/user-guide/getting-started

当我们安装好eslint之后， 并在项目根目录中创建一个.eslintre.js文件（也可以是其他格式的文件， 这里主要是方便注释）， 写上如下内容即可：

```javascript
module.exports = {
  root: true,
  env: {
    node: true,
    browser: true
  },
  extends: [
    'eslint:recommended'
  ],
  rules: {
    // 我们的项目中使用了webpack, 且用了import等模块导入导出语法，故需要加上eslint-plugin-import
    // 规范中提到的规则
    'no-use-before-define': ['error', { 'functions': true, 'classes': true }], // disallow use of variables before they are defined

    'prefer-const': ['error',  {'destructuring': 'any', 'ignoreReadBeforeAssign': false}], // 优先使用const

    'no-const-assign': 'error', // 不允许修改const的值
    }
}

```

上面的配置相当的简单，但实际上我们可能还会用到其他很多的配置。具体选择看个人情况，可以选择业界常用的规范配置例如： airbnb、google规范等。

我们一airbnb的规范eslint-config-airbnb-base为例：https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb-base 。这是airbnb公司最基本的规范（他们还有一个是结合了react的规范:eslint-config-airbnb）。我们只需要安装即可， 然后再配置文件中加上他们的配置即可：

```javascript
{
	extends: [
		"airbnb-base"
	]
}

```



但是他们的规范和可能并不是适合我们自己的开发习惯， 所以我们应该取其精华，去其糟粕；按照我们自己团队大多数人的开发习惯定制一套自己的开发规范。

我以下便是我目前的eslint配置。由于我们的项目使用了vue， 所以我安装了eslint 对vue语法支持的插件： eslint-plugin-vue. 最后我挑选了部分比较合适的规则。

```javascript
module.exports = {
  root: true,
  env: {
    node: true,
    browser: true
  },
  extends: [
    'plugin:vue/base',
    'eslint:recommended'
  ],
  plugins: [
    'import'
  ],
  globals: {
    'logger': false,// 在backend/plugin/global-logger.js中注册的全局办理
    'assert': false,
    'searchString': true,// jsapi-manager/getApi.js中使用
  },
  rules: {
    // 我们的项目中使用了webpack, 且用了import等模块导入导出语法，故需要加上eslint-plugin-import
    // 规范中提到的规则
    'no-use-before-define': ['error', { 'functions': true, 'classes': true }], // disallow use of variables before they are defined

    'prefer-const': ['error',  {'destructuring': 'any', 'ignoreReadBeforeAssign': false}], // 优先使用const

    'no-const-assign': 'error', // 不允许修改const的值

    'no-var': 'error', // 不允许出现var申明

    'no-new-object': 'error', // 不允许使用new Object的方式创建对象

    // https://eslint.org/docs/rules/object-shorthand
    'object-shorthand': ['error', 'always', { // 尽量使用对象属性简写
      ignoreConstructors: false,
      avoidQuotes: true,
    }],

    // https://eslint.org/docs/rules/quote-props.html
    'quote-props': ['error', 'as-needed', {// 尽量少的给属性名加引号
      keywords: false,
      unnecessary: true,
      numbers: false }
    ],

    // Prefer destructuring from arrays and objects
    // https://eslint.org/docs/rules/prefer-destructuring
    'prefer-destructuring': ['warn', {
      VariableDeclarator: {
        array: false,
        object: true,
      },
      AssignmentExpression: {
        array: true,
        object: true,
      },
    }, {
      enforceForRenamedProperties: false,
    }],

    // disallow use of the Array constructor
    'no-array-constructor': 'error',

    'quotes': ['error', 'single'], // 强制使用单引号

    'max-len': ['error', 120, 2], // 一行的字符不能超过100

    'prefer-template': 'error', // 优先使用模板字符串

    // disallow use of eval()
    'no-eval': 'error',

    'no-useless-escape': 'error', // 去掉不必要的转译

    'func-style': ['off', 'expression'], // 需要使用函数表达式

    'wrap-iife': ['error', 'outside'], // 立即执行函数需要用括号包裹

    // disallow reassignment of function parameters
    // disallow parameter object manipulation except for specific exclusions
    // rule: https://eslint.org/docs/rules/no-param-reassign.html
    'no-param-reassign': ['off', {
      props: true,
      ignorePropertyModificationsFor: [
        'acc', // for reduce accumulators
        'accumulator', // for reduce accumulators
        'e', // for e.returnvalue
        'ctx', // for Koa routing
        'req', // for Express requests
        'request', // for Express requests
        'res', // for Express responses
        'response', // for Express responses
        '$scope', // for Angular 1 scopes
      ]
    }],
    // disallow use of new operator for Function object
    'no-new-func': 'error',

    // suggest using the spread operator instead of .apply()
    // https://eslint.org/docs/rules/prefer-spread
    'prefer-spread': 'error', // 用spread操作符...去调用多变的函数更好

    // use rest parameters instead of arguments
    // https://eslint.org/docs/rules/prefer-rest-params
    'prefer-rest-params': 'error',

    // suggest using arrow functions as callbacks
    'prefer-arrow-callback': ['error', {
      allowNamedFunctions: false,
      allowUnboundThis: true,
    }],

    // disallow arrow functions where they could be confused with comparisons
    // https://eslint.org/docs/rules/no-confusing-arrow
    'no-confusing-arrow': ['error', {
      allowParens: true,
    }],

    // Enforce the location of arrow function bodies with implicit returns
    // https://eslint.org/docs/rules/implicit-arrow-linebreak
    'implicit-arrow-linebreak': ['error', 'beside'],

    // disallow use of Object.prototypes builtins directly
    // https://eslint.org/docs/rules/no-prototype-builtins
    'no-prototype-builtins': 'error',

    // disallow duplicate class members
    // https://eslint.org/docs/rules/no-dupe-class-members
    'no-dupe-class-members': 'error',

    // encourages use of dot notation whenever possible
    'dot-notation': ['error', { allowKeywords: true }],

    // disallow use of chained assignment expressions
    // https://eslint.org/docs/rules/no-multi-assign
    'no-multi-assign': ['off'],

    // disallow use of unary operators, ++ and --
    // https://eslint.org/docs/rules/no-plusplus
    'no-plusplus': 'off',

    // disallow declaration of variables that are not used in the code
    'no-unused-vars': ['error', { vars: 'all', args: 'after-used', ignoreRestSiblings: true }],

    // require the use of === and !==
    // https://eslint.org/docs/rules/eqeqeq
    'eqeqeq': ['error', 'always', { null: 'ignore' }],

    // disallow un-paren'd mixes of different operators
    // https://eslint.org/docs/rules/no-mixed-operators
    'no-mixed-operators': ['error', {
      // the list of arthmetic groups disallows mixing `%` and `**`
      // with other arithmetic operators.
      groups: [
        ['%', '**'],
        ['%', '+'],
        ['%', '-'],
        ['%', '*'],
        ['%', '/'],
        ['**', '+'],
        ['**', '-'],
        ['**', '*'],
        ['**', '/'],
        ['&', '|', '^', '~', '<<', '>>', '>>>'],
        ['==', '!=', '===', '!==', '>', '>=', '<', '<='],
        ['&&', '||'],
        ['in', 'instanceof']
      ],
      allowSamePrecedence: false
    }],

    // enforce position of line comments
    // https://eslint.org/docs/rules/line-comment-position
    // TODO: enable?
    'line-comment-position': ['off', {
      position: 'above',
      ignorePattern: '',
      applyDefaultPatterns: true,
    }],

    // require or disallow a space immediately following the // or /* in a comment
    // https://eslint.org/docs/rules/spaced-comment
    'spaced-comment': ['error', 'always', {
      line: {
        exceptions: ['-', '+'],
        markers: ['=', '!'], // space here to support sprockets directives
      },
      block: {
        exceptions: ['-', '+'],
        markers: ['=', '!'], // space here to support sprockets directives
        balanced: true,
      }
    }],

    'indent': ['error', 2],

    // require or disallow space before blocks
    'space-before-blocks': 'error',


    // disallow multiple empty lines and only one newline at the end
    'no-multiple-empty-lines': ['error', { max: 2, maxBOF: 1, maxEOF: 0 }],// 不允许多个空行.该规则目的在于，当你读代码时，减少滚动。当超过最大空行数，该规则将发出警告。

    // disallow dangling underscores in identifiers
    // https://eslint.org/docs/rules/no-underscore-dangle
    'no-underscore-dangle': ['warn', {
      allow: [],
      allowAfterThis: false,
      allowAfterSuper: false,
      enforceInMethodNames: true,
    }],

    // enforce consistent line breaks inside function parentheses
    // https://eslint.org/docs/rules/function-paren-newline
    'function-paren-newline': ['error', 'consistent'],

    // require or disallow spaces inside parentheses
    'space-in-parens': ['error', 'never'],

    // require spaces around operators
    'space-infix-ops': 'error',

    'no-else-return': 'error',

    // enforce spacing before and after comma
    'comma-spacing': ['error', { before: false, after: true }],// 该规则强制在变量声明、数组字面量、对象字面量、函数参数 和 序列中的逗号左右的空格的一致性。

    'key-spacing': ["error", { "afterColon": true }],

    'no-empty': ['error', { 'allowEmptyCatch': true }],
    
    // ========================
    'global-require': 'off', // 此规则要求所有调用 require() 必须在模块顶部，与 ES6 中 import 和 export 语句（只能放在顶部）相同。
    
    'comma-dangle': ['error', 'never'], // 结尾逗号
    
    'no-unused-expressions': 'error', // 不允许  this.toast.finally && this.toast.finally();形式
    
    'func-names': 'off', // 不允许使用 const getList = function() {...}，是个警告， 是否允许命名表达式
    
    'no-restricted-syntax': 'off', // 禁止使用for in
    
    'arrow-parens': 'off', // 箭头函数用小括号括起来
    
    'guard-for-in': 'off', // for in循环要用if语句过滤
    
    'no-await-in-loop': 'warn',//禁止在循环中 出现 await.然而，每次运算都执行 await，意味着该程序并没有充分利用 async/await 并行的好处。通常，代码应该重构为立即创建所有 promise，然后通过 Promise.all() 访问结果。否则，每个后续的操作将不会执行，直到前一个操作执行完毕。在许多情况下，一个循环的迭代实际上并不是相互独立的。例如，一次迭代的输出可能是另一次迭代的输入。或者，循环可以重试不成功的异步操作。在这种情况下，在循环中使用 await 是有意义的，并建议使用标准的 ESLint 禁用注释禁用规则。
    
    'consistent-return': 'off',//该规则要求 return 语句要么总是要么从不指定值。
    
    'default-case': 'error', //要求所有的 switch 语句中必须包含 default 分支，即使默认分支中没有任何代码,允许省略掉 default 分支，但是要写明注释以说明是故意为之。 // no default.该规则接受单个选项参数：设置 commentPattern 为一个正则表达式字符串，来改变默认的 /^no default$/i 注释匹配模式
    
    'no-continue': 'off', //该规则禁止使用 continue 语句。
    
    'no-empty-function': 'error', //该规则旨在消除空函数。如果一个函数包含了一条注释，它将不会被认为有问题。
    
    'no-tabs': 'error', //该规则查找文件中任何位置的 tab：代码，注释或其他
    
    'no-mixed-spaces-and-tabs': 'error',//该规则禁止使用 空格 和 tab 混合缩进。
    
    'one-var': ['error', 'never'],//
    
    'no-return-await': 'off',//该规则旨在防止由于缺乏对 async function 语法的理解而造成的常见的性能风险。在 async function， return await 是没有用的 。因为 async function 的返回值总是包裹在 Promise.resolve，在 Promise resolve 或 reject 之前，return await 实际上不会做任何事情。这种模式几乎可以肯定是由于程序员不知道 async function 语法的返回值造成的。
    
    'no-shadow': 'error',//此规则旨在消除变量声明覆盖。
    
    'no-return-assign': 'off', //此规则目的在于移除 return 语句中的赋值语句。因此，当在return中发现赋值，该规则将发出警告。
    
    'semi': ['error', "never"],
    
    "semi-style": 'off',
    
    'object-curly-newline': 'off', //要求需要有一致的换行符
    
    'no-trailing-spaces': ["error", { "skipBlankLines": true }],//禁用行尾空白
    
    'object-curly-spacing': 'error',//该规则强制在对象字面量、解构赋值 和 import/export 说明符的花括号中使用一致的空格。 { key: value }与{key: value}
    
    'keyword-spacing': 'error', //该规则强制关键字和类似关键字的符号周围空格的一致性：as、async、await、break、case、catch、class、const、continue、debugger、default、delete、do、else、export、extends、finally、for、from、function、get、if、import、in、instanceof、let、new、of、return、set、static、super、switch、this、throw、try、typeof、var、void、while、with 和 yield。该规则不会与其它空格规则发生冲突：它并不应用于别的规则会报告问题的空格。
    
    'vars-on-top': 'off',//for (var i=0; i<10; i++) {}会报错
  },
  parserOptions: {
    parser: 'babel-eslint',
  },
  settings: {
    'import/resolver': 'webpack'
  }
};


```

该规则可以直接使用， 普遍的规范需要还是能满足的。

