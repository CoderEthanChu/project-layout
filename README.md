# golang工程标准结构（Standard Go Project Layout）

这是golang应用工程代码的基本的项目结构。虽然该结构不是由go语言核心开发团队提供的官方标准版本，但go语言生态中许多新老项目都采用了这种项目结构组成形式。其中的一些项目结构方式也比较流行。这个项目布局结构除了支持实际各种大型应用，还附带许多小的增强。

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


### `/vendor`

Application dependencies (managed manually or by your favorite dependency management tool like [`dep`](https://github.com/golang/dep)).

Don't commit your application dependencies if you are building a library.

## Service Application Directories

### `/api`

OpenAPI/Swagger specs, JSON schema files, protocol definition files.

See the [`/api`](api/README.md) directory for examples.

## Web Application Directories

### `/web`

Web application specific components: static web assets, server side templates and SPAs.

## Common Application Directories

### `/configs`

Configuration file templates or default configs.

Put your `confd` or `consul-template` template files here.

### `/init`

System init (systemd, upstart, sysv) and process manager/supervisor (runit, supervisord) configs.

### `/scripts`

Scripts to perform various build, install, analysis, etc operations.

These scripts keep the root level Makefile small and simple (e.g., `https://github.com/hashicorp/terraform/blob/master/Makefile`).

See the [`/scripts`](scripts/README.md) directory for examples.

### `/build`

Packaging and Continuous Integration.

Put your cloud (AMI), container (Docker), OS (deb, rpm, pkg) package configurations and scripts in the `/build/package` directory.

Put your CI (travis, circle, drone) configurations and scripts in the `/build/ci` directory. Note that some of the CI tools (e.g., Travis CI) are very picky about the location of their config files. Try putting the config files in the `/build/ci` directory linking them to the location where the CI tools expect them (when possible).

### `/deployments`

IaaS, PaaS, system and container orchestration deployment configurations and templates (docker-compose, kubernetes/helm, mesos, terraform, bosh).

### `/test`

Additional external test apps and test data. Feel free to structure the `/test` directory anyway you want. For bigger projects it makes sense to have a data subdirectory. For example, you can have `/test/data` or `/test/testdata` if you need Go to ignore what's in that directory. Note that Go will also ignore directories or files that begin with "." or "_", so you have more flexibility in terms of how you name your test data directory.

See the [`/test`](test/README.md) directory for examples.

## Other Directories

### `/docs`

Design and user documents (in addition to your godoc generated documentation).

See the [`/docs`](docs/README.md) directory for examples.

### `/tools`

Supporting tools for this project. Note that these tools can import code from the `/pkg` and `/internal` directories.

See the [`/tools`](tools/README.md) directory for examples.

### `/examples`

Examples for your applications and/or public libraries.

See the [`/examples`](examples/README.md) directory for examples.

### `/third_party`

External helper tools, forked code and other 3rd party utilities (e.g., Swagger UI).

### `/githooks`

Git hooks.

### `/assets`

Other assets to go along with your repository (images, logos, etc).

### `/website`

This is the place to put your project's website data if you are not using Github pages.

See the [`/website`](website/README.md) directory for examples.

## Directories You Shouldn't Have

### `/src`

Some Go projects do have a `src` folder, but it usually happens when the devs came from the Java world where it's a common pattern. If you can help yourself try not to adopt this Java pattern. You really don't want your Go code or Go projects to look like Java :-)

Don't confuse the project level `/src` directory with the `/src` directory Go uses for its workspaces as described in [`How to Write Go Code`](https://golang.org/doc/code.html). The `$GOPATH` environment variable points to your (current) workspace (by default it points to `$HOME/go` on non-windows systems). This workspace includes the top level `/pkg`, `/bin` and `/src` directories. Your actual project ends up being a sub-directory under `/src`, so if you have the `/src` directory in your project the project path will look like this: `/some/path/to/workspace/src/your_project/src/your_code.go`. Note that with Go 1.11 it's possible to have your project outside of your `GOPATH`, but it still doesn't mean it's a good idea to use this layout pattern.


## Badges

* [Go Report Card](https://goreportcard.com/) - It will scan your code with `gofmt`, `go vet`, `gocyclo`, `golint`, `ineffassign`, `license` and `misspell`. Replace `github.com/golang-standards/project-layout` with your project reference.

* [GoDoc](http://godoc.org) - It will provide online version of your GoDoc generated documentation. Change the link to point to your project.

* Release - It will show the latest release number for your project. Change the github link to point to your project.

[![Go Report Card](https://goreportcard.com/badge/github.com/golang-standards/project-layout?style=flat-square)](https://goreportcard.com/report/github.com/golang-standards/project-layout)
[![Go Doc](https://img.shields.io/badge/godoc-reference-blue.svg?style=flat-square)](http://godoc.org/github.com/golang-standards/project-layout)
[![Release](https://img.shields.io/github/release/golang-standards/project-layout.svg?style=flat-square)](https://github.com/golang-standards/project-layout/releases/latest)

## Notes

A more opinionated project template with sample/reusable configs, scripts and code is a WIP.

