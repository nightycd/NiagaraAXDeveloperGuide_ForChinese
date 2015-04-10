#API 相关信息

##概览
在Niagara中已经有海量的不同程度被记载的API可用，编写一个API时应明白几个关键概念：
* 稳定性：这个词柏树这API的成熟度和其初相不兼容的改变的可能性。
* Baja 与 Tridium：公开的API会放在java.baja包中，执行特殊目的的代码会放在com.tridium包中。

##稳定性（Statbility）
公共API被分为3类：
* 稳定版：此类API已经经过测底的评估并且已被锁定。此类API将会尽一切可能的稳定代码，使其在其各个发行版本中兼容（中间会重新编译）。即使是主要版本间的升级（如3.0到3.1）也如此，只有在有重大的BUG修正或设计缺陷时才会中断兼容。这并不意味着这些API会一成不变，新的类和新的函数会被持续的加入进来，但已存在的类和函数不会被移除。
* 评估版：此类API已实现其功能，并发布出来公开使用。评估版的API已成熟到能够使用在生产中，但是它们并没有足够多的应用及反馈来证明它足够稳定可以被锁定。此类API在主要版本更新中（如3.0到3.1）将可能有轻微的改动，这些改动将可能会打破二进制兼容性和源代码兼容性。但是，任何改动都应该能够使用合理的重构轻易的纳入至已有源代码中（比如函数改个名字）。
* 开发版：此类API指仍在开发中的代码，它是为需要框架最新功能的客户发布的。该类API将有可能出现因大规模重新设计而出现前后完全不兼容的改变的情况出现。

##Baja是什么
Baja来自于Building Automation Java Architecture（Java建筑自动化架构）的缩写。核心框架是Tridium公司设计，并发布为公开标准，并由Sun公司的Java社区开发发布为JSR 60，这个JSR仍在持续努力更新中，但重要的是要了解Baja和Tridium间的区别。

##标准与实现
从根本上讲，Baja是一种公开标准，而Tridium构建的Niagara框架是Baja标准的实现。作为一个标准，Baja不是一套软件，而纯粹是一组文档。Baja标准包括：
* Baja软件模块的打包标准。
* 组件模型及其API。
* 历史数据库组件及其API。
* 警报组件及其API。
* 日程安排组件及其API。
* BACnet驱动组件及其API。
* Lonworks驱动及其API
随着时间的推移，更多的标准和功能会被加进Baja，但是重要的是要记住Baja只是一个标准，Niagara是它的实现。同时，在Niagara中你还会发现很多不属于Baja的功能，从这方面来说Niagara是Baja的超集。

##Javax.baja 与 com.tridium
Niagara中的很多功能都通过Java API开放出来。在Java中，API一般都成组的以DNS域名的方式打包，Java社区开发组开发的软件一般都放在java或javax包中，为Baja开发的API一般都在javax.baja包中，这部分API是公开标准Baja的一部分，并且有可能是由Tridium之外的厂商实现的，这种措施保证的这些API的中立性和向后兼容性。

由Tridium开发的在Baja标准之外的软件，将会被集中于com.tridium包中。在com.tridium包中主要包含的是Niagara如何将Baja执行起来的代码，在com.tridium包中的代码不一定有文档说明，这些包大部分会有文档说明它的组件和slots的情况（使用bajadoc格式），但它们的更底层的文件和函数不会有说明。在com.tridium中的API将不允许被开发者使用，也不会保证其的兼容性。

`提醒：在javax.baja中，由Tridium开发的一些API并不属于当前的Baja标准。这些API是Niagara感觉也许最后会通过Baja发布，但当前正处在开发阶段的API。`
