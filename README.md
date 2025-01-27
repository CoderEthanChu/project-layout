# golang工程标准结构（Standard Go Project Layout）

这是golang应用工程代码的基本的项目结构。虽然该结构不是由go语言核心开发团队提供的官方标准版本，但是在go语言生态中许多新老项目都采用了这种项目结构组成形式。其中的一些项目结构方式也比较流行。这个项目布局结构除了支持实际各种大型应用，还附带许多小的增强。

如果你只是打算尝试学习一下golang，或者做做poc验证，或者是创建自己玩玩的项目，那么用这种项目布局结构就过头了，并不合适你的场景。建议你用简单的方式处理，可能一个单独的main.go文件就绰绰有余了。随着你的项目规模扩大，你需要注意，让你的代码保持合理的结构是很重要的，不然最终项目的状态将是一堆包含大量隐式依赖和全局状态的混乱代码。当有更多的团队成员在项目上工作的时候，项目将需要更多的目录结构。这体现引入package/lib的常用管理方式的重要性。当你有一个开源项目或者你知道其他项目将import你工程的代码时，这就体现有私有包和私有代码（项目中的`internal`目录）的重要性。在创建项目的时候Clone这个库，可以保留你要的，然后删除其他的，因为你没有必要使用全部所有项目目录结构。不是这里所有的目录在每个项目上都用得到，就连`vendor`目录也不是普遍都使用的。

该项目布局结构是为了通用性而设计，不会去引入一些非常特殊的go包结构

该工程来自社区努力，如果你发现有新的项目结构布局方式需要加入或者已有的项目布局需要更新，请上报issue。

你可以通过执行`gofmt`和`golint`来获得命名，格式化，代码风格等方面的帮助。另外你应该阅读如下go代码风格指导、建议文档。

 国内访问能访问参考文档:
 
