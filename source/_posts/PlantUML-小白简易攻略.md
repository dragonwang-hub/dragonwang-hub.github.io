---
title: PlantUML 小白简易攻略
date: 2021-02-20 21:47:22
author: Dragon
toc: true
mathjax: false
categories: Tech Tools
tags:
    - PlantUML
    - C4 modal
    - 绘图工具
---

# PlantUML 小白简易攻略

>因本文首次发布在牛客网，故而部分图片外链无法显示。可直接通过下方链接跳转牛客网查看。
>Refer：https://blog.nowcoder.net/n/21fa67558e0141669ddc6970b336879c
<hr>  
程序猿必备绘图工具PlantUML，不再像visio一样调整线条，边框，位置等等。只需要简单几行代码，export出最舒适的图。
PlantUML 是一个开源项目，支持快速绘制时序图、用例图、类图、活动图、组件图、状态图、对象图、部署图等。
作为一款快速绘图工具，其更多的是让用户可以关注于业务逻辑，而不是绘图样式，排版等。
其支持的文件后缀名是 *.wsd, *.pu, *.puml, *.plantuml, *.iuml

## 1、PlantUML工具的三种姿势

### a、PlantUML web server
如果machine没有任何开发工具，那么也是可以使用浏览器绘制plantUML图的。
打开link： http://www.plantuml.com/plantuml/uml/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000
即可开始绘制。
使用这种方式，不需要安装任何插件依赖，非常简单直接，但是不能实时预览图片，每次绘制完点击submit可以更新图片。
---
当然，作为一名Dev，不会缺少必备开发工具。无论是Vs code 还是 IDEA, 或者其他，都可以安装PlantUML plugins。
注意如果你在本机使用需要以下依赖：
- Java : 是运行PlantUML的必需条件, 请在您的环境中安装Java
- graphviz : 预览视图必须需要。[mac command :brew install graphviz]

