# 打包工具

然后是“打包工具”。它的职责是将代码“打包”成特定平台上可执行的文件，比如 Windows 上的 exe 文件或是运行在 Java 虚拟机中的 jar 文件等。

实际上上面提到的很多包管理器/依赖管理工具也具有打包功能。比如 Maven 除了依赖管理外也同时具有打包功能，可以直接将你的 Java 项目代码打包成一个 jar 包；又比如上面提到的 Poetry 也同样具有打包能力。

但是在有些编程语言生态中，“打包”这一职责通常使用专门的工具，比如 JavaScript 生态中由于要涉及到极其复杂的浏览器兼容工作，打包工具通常都是独立且复杂性极高的，作为单独的工具而存在，并且存在多种不同的打包工具，比如 Webpack 和 Vite。

下面是一些编程语言中常见的打包工具。

- Python - Poetry / PyPA / Conda Build
- Java - Maven / Gradle / Ant / Eclipse
- C++ - CMake / XMake
- C# - MSBuild / dotnet
- JavaScript - Webpack / Vite / ESBuild / SWC
- Ruby - Bundler
- Go - Go Build
- Rust - Cargo

可以看到这里的很多工具与上面提到的包管理器/依赖管理工具有所重叠，这是因为这些工具本身就是“身兼多职”的工具，比如 Maven 涵盖了整个管理依赖、测试、打包甚至发布的流程，而 Cargo、Poetry 等同样如此。对于这类“全能”工具，我们常常将其称为“构建工具”，当然，也有更多人只是简单地将它们统称为“包管理器”。
