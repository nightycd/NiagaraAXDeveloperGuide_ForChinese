#模块（Modules）

##概览
理解Niagara架构的第一步就是理解关于模块的概念。模块是Niagara架构中可部署的并拥有版本号的最小单位。一个模块应：
* 是一个使用PKZIP标准压缩的JAR文件。
* 包含了名为meta-inf.xml和module.xml两个XML格式清单文件。
* 已有版本号并可独立部署。
* 需指明其依赖的其他模块的名称和版本号。

##版本号
版本号指一列由“.”号分隔的数字，如“1.0.3042”。两版本号可比较他们之间的大小，比较的方法是从左比到右。如果前几位都一致，则位数多的比位数少的大。如：

	2.0 > 1.0
	2.0 > 1.8
	2.0.45 > 2.0.43
	1.0.24.2 > 1.0.24

每个模块都有两个版本号。第一个版本号是“Baja Version”，它标识该模块属于Baja标准的版本号，如果模块不是Java社区工作区发布的，则其版本号为0。第二个版本号声明一个厂商名（vendor Name）和一个厂商版本号（vendor Version），厂商名大小不敏感只用来指明该模块由哪个厂商开发的而已，而厂商版本号由厂商指定大小敏感。

Tridium的“vendor Version”以“主版本.副版本.发布数[.补丁]”规则命名：
* 主要版本和副版本定义了一个功能的发布版本，比如3.0。
* 第三个数指该版本整体编译次数，从0开始，每编译一次加一。
* 第四个数属可选项不一定加，它表示代码分支关闭的特殊版本，一般是因为小的Bug修正或代码的细微变化。

例如，版本号“3.0.45.2”表示此版本为3.0的第45此编译的第二个修正版本。

##清单（Manifest）
所有模块在其JAR文件中都必须包括一个清单，该清单包含在meta-inf.xml和module.xml文件中，说明该文件内容的最好方法就是举个例子：

``` xml
<?xml version="1.0" encoding="UTF-8"?> 

<module 
name = "control" 
bajaVersion = "1.0" 
vendor = "Tridium" 
vendorVersion = "3.0.20" 
description = "Niagara Control Module" 
preferredSymbol = "c" > 

	<dependencies> 
		<dependency name="baja" vendor="Tridium" vendorVersion="3.0"/> 
		<dependency name="bajaui" vendor="Tridium" vendorVersion="3.0"/> 
		<dependency name="bql" vendor="Tridium" vendorVersion="3.0"/> 
		<dependency name="gx" vendor="Tridium" vendorVersion="3.0"/> 
		<dependency name="workbench" vendor="Tridium" vendorVersion="3.0"/>
	</dependencies> 

	<dirs>
		<dir name="javax/baja/control" install="runtime"/> 
		<dir name="javax/baja/control/enum" install="runtime"/> 
		<dir name="javax/baja/control/ext" install="runtime"/> 
		<dir name="javax/baja/control/trigger" install="runtime"/> 
		<dir name="javax/baja/control/util" install="runtime"/> 
		<dir name="com/tridium/control/ui" install="ui"/> 
		<dir name="com/tridium/control/ui/trigger" install="ui"/> 
		<dir name="doc" install="doc"/> 
	</dirs> 

	<defs> 
		<def name="control.foo" value="something"/> 
	</defs> 

	<types> 
		<type name="FooBar" class="javax.baja.control.BFooBar"/> 
	</types> 

</module>

```

元素<module …>中各属性如下：
* name：模块的全局唯一的名称，开发者应为模块取一个唯一的名称以避免名称冲突。模块名称必须为1~255个ASCII码字符。
* baja Version：即前面“版本号”中所述的“baja Version”，命名规则如上所述。
* vendor：即前面“版本号”中所述的“vendor Name”，标识模块的开发公司。
* vendor Version：即前面“版本号”中所述的“vendor Version”，有开发公司指定。
* description：概述模块的目的。
* preferredSymbol：起始符号，用于XML的序列化。 

模块必须含有一个`<dirs>`元素，该元素将包含一个该模块所包含文件夹描述信息的子元素集（多个`<dir>`元素）。每一个`<dir>`元素包含一个name属性，标识该文件夹在模块内的相对路径，还包含一个install属性，其值如下：
* runtime（运行时）：该文件夹总会被主机安装。
* ui（界面）：当主机的模块内容筛选级别选中“ui”或“doc”时，该文件夹才会被主机安装。
* doc（文档）：当主机的模块内容筛选级别选中“doc”时，该文件夹才会被安装。

模块可以含有一个`<dependencies>`元素，也可以没有。该元素包含0或多个`<dependency>`子元素，这些子元素列举模块的依赖关系。模块所依赖的外部模块应已经被Niagara软件侦测到并能成功运行。每个`<dependencies>`元素至少要有一个属性。name属性特指依赖模块的全局名称 。一个`<dependencies>`元素亦可只指定一个baja Version和一个vendor Version。如果Baja Version被指定时，特指模块所需的最低版本的Baja Version，因为我们设定的是高版本Baja会向后兼容低版本Baja，所以如果系统使用高版本Baja而模块指定的是低版本Baja是可以接受并被运行的。同样的，vendor属性和vendor Version属性可以指定一个特定的编译版本模块。可以指定vendor属性而不指定vendor Version属性，反之则不行。当要强制模块在嵌入式Niagara设备上运行时可指定embed属性。

模块可以含有一个`<def>`元素，该元素会存储一对`字符串名字`/`值`的对应关系，所有的`<def>`会被存储在系统用来存储`<def>`的全局数据库registry中。

包含具体Niagara BObjects的模块还会包含`<types>`元素。该元素可以包含0或多个`<type>`元素，每个`<type>`元素定义一个在Baja类型和Java类名之间的映射 。每个`<type>`元素都必须包含“type”和“class”两个元素。
