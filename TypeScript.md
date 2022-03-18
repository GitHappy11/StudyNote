# TypeScript

**前置要求：JavaScript**

JavaScript的超集，可以看作TS是JS的工程，TS写完之后可以编译成JS,为什么不直接写JS，因为JS是一个弱类型语言，很多不符合规范的代码它依旧可以执行。使用TS可以更好的提示你在代码中的错误，防止出现预期之外的结果。本质上还是JS。

TypeScript不能直接被HTML引用，需要编译过后成为JS代码才能被识别。

### 1.安装环境

直接使用CMD进行安装 这玩意就是用来编译TS代码成JS的

```apl
nmp install -g typescript
```

然后在VSCODE中写好TS代码后，就可以编译了

```apl
tsc .\我的TS文件.ts
```

最终会在目录下输出一个js文件，用来给你引入到HTML中

### 2.VSCODE设置自动编译

首先要生成配置文件，如果是第一次 请打开PowerShell  更改执行策略，才能生成配置文件

```powershell
PS C:\Users\Administrator> set-ExecutionPolicy RemoteSigned
执行策略更改
执行策略可帮助你防止执行不信任的脚本。更改执行策略可能会产生安全风险，如 https:/go.microsoft.com/fwlink/?LinkID=135170
中的 about_Execution_Policies 帮助主题所述。是否要更改执行策略?
[Y] 是(Y)  [A] 全是(A)  [N] 否(N)  [L] 全否(L)  [S] 暂停(S)  [?] 帮助 (默认值为“N”): ytsc --init
PS C:\Users\Administrator>
```

更改完成后，在VSCODE打开终端（记得在根目录下打开）

```powershell
tsc --init //输出配置文件
```

然后就能看到输出的配置文件了 配置以下两个段落 记得先创建一个TS文件 否则它会找不到TS文件然而你确配置了TS导致报错

```json
"outDir": "./js",                                   /* 更改输出目录 */
"strict": false,                                    /* 关闭严格模式 */
```

最终就可以启动监视任务  选中配置配置  点击终端 运行任务 显示所有任务 监视 然后你再写ts代码，每次保存，就会自动输出相应的js文件了