*  https://golang.google.cn/doc/effective_go.html#names
* https://github.com/golang/go/wiki/CodeReviewComments
* [Style guideline for Go packages](https://rakyll.org/style-packages) (rakyll/JBD)

不能访问：

* https://talks.golang.org/2014/names.slide
* https://golang.org/doc/effective_go.html#names
* https://blog.golang.org/package-names


参考[`Go Project Layout`](https://medium.com/golang-learn/go-project-layout-e5213cdcfaa2) 获取额外的项目背景信息。

更多的命名和包组织以及其他代码结构的建议：

* [GopherCon EU 2018: Peter Bourgon - Best Practices for Industrial Programming](https://www.youtube.com/watch?v=PTE4VJIdHPg)
* [GopherCon Russia 2018: Ashley McNamara + Brian Ketelsen - Go best practices.](https://www.youtube.com/watch?v=MzTcsI6tn-0)
* [GopherCon 2017: Edward Muller - Go Anti-Patterns](https://www.youtube.com/watch?v=ltqV6pDKZD8)
* [GopherCon 2018: Kat Zien - How Do You Structure Your Go Apps](https://www.youtube.com/watch?v=oL6JBUk6tj0)

## Go 工程目录介绍

### `/cmd`目录
用于放应用的main包代码

`/cmd`下每个子目录名字应该和你想生成可执行文件名称相同（例如`/cmd/myapp`）

不要放太多的代码在应用目录cmd下，如果打算代码能够被其他项目import或者使用，应该放置到`/pkg`目录中。如果代码不是可复用的或者不希望其他项目复用，这个时候需要把代码放到`\internal`目录。别人使用代码的方式可能会让你意想不到，所有要明确你的意图，避免别人错误使用。

通常的方式是`main`方法要精简，除了导入和调用来自`/internal`和`/pkg`目录的代码以外，没有其他类型代码。

参考 [`/cmd`](cmd/README.md) 目录的示例.

### `/internal` 目录

该目录放私有功能和私有库的代码。这个目录中的代码是不希望被其他应用或者类库导入使用的。

将具体实际的应用代码放在`/internal/app` 目录中（例如 `/internal/app/myapp`），将多个应用要共用的代码放到`/internal/pkg`目录（例如 `/internal/pkg/myprivlib`）


### `/pkg`目录

该目录用于放置库代码（library code），库代码可以被外部应用使用（例如 `/pkg/mypubliclib`）。其他项目可能会导入使用这个目录下的库代码，并且期望能够运行正常，所以请想清楚相应的代码是否可以添加到这个目录。

当你的项目根目录包含其一些他非go的组件和目录时，`/pkg`目录也是一种把go代码组织到一起且容易运行各种go工具的方法。

参考[`/pkg`](pkg/README.md)目录中的内容，可以看到一些流行的go项目代码库中使用`/pkg`这个目录结构。这种方式很常见的，但是确没有被普遍接受，一些go社区的人并不推荐这种代码组织方式。


### `/vendor`目录

该目录为应用依赖目录（可以通过手工进行管理，或是者通过你们喜欢的依赖管理工具进行管理比如[`dep`](https://github.com/golang/dep)）

如果你们是构建一个库（library），不要提交应用依赖。


## service服务涉及的目录

### `/api`目录

该目录放置OpenAPI/Swagger描述文件，JSON格式文件，protocol协议定义文件。

参考[`/api`](api/README.md) 获取相关实例。


## Web应用涉及的目录

### `/web`目录


该目录放置web应用相关的组件代码：静态web资源，服务端模版和单页应用等


## 常规应用涉及的目录


### `/configs`目录

该目录放置配置文件或者配置模板。

把诸如`confd` 、`consul-template` 等目录的模板文件放置到该目录下。


### `/init`目录

该目录放置系统初始化（systemd，upstart，sysv），进程管理（runint，supervisord）等相关配置。

### `/scripts`目录

该目录放置各种功能脚本文件，如构建，安装，分析等等。

这些功能脚本使得根目录的makefile变得更简洁，（示例 `https://github.com/hashicorp/terraform/blob/master/Makefile` ）

查看 [`/scripts`](scripts/README.md) 目录获取更多示例。

### `/build`目录

该目录放置构建打包和持续集成的内容。

将应用相关的云，容器，操作系统的打包配置和脚本放到`/build/package`目录下。

将应用持续集成(travis, circle, drone)的配置和脚本放置到`/build/ci`目录下。注意有些持续集成工具（如Travis CI）他们对其配置文件所在位置有严格邀请。可以尝试将他们的配置文件放到`/build/ci` 目录中，然后链接到CI工具希望的目录。

### `/deployments` 目录

该目录放置部署相关的文件，如IaaS,PaaS,主机和容器编排的部署配置文件和模板文件（如 ocker-compose, kubernetes/helm, mesos, terraform, bosh等）

### `/test`目录

该目录放置其他外部的测试应用和测试数据。`/test` 目录内容可以自由构建。对于大型的项目来说创建一个数据子目录比较合理。例如，可以创建`/test/data` 或者 `/test/testdata`来让go忽略其中内容。注意go会忽略以"." 或者"_"开头的文件或者目录，所以你可以有更灵活的方式来命名测试数据目录。

参考[`/test`](test/README.md)目录中的示例。

## 其他目录

### `/docs`目录

该目录放置设计文档和用户文档（这类由godoc生成的文档之外文档）

参考 [`/docs`](docs/README.md) 获取更多示例


### `/tools`目录

该目录放置用于项目所用到的工具。注意这些工具可以被`/pkg` 和 `/internal` 目录中的代码导入使用。

参考 [`/tools`](tools/README.md) 获取更多的示例。

### `/examples` 目录

该目录放置应用或者开放libraries的使用示例。

参考[`/examples`](examples/README.md) 获取跟多示例。

### `/third_party`目录

该目录放置外部辅助工具，fork的代码，和第三方的工具（例如 Swagger UI之类）

### `/githooks`目录

该目录放置Git钩子配置.

### `/assets`目录


该目录放置项目工程需要的额外资源文件(如 图片，logo等)

### `/website`目录

如果不使用github page，这个目录可以放置你的项目工程说明网站
参考[`/website`](website/README.md) 获取更多示例。

## 不应该创建的目录

### `/src`目录

一些go的项目确是有`src`的目录，但这种情况发生在来熟悉java的开发人员身上，因为在java开发中src目录是项目工程中很常见的。可以尝试不再使用这种java的布局。真的不希望你的go代码或者go工程看起来像java:-)


不要将项目级别的`/src` 目录和go工作空间的`/src`目录（其中[`How to Write Go Code`](https://golang.org/doc/code.html)描述）混淆。`$GOPATH`的环境变量指向的是当前工作空间（非windows系统默认指向`$HOME/go` ）。工作空间目录包括三个顶级目录`/pkg`, `/bin` 和 `/src` 。实际项目最终是在`/src`下面的一个子目录。所以如果在工程中有`/src`目录，则文件路径就会像这样子`/some/path/to/workspace/src/your_project/src/your_code.go`。注意一点在GO1.11以后，项目工程可以放置到`GOPATH`以外，但是这并不意味着在项目中使用`/src`是一个好方式。


## Badges

* [Go Report Card](https://goreportcard.com/) - It will scan your code with `gofmt`, `go vet`, `gocyclo`, `golint`, `ineffassign`, `license` and `misspell`. Replace `github.com/golang-standards/project-layout` with your project reference.

* [GoDoc](http://godoc.org) - It will provide online version of your GoDoc generated documentation. Change the link to point to your project.

* Release - It will show the latest release number for your project. Change the github link to point to your project.

[![Go Report Card](https://goreportcard.com/badge/github.com/golang-standards/project-layout?style=flat-square)](https://goreportcard.com/report/github.com/golang-standards/project-layout)
[![Go Doc](https://img.shields.io/badge/godoc-reference-blue.svg?style=flat-square)](http://godoc.org/github.com/golang-standards/project-layout)
[![Release](https://img.shields.io/github/release/golang-standards/project-layout.svg?style=flat-square)](https://github.com/golang-standards/project-layout/releases/latest)

## Notes

A more opinionated project template with sample/reusable configs, scripts and code is a WIP.

