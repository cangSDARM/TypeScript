Why not use OOP? TS 早期是使用面向对象的类开发的，从 1.0 开始，为了适配 JS 引擎的性能，所有源码已经没有类了，全部改用函数闭包


相关关系如下所示:

流程
> SourceCode（源码） ~~ 扫描器 ~~> Token 流 ~~ 解析器 ~~> AST（抽象语法树）~~ 绑定器 ~~> Symbols（符号）

验证
> AST + 符号 ~~ 检查器 ~~> 类型验证

输出JS
> AST + 检查器 ~~ 发射器 ~~> JavaScript 代码

> SourceFile + Context ~~> Program

> Program ~~ CompilerHost ~~> Program

`tsc ...`调用顺序
```
// tsc folder
ts.executeCommandLine 获取sys，调用执行函数

// executeCommandLine folder
调用 executeCommandLine 执行tsc
1. createDiagnosticReporter 错误报告
2. parseConfigFileWithSystem tsconfig.json
调用 performCompilation 编译过程
1. createCompilerHostWorker 编译监视器
2. createProgram 创建Program
3. emitFilesAndReportErrorsAndGetExitStatus emit并捕获编译时错误
4. sys.exit 退出

// compiler/program.ts
调用 createProgram 编译，并解析项目的结构
1. createCompilerHost 解析SourceFile
2. processRootFile 解析main指向的文件

反复处理文件、文件夹编译
1. processSourceFile
2. findSourceFile
3. processImportedModules

调用 findSourceFile 处理单个文件
2. host.getSourceFile 编译

// compiler/parser.ts
调用 getSourceFile
1. Parser.parseSourceFile 解析

调用 parseSourceFile
1. parseJsonText 解析Json文件
1. initializeState 初始化状态，注册scanner需要的内容(文字等)
2. parseSourceFileWorker worker

调用 parseSourceFileWorker
1. createSourceFile 绑定SourceFile的文件的通用属性(版本、文字等)
2. nextToken 激活scanner
3. processCommentPragmas 处理注释
4. processPragmasIntoFields 处理语法
5. addJSDocComment 处理JSDoc

调用 nextToken
1. nextTokenWithoutCheck 将工作移交给scanner

// compiler/scanner.ts
1. scan Scanner的Gatway

// compiler/watch.ts
调用 emitFilesAndReportErrorsAndGetExitStatus
1. emitFilesAndReportErrors 生成js并报告错误
2. program.emit emit js
```

**语法节点分类**

虽然语法节点种类很多，但其实只有四类：

1. 类型节点（Type Node）：一般出现在“:”后面（var a: 类型节点)，可以解析为一个类型。
2. 表达式节点（Expression）：可以计算得到一个值的节点，表达式节点只能依附于一个语句节点，不能独立使用。
3. 语句节点（Statement）：可以直接在最外层使用的节点，俗称的几行代码就是指几个语句节点。
4. 其它节点：其它内嵌在表达式或语句节点的特定节点，比如 case 节点。

> 在 TypeScript 中，一般类型节点以 TypeNode 结尾；表达式节点以 Expression 结尾；语句节点以 Statement 结尾

### Search More

*  [AST Explore](https://astexplorer.net/)
*  [ESTree Node Types Table](https://github.com/meriyah/meriyah/wiki/ESTree-Node-Types-Table)
*  [ECMA Specification](http://www.ecma-international.org/ecma-262/)
*  [Typescript Language specification](https://github.com/microsoft/TypeScript/blob/master/doc/spec.md)
*  [Typescript Roadmap](https://github.com/microsoft/TypeScript/wiki/Roadmap)
