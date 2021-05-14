# kubernetes源码学习 - 1

# 初始化环境

参照**README**或者根目录**Makefile**找到最快最小项目初始化命令，帮助下载依赖包和打包。

```
make quick-verify
```

- 注意事项
    - 一定要拿最新的代码 - 老版本的很多依赖可能在用后续初始化项目时无法找到
    - 一定要有VPN，用来下载golang library
    - 一定要用对Golang的版本（推荐[gvm](https://github.com/moovweb/gvm)）

# 项目结构

## 根目录

Golang的项目结构不同于Maven/Gradel构建的项目结构，这对刚开始接触Golang项目的小伙伴会有一些不适应，由于Golang至今未发布官方认可的项目结构，早期Golang的项目结构也是形式各异，但随着Golang社区的不断成熟，特别是一些开源项目的成功，也将它们所使用的项目结构推向了前台，得到了越来越多开发者的认可，形成了一些事实上的“官方”项目结构。

值得注意的是，虽然官方是放开手让开发者自行设计项目结构，不希望对开发者产生过多的约束，但是自Go 1.14发布了[Go Modules](https://github.com/golang/go/wiki/Modules)后，还是强烈建议**必用**。

如果你的项目就是一个简单的PoC或者自己写着玩，那么用一个`main.go`和`go.mod`就足够了！

但是为了阅读一些开源项目，还是可以看看社区如何定义项目结构的，比如提到的“面向包设计原则”。推荐阅读：[Standard Go Project Layout](https://github.com/golang-standards/project-layout#standard-go-project-layout)

回到Kubernetes源码中，其根目录的结构如下：

```
𝝀 tree -L 1
.
├── CHANGELOG
├── CHANGELOG.md -> CHANGELOG/README.md
├── CONTRIBUTING.md
├── LICENSE
├── LICENSES
├── Makefile -> build/root/Makefile
├── Makefile.generated_files -> build/root/Makefile.generated_files
├── OWNERS
├── OWNERS_ALIASES
├── README.md
├── SECURITY_CONTACTS
├── SUPPORT.md
├── _output
├── _tmp
├── api
├── build
├── cluster
├── cmd
├── code-of-conduct.md
├── docs
├── go.mod
├── go.sum
├── hack
├── logo
├── pkg
├── plugin
├── staging
├── test
├── third_party
├── translations
└── vendor

18 directories, 13 files
```

标红的目录是包含了Kubernetes比较重要组件的包。标绿的文件是Golang项目比较常用的构建工具[GNU make](https://www.gnu.org/software/make/manual/make.html)。安利一个不错的中文博客：[跟我一起写Makefile](https://seisman.github.io/how-to-write-makefile/introduction.html)。

## api/

Kubernetes API的OpenAPI specification文件

- 子目录

    ```
    𝝀 tree -L 1 api
    api
    ├── OWNERS
    ├── api-rules
    └── openapi-spec

    2 directories, 1 file
    ```

## cmd/

可执行文件的代码入口，每个可执行文件对应一个main函数

```
𝝀 tree -L 1 cmd
cmd
├── OWNERS
├── ~~clicheck~~
├── cloud-controller-manager
├── ~~dependencycheck~~
├── ~~gendocs~~
├── ~~genkubedocs~~
├── genman
├── ~~genswaggertypedocs~~
├── genutils
├── genyaml
├── importverifier
├── kube-apiserver
├── kube-controller-manager
├── kube-proxy
├── kube-scheduler
├── kubeadm
├── kubectl
├── kubectl-convert
├── kubelet
├── kubemark
├── ~~linkcheck~~
└── preferredimports

21 directories, 1 file
```

标红的部分，熟悉Kubernetes集群组件的小伙伴应该不会感到陌生，这些可以说是整个k8s最基础也是最常用的组件了，这个内容会在后续的源码学习内容中逐一讲解。而文字划了横线的部分，是文档生成和检查工具。

## pkg/

核心库代码，被项目内部或外部引用

- 子目录

    ```
    𝝀 tree -L 1 pkg
    pkg
    ├── OWNERS
    ├── api
    ├── apis
    ├── auth
    ├── capabilities
    ├── client
    ├── cloudprovider
    ├── cluster
    ├── controller
    ├── controlplane
    ├── credentialprovider
    ├── features
    ├── fieldpath
    ├── generated
    ├── kubeapiserver
    ├── kubectl
    ├── kubelet
    ├── kubemark
    ├── printers
    ├── probe
    ├── proxy
    ├── quota
    ├── registry
    ├── routes
    ├── scheduler
    ├── security
    ├── securitycontext
    ├── serviceaccount
    ├── ssh
    ├── util
    ├── volume
    └── windows

    31 directories, 1 file
    ```

按照pkg目录的功能，仅仅从目录中包名字就可以看出，想要深入了解Kubernetes，对这个包目录下代码的理解和熟悉程度，会是正比与你对Kubernetes底层原理的理解程度。这个也是后续在阅读源代码的重点部分。

## build/

与构建有关的脚本

- 子目录

    ```
    𝝀 tree -L 1 build
    build
    ├── OWNERS
    ├── README.md
    ├── build-image
    ├── common.sh
    ├── copy-output.sh
    ├── dependencies.yaml
    ├── lib
    ├── make-build-image.sh
    ├── make-clean.sh
    ├── nsswitch.conf
    ├── package-tarballs.sh
    ├── pause
    ├── release-images.sh
    ├── release-in-a-container.sh
    ├── release.sh
    ├── root
    ├── run.sh
    ├── server-image
    ├── shell.sh
    ├── tools.go
    └── util.sh
    ```

    简单搜索，这些脚本基本都可以在根目录的Makefile中找到相应的引用。这也是符合build/包的设计原则。

## test/

测试工具及数据

- 子目录

    ```
    𝝀 tree -L 1 test
    test
    ├── OWNERS
    ├── cmd
    ├── conformance
    ├── e2e
    ├── e2e_kubeadm
    ├── e2e_node
    ├── fixtures
    ├── fuzz
    ├── images
    ├── instrumentation
    ├── integration
    ├── kubemark
    ├── list
    ├── soak
    ├── typecheck
    └── utils

    15 directories, 1 file
    ```

Golang的测试框架是与Maven/Gradle生态中的设计有所不同，主要原因是Golang中，测试文件往往与源代码文件“伴生”，我这里说的“伴生”是指源文件和测试文件**同处一个目录下**，举例来说，源文件是`pkg/app/main.go`，那么测试文件是`pkg/app/main_test.go`。这就与Maven/Gradle的测试文件有较大的不同。

所以，Kubernetes项目中，单元测试基本都分散在各个模块或可执行文件中，而这里test/包，主要是一些集成测试（e2e）、测试通用工具、类型检测等。

以上是Kubernetes源码学习的第一部分。下一部分将先了解下Kubernetes的核心数据结构。