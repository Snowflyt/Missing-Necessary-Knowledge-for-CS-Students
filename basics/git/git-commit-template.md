# Git Commit Template

关于要如何规范 Git 中提交（commit）时的信息格式，一直以来都是个比较让人头疼的问题。在很多项目中，尽管使用了 Git 做版本控制，但由于大家提交代码时都很随心所欲，提交信息（commit message）不能够清晰地表达本次提交所做的修改，因而仍旧不容易规范地管理仓库中的代码。

后来，为了解决这个问题，许多项目制定了对 commit message 格式的要求，甚至许多项目在将代码并入仓库前会检查 commit message 格式是否规范，如果不规范，则拒绝入库。而被大家普遍接受的一种格式是知名前端框架 Angular 仓库所采用的格式。这也被称为 Git Commit Template.

![Angular's Commit History](img/git-commit-template-1.png)

可以看到，在上图中，Angular 仓库的 commit message 看起来很清晰直观，这就是 Git Commit Template 带来的价值。

在 VS Code 中，可以通过 Git Commit Template 插件按照模板生成符合规范的 commit message.

![VS Code Extension for Git Commit Template](img/git-commit-template-2.png)