### b、Vs code 安装PlantUML
按照以下步骤:
1. 打开vs code 插件管理栏，搜索palntUML，点击install安装
![图片说明](https://uploadfiles.nowcoder.com/images/20210219/334329973_1613728295146/AA387DA4D88B64B00833E66FC06BE715 "图片标题") 

2. 常用的指令: alt/option + D 开启实时预览窗口
![图片说明](https://uploadfiles.nowcoder.com/images/20210219/334329973_1613729572686/5BED10922FBEB7B4EB6FB31C9B3F2644 "图片标题") 

3. shift + command + p 调出命令行，输入plantUML,可以选择多种输出方式，包括export为图片，或者生成url。
![图片说明](https://uploadfiles.nowcoder.com/images/20210219/334329973_1613729832900/15E90D7594E8F12655DD41F2C0A0992F "图片标题") 

4. 在vs code中，可以在setting.json设置plantUML的自定属性。[一般不会使用]

### c、IDEA 安装PlantUML
1. 安装: 打开idea，快捷键command + ，打开设置，在左侧选择plugin点击，右侧搜索plantUML，安装**PlantUML integration**
![图片说明](https://uploadfiles.nowcoder.com/images/20210219/334329973_1613735261760/C8D256E32DBDA0A70EA0B4E38A20E62C "图片标题") 
2. 新建: 选择任意目录，新建文件，在弹出栏选择PlantUML，之后可以选择绘制的类型，Idea中会自动生成一个同类型的示例。
![图片说明](https://uploadfiles.nowcoder.com/images/20210219/334329973_1613730224207/2EE3D5382BDAE4091C751452E9F47690 "图片标题") 
3. 输出: 点击右侧预览图 save 按钮，即可输出至本地。（图片所示，为选择时序图后自动生成的样例）
![图片说明](https://uploadfiles.nowcoder.com/images/20210219/334329973_1613735621447/5AD219A6CB268C9CEF4D8058109B83DE "图片标题") 

## 2、PlantUML的多种使用姿势
当配置完成，就可以使用PlantUML绘制任意类型图，只有你想不到，没有它做不到。
首先，必须知道PlantUML采用DSL（领域特定语言）,语法简单易懂。
**每个file都以@startuml开头，以@enduml结尾**
但绘制不同类型图会采用不同的关键字。之后将讲解如何使用PlantUML绘制三种常用的图形。
### a、时序图
在时序图中，常用的关键字有：
- actor : 执行者，一般就是用户。
- participant : 参与者，在时序图代表整个流程的关键角色
- activate : 激活该角色生命周期，代表其起点
- deactivate : 终止该角色生命周期，代表其终点, 若声明了activate, 而不声明deactivate, 则默认至结束
- group : 用于分组某一部分时序为具体功能，以end为结束符
- alt : 条件选择框，与else结合使用，代表两种不同的情况，以end为结束符
- loop : 循环调用框，代表当前功能时序是一个循环过程，以end为结束符
- return : 对应上一句的数据流向的反向（虚线）
- note over: 显示一个标签框在当前位置，标记某些行为
- autonumber : 用于自动生成数字标号
注：->为实线，-->为虚线

**时序图示例**

```
@startuml
autonumber
actor User as user
participant "Browser UI" as browser
participant "Reseller UI" as reseller_ui
activate user
group loginFunction
user -> browser : Visit the Reseller UI login page
browser -> reseller_ui : Retrieve the Reseller UI login page
activate reseller_ui
browser <- reseller_ui : Return the login page with form field \nusername, password, and One Time Password(OTP)
end
user <- browser : Display the page, wait for user input
user -> user: Recall username and password \nfrom memory
user -> browser : Fill in the username and password field
user -> user: Open Google Authenticator, \nread the OTP
user -> browser : Fill in the OTP, and hit the send button
browser -> reseller_ui : Send the username, password and OTP
reseller_ui -> reseller_ui : Verify the information is valid
alt Login valid
    browser <- reseller_ui : Return the logged in page
    user <- browser : Display the logged in page
else Login invalid
    browser <- reseller_ui : Return login failure page
    deactivate reseller_ui
    user <- browser : Display the login failure page
end
loop at Loop_Example
note over user : it_is_loop_example
user -> browser : step 1 
return nothing
user -> reseller_ui : step 2
return nothing again
end
@enduml
```
如下为所根据代export的图片：
使用了group, alt, loop
![图片说明](https://uploadfiles.nowcoder.com/images/20210219/334329973_1613749830907/D905445717268C519DD18C6FD3C03D3D "图片标题") 

### b、C4模型图
在描述软件架构方面，C4 model是非常不错的选择，其具有多个层次描述软件系统架构，非常清晰明了简单。并且此模型既可以在设计系统阶段使用，也可以在系统构建完成后使用。
使用PlantUML工具使得绘制C4model图变得异常简单。仅需要注意以下常用点即可：
- 必须引入C4 container standard library (注意，使用此library，需要科学，负责无法打开此link): !include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
- 在C4 model中常用的Object类型有：
    1. Person/Person_Ext : 代表使用者，加上Ext(external）代表外部(灰色) 
    Person/Person_Ext(id, name, description)
    2. System/System_Ext : 软件系统方框，Ext代表外部系统(灰色)
    System/System_Ext(id, name, description)
    3. Boundary/System_Boundary : 虚线边界框，可将多个模块放在同一Boundary内。
    Boundary/System_Boundary(id, name)
    4. Container : 可用于代表系统内部服务
    Container(id, name, tech-stack, description)
    5. Relationship : 各模块之间的关系
    Rel(upstream, downstream, description, method)

C4 model 示例：（注，此示例仅为实现多个Object，未必是标准C4 model图）
在这示例代码中：
- LAYOUT_LEFT_RIGHT() : 保证图片排版从左至右，默认为上至下
- HIDE_STEREOTYPE() : 隐藏方框类型标签
```
@startuml System Context
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
LAYOUT_LEFT_RIGHT()
HIDE_STEREOTYPE()
Person(user, "User","description of user")
System_Boundary(system_id, "Web Application") {
    Container(ui, "UI", "React, TypeScript, Nginx", "Frontend page.")
    Container(api, "API", "Kotlin, Spring", "Backend API")
}
System_Ext(ext_system1, "ext_system1"," Code Hosting Platform for Version Control and Collaboration.")
System_Ext(ext_system2, "ext_system2", "Website Performance and Availability Monitoring.")
System_Ext(ext_system3, "ext_system3","Open Source Analytics & Monitoring for Multiple Data Sources.")
Rel(user, ui, "Uses", "HTTPS")
Rel(ui, api, "Interact", "HTTPS")
Rel(api, ext_system1, "Interact with", "HTTPS")
Rel(api, ext_system2, "Gets ext data from", "HTTPS")
Rel(api, ext_system3, "Gets ext data from", "HTTPS")
@enduml
```

如下为所根据代export的图片：
使用了Person, System, Container, Rel
![图片说明](https://uploadfiles.nowcoder.com/images/20210220/334329973_1613780480854/DB70862ED7FAEB9A1D59BBE302E35516 "图片标题") 

### c、流程图
任何时间、事件都有可能使用流程图。因此，我们最常用还是流程图，当然，PlantUML未失众望，其也是可以绘制流程图的。
基本流程图会涉及分支，循环，并行，终止等。使用此工具绘制流程图需要了解一下几点：
- start和end是流程图的开始和结束标志，其在图中显示为圆圈。
- “: 文字” 表示流程图的工作环节，以方框显示
- 分支使用方法包括 
    1. if(condition?) then(yes/no) ... else(no/yes) ...
    2. if(result?) is(what?) then ... else ...
    3. if(counter?) equals(Number?) then ... else ...
    注：分支中也有elseif 和 endif
- 循环使用方法包括
    1. repeat[循环体首] ... repeat while(condition?) is(yes/no)[循环体尾]
    如需要在循环返回线中增加其他环节，则可以使用**backward**关键字
    如需要在循环体中跳出，可以使用**break**关键字
    2. while(conditon?) ... endwhile
    3. while(value?) is(exist?) ... endwhile(not exist)
- 若要终止某一分支，可以使用stop/kill/detach
注：每个工作环节都以;结尾
代码示例：
```
@startuml
start
:"步骤1处理";
:"步骤2处理";
if ("条件1判断") then (true)
    :条件1成立时执行的动作;
    if ("分支条件2判断") then (no)
        :"条件2不成立时执行的动作";
    else
        if ("条件3判断") then (yes)
            :"条件3成立时的动作";
        else (no)
            :"条件3不成立时的动作";
        endif
    endif
    :"顺序步骤3处理";
endif
if ("条件4判断") then (yes)
:"条件4成立的动作";
else
    if ("条件5判断") then (yes)
        :"条件5成立时的动作";
    else (no)
        :"条件5不成立时的动作";
    endif
endif
repeat
    :循环体第一步;
    :循环体第二步;
    backward: 这是返回线的提示语;
repeat while("条件判断") is (yes)
while ("while示例，检测文件流数据" ?) is ("存在未读取数据")
    :读取文件;
endwhile (文件读完)
:关闭文件;
stop
@enduml
```
该流程export的图片如下：
使用了分支，及循环环节示例。
![/Users/yunlong.wang/Desktop/我的文件/interview++/plantUML_example/out/flowchart/flowchart.png](https://uploadfiles.nowcoder.com/images/20210220/334329973_1613800009671/0F4B7D73725360491D67C2FC17C863B8 "图片标题") 

至此，我们已经可以使用PlantUML绘制简单的模型图，本文所有示例只为展示各模块关键字，与具体业务无关。
### ...
如需要更多示例，可查看：https://real-world-plantuml.com/?type=usecase

## reference
- web server: http://www.plantuml.com/plantuml/uml/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000
- diagram example: https://real-world-plantuml.com/
- introduce: https://plantuml.com/zh/
- C4 model: https://github.com/plantuml-stdlib/C4-PlantUML
- install: https://formulae.brew.sh/formula/plantuml


