# printlog-demo

试验 git submodule 特性，主项目。

第三方库调用程序。使用 [printlog 第三方库][1] 的主程序

在官方文档中 [子模块][2] 中明确指出：

> 在项目中使用子模块的**最简模型**，就是只使用子项目并不时地获取更新，而并不在你的检出中进行任何更改。

与之相比，在子模块上工作以及发布子模块改动都要复杂很多，学习曲线上升，操作也并不简洁，并不容易记忆。

如果主项目和子模块，我们都有完全的权限，那么遵守最简模型就是最佳实践：相对而言最不容易出错的操作模型。

但开发中如何处理稍作修改才能使用的第三方 GitHub 仓库呢？在上述文档中并未针对性地给出解决方案。

- 有些第三方项目简单，我们指示参考某个函数的实现，或者拷贝几个文件直接使用。
- 但对于 grpc 这样的大项目就捉襟见肘了，当然也有二愣子直接把 grpc 克隆之后，生成静态库或动态库，直接将二进制文件上传到主项目仓库中做版本控制。

目前摸索出来的**最佳实践**是使用 vcpkg 包管理器，无需关心代码仓库，也无需操心二进制文件的存储。

那在 vcpkg 中尚未引入的库怎么办呢？没有价值上传到 vcpkg，但我们的项目又要使用的 GitHub 开源库如何处理？

使用 git submodule 无疑，但这种几乎无人关注的仓库在跨平台、跨编译工具，甚至代码自身都存在缺陷，做不到拿来就可以用，往往需要做些修改才能应用到我们的项目中。怎么办？

- Pull requests? 不推荐，沟通成本大，等原作者合并遥遥无期，而且我们的修改往往不具备广泛适用的特征，不适合推到公共库中。
- fork 一份自己提交维护？不推荐，跟踪原作者的更新耗费精力，污染个人账户，污染 GitHub 仓库。
- 克隆一份，改改生成了动态库就搁那不管了。项目中用到 grpc-xp 目前是这样，风险在于  
    
    你得祈祷动态库永远不出错，不更新。grpc-xp 仓库丢了还能重新克隆，但你的 modification 和 base 如果丢了，呵呵

这种调整往往和主项目是高度相关的，所以这种调整纳入主项目的版本管理更适合。参考 vcpkg，发现有个 patch 的概念，怎么使用？

[Patching the code to improve compatibility](https://vcpkg.readthedocs.io/en/latest/examples/patching/)

`git format-patch` 生成补丁时默认命名规则使用 commit message，但只抽取其中的英文字符，会过滤掉中文汉字。

具体利用 patch 管理三方依赖的案例见：[grpc-win-xp](https://github.com/tnie/grpc-win-xp)

[1]:https://github.com/tnie/printlog
[2]:https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97
