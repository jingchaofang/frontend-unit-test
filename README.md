# frontend-unit-test
frontend unit test


## 什么是单元测试

单元测试（unit testing），是指对软件中的最小可测试单元进行检查和验证。对于单元测试中单元的含义，一般来说，要根据实际情况去判定其具体含义，如C语言中单元指一个函数，Java里单元指一个类，图形化的软件中可以指一个窗口或一个菜单等。总的来说，单元就是人为规定的最小的被测功能模块。单元测试是在软件开发过程中要进行的最低级别的测试活动，软件的独立单元将在与程序的其他部分相隔离的情况下进行测试。——百度百科

对于JavaScript来说，通常也是针对函数、对象和模块的测试

## 为什么要进行单元测试

经验表明一个尽责的单元测试方法将会在软件开发的某个阶段发现很多的Bug，并且修改它们的成本也很低。在软件开发的后期阶段，Bug的发现并修改将会变得更加困难，并要消耗大量的时间和开发费用。无论什么时候作出修改都要进行完整的回归测试，在生命周期中尽早地对软件产品进行测试将使效率和质量得到最好的保证。在提供了经过测试的单元的情况下，系统集成过程将会大大地简化。开发人员可以将精力集中在单元之间的交互作用和全局的功能实现上，而不是陷入充满很多Bug的单元之中不能自拔。——百度百科

## 如何进行单元测试

### 选择测试工具

在JavaScript世界中，我们需要至少三个工具来进行单元测试，这意味着每个工具都需要你进行选择：

