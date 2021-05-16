---
title: GoCD Get Started
date: 2021-01-16 20:53:27
author: Dragon
toc: true
mathjax: false
categories: DevOps
tags:
    - GoCD
    - CI/CD
    - Pipeline
    - 部署
---
# GoCD萌新入门之路

>因本文首次发布在牛客网，故而部分图片外链无法显示。可直接通过下方链接跳转牛客网查看。
>Refer：https://blog.nowcoder.net/n/b800102df5354174b17819da225c4445
<hr> 

## 一、啊，什么是GoCD呀
首先，需要了解一下传统的集成方式，在传统的集成方式中，团队中每天上班拉取代码，下班前merge代码。若出现code conflict，大概率是大面积的code冲突，解决冲突便会花费超多时间，并且在传统方式中若不采取CI工具，也不能保证每次代码的提交的功能都是完整且不影响原有功能，一旦出现构建失败，不能及时发现也是其存在的问题。因此传统的集成方式存在很大的缺陷。

CI/CD就此出世。持续集成使用自动化工具协助开发团队，在每次merge代码后，尽可能无缝式的构建和测试代码。其主要流程包括：

- 小步提交功能代码
- 代码必须配备单元测试
- 每次提交会自动触发CI server, 其开始执行Build -> Test -> Package -> Deploy Dev Env任务
- 无论任务成功与否，都能及时给予团队反馈

持续交付是基于持续集成实现的，其流水线的各步骤和代码的部署集成都是自动的，只是在需要发布产品时候手动发布即可。在整个CD过程中还包含了monitoring, alarm, log and other tools组成实现反馈闭环系统以保证发布产品的可靠性。

持续部署与持续交付的区别仅在于，持续部署在不同环境下对产品的发布都是自动的，也就是在提交代码后，若不存在问题，其会自动发布生产版本。其目的就在于使用此方式，在任何时候，代码都是可直接发布至生产阶段的。

**GoCD**-一个开源的持续集成和持续交付（CI/CD）系统。

CI/CD 是一种通过在应用开发阶段引入自动化来频繁向客户交付应用的方法。CI/CD 的核心概念是持续集成、持续交付和持续部署。没必要纠结其具体语义概念，CI/CD就是一个流程，用于实现应用开发中的高度持续自动化和持续监控。

### 1、Pipeline、Stage、Job、Task

Pipeline、Stage、Job、Task依次为组合关系。一个Pipeline由多个Stage组成，Stage由Job组成，Job由Task组成。

