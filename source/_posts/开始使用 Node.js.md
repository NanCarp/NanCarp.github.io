---
title: 开始使用 Node.js 
date: 2017-06-27 20:29:15
tags: Node.js MongoDB AngularJS Web 学习
---
# 3 开始使用 Node.js  

## 3.2 Node.js 安装  

### 3.2.1 纵观 Node.js 安装位置  
- **node：** 该文件启动一个 Node.js JavaScript 引擎。如果你传递一个 JavaScript 文件的位置， Node.js 就执行该脚本。如果没有制定目标 JavaScript 文件，就会出来一个脚本提示符，可以利用它直接从控制台执行 JavaScript 代码。  
- **npm：** 你可以侍弄此命令来管理 Node.js 包。  
- **node_modules：** 此文件夹包含安装的Node.js 包。这些包作为扩展 node.js 功能的库。  

### 3.2.2 验证 Node.js 可执行文件  
打开命令提示符，执行以下命令，这将弹出一个 Node.js 虚拟机：  
`node`  
接下来，执行以下命令，将 “Hello World” 写到屏幕上：  
`>console.log("Hello World");`  
当你看到 “Hello World” 被写入控制台屏幕后，可以按 Ctrl+C 组合键退出控制台。  
接下来，通过在命令提示符下执行以下命令来验证 npm 命令能正常工作：  
`npm version`  
你应该看到类似如下的输出：  
 ```
{ censorify: '0.1.1',  
  npm: '3.10.10',  
  ares: '1.10.1-DEV',  
  http_parser: '2.7.0',  
  icu: '58.2',  
  modules: '48',  
  node: '6.10.3',  
  openssl: '1.0.2k',  
  uv: '1.9.1',  
  v8: '5.1.281.101',  
  zlib: '1.2.11' }  
```

### 3.2.3 选择 Node.js IDE  
暂时用 MarkdwonPad2 ，仍然有些问题。计划用 WebStorm。  

## 3.3 使用 Node.js 包  
Node.js 框架最强大功能之一是能够轻松地使用 Node 包管理器（Node Packaged Manager，NPM）用额外的 Node 封装模块
（NPM）将其扩展。没错：在 Node.js 世界，NPM 意味着两种东西。本文将 Node 封装模块成为模块（Node Package Module，
module），而不是 NPM，以避免混乱。  

### 3.3.1 什么是 Node 封装模块  
Node 封装模块是一个打包的库，它可以很容易地在不同的项目中被共享、重用和安装。  

### 3.3.2 了解 Node 包注册表  
Node 包注册表位于 http://npmjs.org。  

### 3.3.3 使用 Node 包管理器  
Node 包管理器是一个命令行实用程序，它可以让你查找、安装、删除、发布，以及做与 Node 封装模块相关的其他很多东西。
Node 包管理器提供了 Node 包的注册表和开发环境之间的联系。  

### 3.3.4 搜索 Node 封装模块  
你可以直接在命令提示符下使用 npm search <search_string> 命令搜索在 Node 程序包注册表中的模块。  

### 3.3.5 安装 Node 封装模块  
在应用程序中使用 Node 模块，它必须先被安装在 Node 可以找到它的地方。要安装 Node 模块，使用 npm install <module_name>
命令下载 Node 模块到你的开发环境，并将其放置在 node_modules 文件夹中，在哪里运行 install 命令。  

### 3.3.6 使用 package.json  
所用 Node 的模块必须在其根目录下包含一个 package.json 文件。package.json 是定义了一个模块，包括其依赖关系的一个
简单的JSON 文本文件。改文件可以包含多个不同的指令来告诉 Node 包管理器如何处理模块。  
下面是一个包含名称、版本、描述和依赖关系的 package.json 文件的例子：  
```
{
    "name": "my_module",
    "version": "0.1.0",
    "description": "a simple node.js module",
    "dependencies": {
        "express": "latest"
    }
}
```
在 package.json 文件中必须的指令只是名称和版本；其余取决于你想要包含什么。  
从包的根目录运行下面的命令，express 模块会自动安装：  
`npm install`  
请注意，npm install 命令没有指定任何模块。这是因为 npm 在默认情况下会蟾照一个 package.json 文件。需要增加额外模块，
只要将这些模块添加到依赖指令中，然后再次运行 npm install。  

