# 工具链

### 代码格式化工具（Formatter）

在[上一节](coding-standards.md)中，我们提到了“代码规范（Code Standards）”的概念。然而，在实际的编程工作中，我们总是很难完全记住所有的代码规范。因此，出现了一些工具帮助我们在编写代码时自动遵守、或提示我们遵守代码规范。

这类工具通常包括“**代码格式化工具（Formatter）**”与“**代码检查工具（Linter）**”。本小节主要介绍代码格式化工具。

代码格式化工具（Formatter）是负责将代码按照某一标准进行格式化的工具。例如，下面是使用 Python 的代码格式化工具 autopep8 格式化一段糟糕的 Python 代码的示例。

![Before](img/toolchain/autopep8-before.png)

![After](img/toolchain/autopep8-after.png)

下面列出一些编程语言常见的代码格式化工具。

- Python - Autopep8 / Black
- Java - Google Java Format
- C++ - Clang-Format
- JavaScript/TypeScript - Prettier
- Ruby - RuboCop
- Go - gofmt
- Rust - RustFmt

### 代码检查工具（Linter）

Linter 是一个不太容易翻译的词，有时我们将其翻译为“代码检查工具”，但更常见的做法其实是不翻译。简单来说，这是一种帮助你编写更好代码、以减少代码中潜在 BUG 的工具。Linter 中通常包含若干规则，如许多 Linter 会在函数的圈复杂度高于一定值时产生提示，建议你重构函数以降低代码复杂度。当然，也有许多针对不同编程语言的特定规则，如建议 Python 使用连续三个双引号而非单引号作为 docstring、对 Java 代码文件中潜在 null 值的检查、对 C++潜在内存泄漏问题的检测等。

一个比较知名的 Linter 是 SonarLint，它为多种不同的编程语言配置了大量规则。你可以在许多编辑器/IDE，比如 VSCode 和 IDEA 的插件市场中找到 SonarLint 插件并安装。

例如，在下面的 JavaScript 框架 React 的代码中，SonarLint 发现我们在 JSX 中传递节点数组时使用了数组下标作为 key，而这是不建议的做法。你可以打开 SonarLint 对这个规则的具体描述，看到为什么不建议这么做，与你可以怎么做来修正这段代码。

![Sonarlint Example](img/toolchain/sonarlint-example.png)

在下面这段 C++代码中，使用了 Clang-Tidy 作为 Linter，可以看到，我们在 main 函数中使用`new`在堆中分配了一个对象`h`，但却没有及时使用`delete`释放它。Clang-Tidy 检测到了这个问题，提示我们这里存在潜在的内存泄露风险，建议我们在使用`h`之后为其释放内存。

![Clang-Tidy Example](img/toolchain/clang-tidy-example.png)

下面列出一些编程语言常见的 Linter 工具。

- Python - Pylint / Ruff
- Java - Checkstyle / SpotBugs
- C++ - Clang-Tidy (clangd) / IntelliSense in Visual Studio
- JavaScript/TypeScript - ESLint
- Ruby - RuboCop
- Go - Golangci-lint
- Rust - Clippy

## 包管理器与依赖管理工具

对于初学者来说，“包管理器”、“依赖管理”、“构建工具”等概念是令人困惑且容易混淆的。并且，实际上很多工具本就“身兼多职”，也没有明确的角色区分，这进一步导致了这些概念上的混乱。在这一小节，我首先介绍什么是包管理器与依赖管理工具。

我们常说的“包管理器”，可能指系统上的软件包管理器，例如 Ubuntu 的 apt、CentOS 的 yum 与 ArchLinux 的 pacman，也可能指编程语言中的库管理器，通常是第三方库，比如 Python 自带的 Pip、JavaScript 生态中的 npm、Java 生态中的 Maven 等。对于后者，通常支持从网络中（一般是某个仓库网站，比如 Python 的 PyPI、Maven 的 MVN Repository 等）直接安装第三方库到本地。这样就可以免于繁琐的、在网络上寻找他人写好的库并费劲周折安装到你自己电脑上的麻烦。

还有一种被我称作“依赖管理”的工具，为一个个具体的代码项目工作，管理一个项目中所有的库，或者叫做“依赖”。这主要是由于不同的项目往往使用不同的编程语言版本甚至不同的第三方库版本，并且有独立的环境配置，如果让电脑上的所有项目都使用全局安装的库，就很容易出现不兼容问题，甚至因为编程语言版本不对而压根跑不起来。因此，通常的做法是为每一个项目单独管理一份环境，也就是“依赖管理”。实际上，大多数包管理器具有依赖管理的功能，比如 Maven、npm 这些工具天生就是“身兼数职”的工具，本就是为管理具体的某一项目而设计的，至于全局库的管理，反倒是顺带的。甚至，其实 Python 自带的 Pip 也有一定的“依赖管理”能力，Pip 可以通过建立一个虚拟环境为某个具体的项目安装特定版本的第三方库。只是 Pip 自身的依赖管理能力比较羸弱，存在很大缺陷，并且很难使用不同版本的 Python，因此出现了 Conda、Poetry 等解决方案提供更现代化的依赖管理能力。

需要注意的是，实际上关于这些工具的归类，一直没有一个明确的说法，这里将这些工具称作“包管理器”或“依赖管理工具”只是我个人的一种归类。包括下面提到的“打包工具”也是如此。

下面是一些编程语言中常见的包管理器/依赖管理工具。

- Python - Pip / Conda | Poetry
- Java - Maven / Gradle / Ant / Eclipse
- C++ - vcpkg
- C# - NuGet
- JavaScript - npm / Yarn / pnpm
- Ruby - RubyGems | Bundler
- Go - Go Package
- Rust - Cargo

## 打包工具

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