1. 测试管理工具
测试管理工具是用来组织和运行整个测试的工具，它能够将测试框架、断言库、测试浏览器、测试代码和被测试代码组织起来，并运行被测试代码进行测试。测试工具有很多选择，Selenium、WebDriver/Selenium2、Mocha、JsTestDriver、HTMLRunners和Karma，我这里选择使用Karma。（关于它们的对比，可以看看这篇文章：[karma 测试框架的前世今生](http://taobaofed.org/blog/2016/01/08/karma-origin/) ）。

2. 测试框架
测是框架是单元测试的核心，它提供了单元测试所需的各种API，你可以使用它们来对你的代码进行单元测试。JavaScript的测试框架可谓百花齐放，选择太多了（可以参考[List of unit testing frameworks](https://en.wikipedia.org/wiki/List_of_unit_testing_frameworks#JavaScript)），我这里选择使用Mocha（关于它们中一些框架的对比，可以参考[javascript单元测试](http://www.cnblogs.com/frostbelt/archive/2012/08/03/2622302.html)）

3. 断言库
断言库提供了用于描述你的具体测试的API，有了它们你的测试代码便能简单直接，也更为语义化，理想状态下你甚至可以让非开发人员来撰写单元测试。当然，你也完全可以不使用断言库，而是用自己的测试代码去测试，不过几乎没有人会这么干，除非你自己实现了一个测试断言库。测试断言库的选择也不少：better-assert、should.js、expect.js、chai.js等等（有关它们的对比，可以参考[几款前端测试断言库(Assertions lib)的选型总结](https://segmentfault.com/a/1190000007487648)）我这里选择chai.js。

有了上面的三个工具，你就可以开始对你的node代码进行测试了。但是如果想要对前端代码进行测试，还需要另外一个工具：

- 测试浏览器
前端代码是运行在浏览器中的，要对其进行单元测试，只能将其运行在浏览器上。目前大部分测试工具都支持调用和运行本地浏览器来进行测试，但如果你的测试仅仅是针对函数和模块的单元测试，则完全可以使用一款无界面的浏览器：PhantomJs

另外，还有一个很重要的事情就是测试覆盖率的统计。一般情况下你的测试管理工具会提供相关的覆盖率统计工具，但是有些情况下它们提供的工具未必是你想要的。比如当被测试的代码是经过了某些打包工具打包完了且被压缩和混淆了，同时打包工具还混入了很多自己的代码，这时覆盖率的统计就容易不准确。所以为了避免这种情况，测试覆盖率统计工具需要谨慎选择，至少你得确认它支持你的打包工具已经打包好的代码。

- 测试覆盖率统计工具
Karma-Coverage是Karma官方提供的覆盖率统计插件，自然成为项目的首选。
至此，我们需要的工具已经完备了，下面是选择好的工具清单：

测试管理工具：Karma
测试框架：Mocha
断言库：Chai
测试浏览器：PhantomJs
测试覆盖率统计工具：Karma-Coverage


### 构建一个最基本的测试

#### 配置好你的npm
初始化项目的package.json，并将需要的工具安装到项目中，安装完成后pakeage.json的devDependencies中应当出现下面的这些工具
```
"devDependencies": {
  "chai": "^3.5.0",
  "karma": "^1.3.0",
  "karma-chai": "^0.1.0",
  "karma-coverage": "^1.1.1",
  "karma-mocha": "^1.1.1",
  "karma-phantomjs-launcher": "^1.0.2",
  "mocha": "^3.0.2"
}
```

#### 初始化Karma配置文件
在项目的根目录运行
```
karma init
```
根据命令交互界面初始化Karma配置文件

生成的karma.conf.js只是最基本的karma配置，我们还需要手动修改一些地方。
在其中的frameworks一项中增加chai

```
frameworks: ['mocha','chai'],
```

然后在config.set({})中添加：

```
plugins : [
  'karma-mocha',
  'karma-chai',
  'karma-phantomjs-launcher'
],
```


#### 提供需要测试的代码并编写测试代码
在上文的初始化Karma配置时，已经告诉Karma，需要被测试的代码和测试代码放在`src/`和`test/`文件夹中，所以我们应该在`src/`文件夹下提供要被测试的代码，在`test/`文件夹下提供测试代码：

在src/文件夹中新建index.js文件，在这个文件中添加两个非常简单的函数：

```
function isNum (num) {
  return typeof num === 'number'
}
function isString (str) {
  return typeof str === 'string'
}
```

然后在test/文件夹中新建index.test.js文件。通常，测试脚本与所要测试的源码脚本同名，但是后缀名为.test.js（表示测试）或者.spec.js（表示规格）。在该文件中开始编写测试代码：

```
describe('index.js的测试', function () {
  it('1应该是数字', function() {
      // expect(isNum(1)).to.be.true
      isNum(1).should.equal(true)
  })
  it('"1" 应该是字符', function() {
      // expect(isString('1')).to.be.true
      isString('1').should.equal(true)
  })
})
```

编写测试文件时，describe、it都是由mocha提供的测试用api：

describe块称为"测试套件"（test suite），表示一组相关的测试。它是一个函数，第一个参数是测试套件的名称（"index.js的测试"），第二个参数是一个实际执行的函数。

it块称为"测试用例"（test case），表示一个单独的测试，是测试的最小单位。它也是一个函数，第一个参数是测试用例的名称（"1应该是数字"），第二个参数是一个实际执行的函数。
——测试框架 Mocha 实例教程

如果测试不通过，测试套件和测试用例的描述都会在命令行输出，告诉你哪里测试失败了。
被测试代码和测试代码编写完毕后，在项目根目录输入：

```
karma start
```

运行成功后，测试结果便会显示在命令行中。并且这时你修改任意代码，单元测试便会在你保存后自动运行。

#### 统计测试覆盖率
单元测试很多时候需要统计测试覆盖率。使用karma-coverage来统计你的单元测试覆盖率。修改karma.conf.js：

```
preprocessors: {
  'src/*.js': ['coverage']
},
reporters: ['progress', 'coverage'],
```

然后再运行karma start，你的项目中便会多出一个coverage文件夹，文件夹中按照浏览器分了覆盖率统计结果，我们使用的是PhantomJs，自然会有一个`PhantomJs ..*`文件夹，用浏览器打开index.html便可查看测试覆盖率。

### 集成webpack
很多时候，项目中会用到webpack来进行打包，有了Webpack我们可以使用ES6甚至ES7语法，可以轻松打包Vue、React、Angular等主流框架，可以有Eslint代码检查。所以将Webpack集成进Karma后，我们可以使用最新的JS语法来编写测试代码，也可以对使用了主流框架的代码进行单元测试了。

这里我们以使用ES6语法为目的，来演示如何集成Webpack。

#### 安装Webpack和Babel
首先安装Webpack和karma-webpack插件
```
npm install webpack karma-webpack --save-dev
```
然后安装babel
```
npm i --save-dev babel-loader babel-core babel-preset-es2015
```

#### 在Karma中配置和使用Webpack
修改karma.conf.js，将webpack添加进去。

#### 设置需要Webpack打包的文件
在preprocessors中告诉karma需要Webpack打包的文件所在位置，这里我想同时在被测试代码和测试代码中使用ES6语法，那么理论上我除了将被测试代码位置告诉Webpack之外，还需要将测试代码的位置也告诉Webpack。

但如果你的代码是模块化的，使用了ES6的模块系统，那么即使你将已经模块化的index.js打包好并注入到浏览器也是没有用的，所以正确的做法应该是在你的测试代码也就是index.test.js中引入index.js模块进行测试。然而Webpack在处理index.test.js时会查找它的引用并自动打包过来，所以如果你的被测试代码是模块化的，Karma配置中的preprocessors中就应当去掉Webpack对被测试代码的处理，同时files中也不需要让Karma将被测试代码放到浏览器了，这一切应当都交给Webpack来做：

```
preprocessors: {
  // 'src/*.js': ['webpack', 'coverage'],
  'src/*.js': ['coverage'],
  'test/*.js': ['webpack']
},
files: [
  // './src/*.js',
  './test/*.js'
],
```


#### 配置好Webpack
在Karma中写好Webpack的配置：

```
// webpack config
    webpack: {
      module: {
        loaders: [{
          test: /\\.js$/,
          loader: 'babel',
          exclude: /node_modules/,
          query: {
            presets: ['es2015']
          }
        }]
      }
    },
```

这一步有三点需要注意：

上面这些配置，完全可以独立出来成为一个webpack.test.config.js，怎么样，是不是很眼熟？
你可能已经注意到Webpack的配置中没有entry，也没有output，因为在Karma的preprocessors中已经告诉了Webpack需要打包哪些文件了，同时Karma也会处理好打包后文件的去向（当然是注入浏览器了，还能去哪，别忘记了还有karma-webpack这个插件在起作用）
测试的Webpack配置除了上面说的入口和出口，其余的配置跟普通使用Webpack没有本质区别，所以从这里你完全可以发散思维，用Webpack去做你想做的~

#### 添加karma-webpack插件
别忘记新版的Karma几乎所有的工具都需要插件支持，这在老版本中是不需要的。所以得把karma-webpack添加到Karma的plugins中去
```
plugins : [
  'karma-mocha',
  'karma-chai',
  'karma-phantomjs-launcher',
  'karma-coverage',
  'karma-webpack'
],
```


#### 在你的被测试代码和测试代码中使用ES6语法
首先是被测试代码
```
function isNum (num) {
  return typeof num === 'number'
}
function isString (str) {
  return typeof str === 'string'
}
export default {
  isNum,
  isString
}
```
然后是测试代码

```
import Index from '../src/index'
console.log('开始测试')
describe('index.js的测试', function () {
  it('1应该是数字', function() {
      // expect(isNum(1)).to.be.true
      Index.isNum(1).should.equal(true)
  })
  it('"1" 应该是字符', function() {
      // expect(isString('1')).to.be.true
      Index.isString('1').should.equal(true)
  })
})
```

我这里使用了ES6 中的模块写法，在index.js中输出了一个带有两个方法的模块，这时测试代码中就需要引入这个模块了，因为仅仅是简单地将index.js输出到浏览器是不会起任何作用的（webpack打包后，两个需要测试的函数已经是私有变量了，前文也有所提及）。

这时再运行karma start，便能看到测试通过的结果，说明我们成功使用了webpack编译了ES6。现在检查一下代码覆盖率：

会发现代码覆盖率无法正常检测了。即使你注释掉某个函数的测试用例，代码覆盖率仍旧是100%。这就是前文提到的，如果使用karma-coverage检测Webpack打包后的代码，就会出现这种情况。所以这里我们需要使用其它办法来检测代码覆盖率。

一般代码覆盖率的检测是需要统计被测试代码中需要测试的量，比如函数、行数等信息，然而打包后的代码因为被混入了很多别的代码，或者是变量被私有化了，这些统计就会出问题。所以最好的办法是在打包之前进行统计。

方案其实有很多，比如isparta、isparta-instrumenter-loader、istanbul。这里选择istanbul，因为karma-coverage用的就是它。同时，babel提供了一个插件babel-plugin-istanbul，能够在babel编译之前instrument你的ES6代码，可以像下面这样使用（参考babel-plugin-istanbul）：
首先安装babel-plugin-istanbul：
```
npm install babel-plugin-istanbul --save-dev
```

然后将其放入到babel的插件选项中：
```
loaders: [{
  test: /\\.js$/,
  loader: 'babel',
  exclude: /node_modules/,
  query: {
    presets: ['es2015'],
    plugins: ['istanbul']
  }
}]
```

这个插件的功能仅仅是instrument，不生成报告，所以报告的生成还是需要karma-coverage来完成的，所以之前有关karma-coverage的设置只需要将instrument部分也就是karma.conf.js中的preprocessors中的coverage去掉即可：

```
preprocessors: {
  // 'src/*.js': ['webpack', 'coverage'],
  // 'src/*.js': ['coverage'],
  'test/*.js': ['webpack']
},
```
这时再次运行`karma start`，便能看到测试通过的结果，说明我们成功使用了webpack编译了ES6。现在检查一下代码覆盖率：
已经恢复正常了！按照上面的思路，我们完成了将Webpack配置到Karma中的工作，所以现在你可以使用Webpack来统一管理你的被测试代码和测试代码了。


## related

https://www.jianshu.com/p/6726c0410650