## 3.4 创建 Node.js 应用程序  
本节中，将会创建自己的 Node 封装模块，然后把该模块在一个 Node.js 应用程序中作为库来使用。  

### 3.4.1 创建 Node.js 模块封装  
建立一个 Node.js 封装模块 censorify，该模块接受文本并用星号代替某些特定的单词。  
1. 创建名为.../censorify 的项目文件夹。这将是此包的根目录。    
2. 在该文件夹中创建 censortext.js 文件。  
3. 添加以下代码  
```
var censoredWords = ["sad", "bad", "mad"];
var customCensoredWords = [];
function censor(inStr) {
    for (idx in censoredWords) {
        inStr = inStr.replace(censoredWords[idx], "****");
    }
    for (idx in customCensoredWords) {
        inStr = inStr.replace(customCensoredWords[idx], "****");
    }
    return inStr;
}
function addCensoredWord(word) {
    customCensoredWords.push(word);
}
function getCensoredWords() {
    return censoredWords.concat(customCensoredWords);
}
// 导出函数
exports.censor = censor;
exports.addCensoredWord = addCensoredWord;
exports.getCensoredWords = getCensoredWords;
```
导出 censor 是使用这个模块的 Node.js 应用程序能够访问 censor() 函数所需的，对于其他两个函数也是如此。  
4. 要生成 Node.js 封装模块，需要 package.json 文件，在 .../censorify 文件夹中创建 package.json 
文件。添加类似以下代码：  
```
{
  "author": "Brad Dayley",
  "name": "censorify",
  "version": "0.1.1",
  "description": "Censors words out of text",
  "main": "censortext",
  "dependencies": {},
  "engines": {
    "node": "*"
  }
}
```
具体而言，至少需要添加 name（名称）、version（版本）和 main 指令。main 指令需要时被加载的主 JavaScript 
的模块的名称，本例中是 censortext。注意，.js 不是必需的：Node.js 会自动搜索 .js 扩展名。  
5. 在 .../censorify 文件夹中创建 README.md 的文件，填写任意自述说明。  
6. 在控制台导航到 .../censorify 文件夹，执行以下命令来建立本地封装模块：  
`npm pack`  
npm pack 命令会在.../censorify 文件夹中生成 censorify-0.1.1.tgz 文件。这是你的第一个 Node.js 
封装模块。   

### 3.4.2 将 Node.js 封装模块发布到 NPM 注册表   

### 3.4.3 在 Node.js 应用程序中使用 Node.js 封装模块   
很简单，只要将模块安装到你的应用程序的结构中，再用 require() 方法加载该模块。  
require() 方法接受任何已安装的模块名或位于文件系统上的 .js 文件路径。例如：  
require("censorify")  
require("./lib/utils.js")  
.js 文件扩展名是可选的。如果它被省略，那么 Node.js 将搜索它。  
按以下步骤操作：  
1. 创建项目文件夹 .../readwords，以此为根目录。    
2. 从根目录的命令提示符下，使用以下命令来安装先前创建的 censorify-0.1.1.tgz 包 censorify 模块：  
`npm install ../censorify/censorify-0.1.1.tgz`  
3. 或者，如果你已经发布了 censorify 模块，可使用标准命令从 NPM 注册表下载和安装：  
`npm install censorify`  
4. 确认名为 node_modules 的文件夹，连同 censorify 的子文件夹应被创建。  
5. 创建 .../readwords/readwords.js 文件。  
6. 写入以下代码  
```
// readwords.js：一个显示文本时加载 censorify 模块的 Node.js 应用程序
var censor = require("censorify");
console.log(censor.getCensoredWords());
console.log(censor.censor("Some very sad, bad and mad text"));
censor.addCensoredWord("gloomy");
console.log(censor.getCensoredWords());
console.log(censor.censor("A very gloomy day."));
```
7. 以下命令运行：  
`node readwords.js`  
输出如下，注意，审查词都被替换为 ****，而且新的审查词 gloomy 被加入到 censorify 模块实例 sensor 中。  
```
$ node readwords.js
[ 'sad', 'bad', 'mad' ]
Some very ****, **** and **** text
[ 'sad', 'bad', 'mad', 'gloomy' ]
A very **** day.
```  