![图片说明](https://uploadfiles.nowcoder.com/images/20210208/334329973_1612792694676/394659692A460258B45A99F1424EA357 "图片标题") 
- Task：是Pipeline中最小单位。需要执行的操作，通常是一个指令。eg: ./build
- Job: 由一系列**有序**task组成。一般，若某task失败，Job也将停止（除非有指定）。其中的Task对environment variable的操作不会影响其他Task，但对文件系统的操作会影响其他tasks。
- Stage: 由一系列**并行**的Job组成。其内的Jobs各自独立，可以并行执行。若某个Job失败，其他Jobs依然会继续运行，但当前Stage会被认为失败。
- Pipeline: 由一系列**有序**Stage组成，若某个Stage失败，Pipeline则失败，其后所有Stage都不会Start。

### 2、Materials and triggers (何时Pipeline会开始运行？)

Material 是Pipeline运行的一个基础。其通常是源代码材料仓（git,svn,mercurial,etc.），也可以是other Pipeline的Stage。

- GoCD Server 会不断的轮询Pipeline配置的Materials。当发现Materials有新的改变或提交时，Pipeline就会运行或被触发。

- “time trigger” 是一种特殊Material，可以在指定的时间或指定时间间隔触发Pipeline。

- Pipeline可以配置多个Materials，当任一Material有变化或提交都会触发Pipeline。


![图9：多种材料](https://uploadfiles.nowcoder.com/files/20210116/334329973_1610809201939/09_multiple_materials.png)

**Pipeline 作为 Material**

当Pipeline的一个Stage被用为另一个Pipeline的Material时，Material作用才更加突出。

如图：FirstPipeline的STAHE2时SecondPipeline的Material，当FirstPipeline的STAHE2执行完成后，即触发SecondPipeline开始运行。

FirstPipeline则称为Upstream Pipeline。

SecondPipeline称为DownStream Pipeline。

FirstPipeline的STAHE2被称为SecondPipeline的上游依赖。

![图片说明](https://uploadfiles.nowcoder.com/images/20210208/334329973_1612792757942/EFAF98DB2EAC3A61946CA0282AE6DDD4 "图片标题") 

Pipeline的任何Stage都可以作为Material，Stage运行完成即可触发新的Pipeline，不在乎其所在的Pipeline中后面的Stage是否成功。

### 3、Fan-out and Fan-in (扇出和扇入)

Fan-out 和 Fan-in是一个执行动作。

Fan-out: 当Material的完成，造成多个Downstream Pipeline被触发。该Material被称作Fan-out至Downstream Pipeline。即，由材料完成后，发散至多个下游Pipeline（1->多）。Fan-out的原因不一定总与Pipeline系系相关，可以是任何Material。

Fan-in: 指需要多种Upstream material 去触发Downstream Pipeline(多->1)。在GoCD中，其最重要一点在于必须在触发下游Pipeline前保证上游所有的material的版本一致。

![13_fanin](https://uploadfiles.nowcoder.com/files/20210116/334329973_1610809203861/13_fanin.png)

如上图所示，一个git仓作为Pipeline1和Pipeline2的material，此可称为Fan-out。而Pipeline1的Stage2和Pipeline2的Stage1可作为Pipeline3的material，此为Fan-in，若Pipeline1的Stage2慢于Pipeline2的Stage1，则Pipeline3会等待Pipeline Stage2的完成。多material情况下，必须保证每个material的版本是一致的，否则不会构建。

### 4、Value Stream Map（VSM：价值流图）

VSM：是整个Pipeline的上游依存关系和触发的下游Pipeline的端到端视图。在决定触发哪些Pipeline时，GoCD的Fan-out和Fan-in方法将处理所有的依赖关系。

![图片说明](https://uploadfiles.nowcoder.com/images/20210208/334329973_1612793113904/D89F8359EDC7D84465DB4BE60B9B9420 "图片标题") 

VSM，可以抽象的展示整个Pipeline的完整过程，从构建到生产。这是建模的抽象级别，可以直接展示端到端的工作流。其中也展示了一次构建Artifact并通过管道传播。并且在此图中展示了并行化的工作流程。正因为其展示了整个Pipeline，所以其可以提供提交到生产的审核性和可追溯性。

### 5、Artifacts（Pipeline内或之间有文件传递怎么办？）

GoCD中的每个Job都可以发布Artifacts。Artifacts可以使文件或者文件夹。

Job运行后，GoCD会确保Job指定的Artifact被发布并可被用户或者下游stage和pipeline使用。

一个Job可以发布多个Artifact。

类型：

Build Artifact：用于安装的程序，二进制文件，文档等。

Test Artifact：GoCD会尝试将文件解析为测试报告。

**如何获取Artifact**

GoCD提供一个指定任务“**Fetch Artifact Task**”，用于从任意祖先Pipeline（当前Pipeline的上流即可）获取和使用Artifact。GoCD会确保获取的Artifact版本是正确的。

![fetch_artifacts](https://uploadfiles.nowcoder.com/files/20210116/334329973_1610809201867/21_fetch_artifacts.png)

如图所示，Pipeline1发布了很多Artifacts，在Pipeline1/Stage2/Job1中的**Fetch Artifact Task**获取了其Stage1发布的文件。

在Pipeline2中获取了Pipeline1/Stage2/Job1发布的文件。Pipeline3中获取了Pipeline1/Stage1/Job2发布的文件夹（祖先->孙）。

### 6、Go-Agent（Pipeline在哪里运行呢？）

Go-Agent是GoCD生态系统的Workers。一个系统可以配置多个Go-Agent，但只有一个Go-Server。

Go-Server  : 是控制一切的服务器，提供用户界面，轮询Material是否有更改，分配相应的作业到各个Agent。其不会运行任何命令和进行部署。

Go-Agent : 执行具体工作的实施者，其执行由系统用户或管理员分配的任何工作。

当Job分配至Agent时，Agent会执行Job内的Task，并传递Job状态给Go-Server。因为Job可并行运行，所以其可分配至不同的Go-Agent，Go-Server会收集所有不同Job的状态信息，然后确定Stage状态。


### 7、Resources（增强Agent和Jobs）

使用Resources可以增强Agent和Jobs。Resources是自由格式的标签，用于帮助Agent决定哪些Agent可以执行特定的Job。

- Resources由管理员自定义的，可以表示任何含义。其就是一个自定义标签，用于设定Agent和Job关系。
- 对Job分配Resources十分有用。若Job分配了Resource，则在Agent中就必须包含此Resource，这样才可成功运行此Job。
- 不需要Resource的Job，可以在任何Agent上被成功执行；但分配了Resource的Job，必须在包含此Resource的Agent才能成功执行。

<img src="https://docs.gocd.org/current/images/concepts/19_agents_jobs_resources.png" alt="19_agents_jobs_resources" style="zoom:25%;" />

假设：

Job1 需要 Firefox Resource，Job2 需要 Linux Resource，Job3 需要Firefox和Linux Resource，Job4 不需要Resource。

Agent1 具有 Firefox和Linux Resource，Agent2 没有 Resource，Agent3 具有Firefox和Apple Resource。

因此：

1. Job1 只能分给带有Firefox Resource的Agent1或Agent3
2. Job2 只能分给Agent1
3. Job3 只能分给Agent1
4. Job4 可以给任一Agent

Agent 3具有Apple Resource这一事实并不会阻止其被分配作业。即使其是未被使用的Resource。

### 8、Environments（用于组合Pipeline和Agent的标签）

Environments 是GoCD中一种对Pipeline和Agent进行关联的方式。其规则：

- 一个Pipeline最多只能关联一个Environments （0-1）
- 一个Agent可以与多个Environments关联，也可以与任何Environments都不关联 （0-n）
- Agent只会被分配在同一Environments的Pipeline的Job
- 与任何Environments都不关联的Agent，只能执行与任何Environments都不关联的Pipeline。

<img src="https://docs.gocd.org/current/images/concepts/22_environment.png" alt="22_environment" style="zoom:28%;" />

如图：

1. Pipeline1 ，2的Job只能由Agent1，2，3执行。
2. Pipeline3，4的Job只能由Agent3，4执行。
3. Pipeline5，6的Job只能由Agent5执行。

Environments和Resources可以同时在Agent和Pipeline(Environments)/Job(Resources)匹配。

### 9、Environment Variables （用于Task使用）

Environment Variables 与 Environments 没有直接关系。

- 环境变量是在配置中自定义的变量。其用于Task，与操作系统运行中使用的环境变量类似。
- 环境变量可以被定义多个层级，由外至内为：Environments->Pipeline->Stage->Job。
- 遵循级联系统，外层定义的若在内层也定义了，则会被内层变量值覆盖。若外层定义，而内层未定义，则会传递至内层Job。

<img src="https://docs.gocd.org/current/images/concepts/23_env_vars.png" alt="23_env_vars" style="zoom:28%;" />

如图，在Jon中各环境变量值分别为：

ENV_ENV = 1

ENV_PIP = 2

ENV_STG = 3

ENV_JOB = 4

MY_VAR = 4

### 10、 Templates（可重用的工作流）

Templates 用于创建可重用的工作流，其目的在于：简化创建和维护分支以及管理大量Pipelines等任务。在Admin菜单的Template可以管理Pipeline Templates。

## 二、来来来，创建一个属于自己的Pipeline

万事开头难，若要使用GoCD，必须要先拥有它。若要GoCD正常工作，必须要有Go-Server和至少一个Go-Agent。

### 1、Docker启动Go-Server和Go-Agent

- 拉取go-serverv20.1.0 镜像： `docker pull gocd/gocd-server:v20.1.0`
- 启动go-server: `docker run -d --name="go-server-v20.1.0" -p 8153:8153 -v <root-path>/docker/go-server/godata:/godata gocd/gocd-server:v20.1.0`
- 拉取go-serverv20.1.0 镜像： `docker pull gocd/gocd-agent-ubuntu-16.04:v20.1.0`
- 启动Go-agent: 前提是go-server也在docker容器内运行，则可以使用：`docker run -d -e GO_SERVER_URL=http://$(docker inspect --format='{{(index (index.NetworkSettings.IPAddress))}}' go-server-v20.1.0):8153/go gocd/gocd-agent-ubuntu-16.04:v20.1.0`

### 2、创建Pipeline

1. 本地运行后，浏览器打开localhost:8153进入主页；若在服务器运行，也是对应服务器IP的8153端口。
2. 选择Materials类型Git，输入git仓地址（示例的demo的git仓http地址,若pipeline的Material为另一个pipeline，则更换类型进行选择即可）。输入定义的Pipeline名字，Stage名字，并定义一个Job：执行./build脚本。

![图片说明](https://uploadfiles.nowcoder.com/images/20210208/334329973_1612792928855/FF7C346F99A63BCCC8A62E1D2B2671BC "图片标题") 


3. 点击Save+Run this Pipeline，会进行跳转，并运行此Pipeline。

![图片说明](https://uploadfiles.nowcoder.com/images/20210208/334329973_1612792957147/28A53670953A0AA0317A5D071D3C64EE "图片标题") 

4. 待运行成功后，进度会变成绿色。点击小齿轮，可进入设置页面。

![图片说明](https://uploadfiles.nowcoder.com/images/20210208/334329973_1612792972327/573B7F89BE681C9B87781B7A2A3062A0 "图片标题") 

在其中就可以设置Resources，Environment variable，Artifacts。也可以在Pipeline设置中增加Stage，在Stage设置中增加Job，在Job设置中增加Tasks。



更多有关GoCD细节操作可查看官方文档~
**reference:** https://docs.gocd.org/current/