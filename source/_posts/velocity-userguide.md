---
title: velocity用户引导
categories: 视图模板技术
---

### 原文地址

http://velocity.apache.org/engine/devel/user-guide.html#What_is_Velocity

### 关于这个指导

velocity用户指导是为了帮助页面设计这和内容提供者熟悉velocity和其简单但强大的脚本语言语法-velocity模板语言(VTL)。在这个引导里的许多例子使用velocity在web网站嵌入动态内容，但是全部VTL例子同样可以应用到其他页面和模板。

### velocity是什么？

velocity是一个基于java的模板引擎。其允许web页面设计者参照在java代码里定义的方法。web设计者通过MVC模型，能够同时同步和java编程者开发web网站，意味着web页面设计这能够专注于创建被很好设计了的网站，于是编程者能够专注于编写顶层代码。velocity从web页面分离了java代码，使网站在长时间内可以更易于维护，而且提供了一个除了Java Server Pages或者PHP的可行方案。

velocity能够通过模板用来生成web页面，SQL，PostScript和其他输出。其能够被用来作为单独的工具生成源代码，报告或者当成一个嵌入到其他系统的组件。当完成时，velocity将会为Turbine web应用框架提供模板服务。velocity+Turbine将可以提供模板服务，以允许web应用通过mvc模型来进行开发。

### velocity能够帮我干什么？

##### 泥土商店例子

假设你是一个售卖泥土的线上商店的页面设计者。让我们称其为“线上泥土商店”。客户可以下不同类型和质量的泥土。他们通过使用用户名和密码登录到你的网站当中，以允许他们查看他们的订单和购买更多的泥土。现在，Terracotta泥土在售，很流行。少数你的顾客通常购买在售的红土。即使不是很流行，经常处于网页的边缘。关于每个顾客的信息在你的数据库中被跟踪，所以有一天问题发生，为什么不使用velocity来指定特定的，那些顾客最感兴趣的泥土类型的泥土交易呢？

velocity使为你的线上访问者制定web页面容易了些。作为一个在the Mud Room的网站设计者，你想要顾客在登陆你的网站后可以看见你的web页面。

你碰见在你公司的软件设计者，每一个人都同意$customer将会持有关于当前登陆用户的信息，$mudsOnSpecial将成为当前整在销售的所有类型的泥土。$flogger对象包含了帮助晋升的方法。对于手头上的任务，让我们只关心这三个参照对象。记住，你不需要关心软件工程师使如何将必要的信息从数据库中提取出来，你只需要知道其已在工作。这使每个人各司其职，互不干涉。

你能够嵌入以下的VTL陈述到web页面当中。

```
<html>
   <body>
     Hello $customer.Name!
     <table>
     #foreach( $mud in $mudsOnSpecial )
       #if ( $customer.hasPurchased($mud) )
         <tr>
           <td>
             $flogger.getPromo( $mud )
           </td>
         </tr>
       #end
     #end
     </table>
   </body>
</html>
```

foreach语句的细节将会被很快更深描述；重要的是这一个小的脚本将会在你的网站上产生的影响。当一个顾客有红色泥土的顾客登陆时，红色泥土则显示在售当中，顾客会看见，很明显地看见。如果另一个长期购买Terracotta泥土的顾客登陆，Terracotta泥土的销售将会显示在网页的最显眼的地方。velocity的灵活性很强大，只会被你的创造力约束。

在VTL参照文档里，会有许多其他velocity元素，提供给你创建网站的网站显示视图的能力和灵活性。当你更熟悉这些元素时，你将会开始发挥velocity的能力。

### Velocity Template Language (VTL): 一个介绍

velocity模板语言为的是提供最容易，最简单和干净的方法来和在web页面里的动态内容合作。甚至一个没有编程经验的web页面的开发者可以很快地使用VTL来和网站中的动态内容交互合作。

VTL使用参照来嵌入动态内容到一个网站中，一个变量则是一个类型的参照。变量是一种能够引用在java代码中定义的事物的类型，或者其能够在web页面自身中通过一个VTL描述获得他的值。这里是一个VTL描述的例子，可以嵌入到HTML文档中。

```
  #set( $a = "Velocity" )
```

这个VTL描述，像全部VTL描述，以一个#字符开始，并包含了一个指令：set。当一个线上访问者请求你的页面，velocity模板引擎将会通过你的web页面遍历查找全部#字符，然后决定哪一个是VTL描述，哪一个#字符和VTL没有关系。

set指令跟在‘#’字符后边。set指令使用表达式（包含在括号里）-一个等式将值赋予变量。变量被列在左边，其对应值被发在右边；两者通过一个=字符分割。

在以上的的例子中，变量是$a，值是Velocity。这个变量，像全部引用，以一个$字符开头。String值总是被包含在引号中，不论单引号还是双引号。单引号将会确保被引值将会被赋予引用。双引用允许你使用velocity参照和指令插槽，像是"Hello $name"，$name将会在将字符变量赋予当先左边的变量前，被当前值替代。

下面的的规则将会让你更好地理解velocity是如何工作的：以$开始的引用是为了获取某些东西。以#开始的指令是为了执行某型事情。

上面的例子当中，#set是用来将值赋予变量。变量$a能够在模板中使用，输出"velocity"。

### Hello Velocity World!

一旦一个值被赋予一个变量，你能够在你的HTML文档的其他任何地方引用这个变量。在以下的例子当中，一个值被赋予$foo，之后被引用。

```
<html>
  <body>
  #set( $foo = "Velocity" )
  Hello $foo World!
  </body>
</html>
```

以上的结果是在页面上显示"Hello Velocity World!"。

为了使包含描述的VTL指令更具可读性，我们鼓励你对每一个VTL描述新启一行。set指令将会在之后更深入细节。

### 注释

注释允许描述性文本被包含在模板引擎中而不被输出。注释是一个有效的方法，让你回忆起和解释给其他人你的VTL描述所干的事情，或者其他你认为有用的目的。下面是一个在VTL里写注释的例子。

```
## This is a single line comment.
```

一个单行注释以##开始，以行尾结束。如果你想要连续写几行注释，则不需要有很多单行的注释。多行注释，以‘#*’开头，‘*#’结束。

```
This is text that is outside the multi-line comment.
Online visitors can see it.

#*
   Thus begins a multi-line comment. Online visitors won't see this text because the Velocity Templating Eagine will ignore it.
*#

Here is text outside the multi-line comment; it is visible.
```

这里是一些例子。

```
This text is visible. ## This text is not.
This text is visible.
This text is visible. #* This text, as part of a multi-line
comment, is not visible. This text is not visible; it is also
part of the multi-line comment. This text still not
visible. *# This text is outside the comment, so it is visible.
## This text is not visible.
```

还有第三种类型的注释，VTL注释块，可以被用来储存任意一种额外的信息，以在模板中进行跟踪。（例如javadoc风格和版本信息）

```
#**
  This is a VTL comment block and
  may be used to store such information
  as the document author and versioning
  information:
    @author John Doe
    @version 5
*#
```

### 引用

在VTL有三种类型的引用：变量，属性和方法。作为一个使用VTL的设计者，你和你的工程师必须对指定引用的名字达成一致，所以才可以在你的模板里正确引用。

##### 变量

一个变量的缩写由一个以'$'字符开头的VTL标识符组成。一个VTL标识符必须由一个字母字符开始。剩下的字符被限制在以下的字符当中：

* 字母（a..z,A..Z）
* 数字（0..9）
* 下划线（"_"）

这里是一些在VTL里有效的变量引用：

```
$foo
$mudSlinger
$mud_slinger
$mudSlinger1
```

当VTL引用是一个变量，像是$foo，变量能够通过在模板里的set指令或者java代码获取他的变量。例如，如果当模板被请求时，java变量$foo拥有值bar，bar代替全部在web页面的$foo的实例。还有，如果我包括了描述

```
#set( $foo = "bar" )
```

在这个指令之后的全部对于$foo实例的输出都会相同。

##### 属性

第二种VTL引用的方式是属性，属性都有独特的格式。一个属性的缩写由一个以'$'字符开头的VTL标识符组成，之后是一个.字符（"."）和其他VTL标识符。这些例子在VTL里是有效的属性引用：

```
$customer.Address
$purchase.Total
```

第一个例子，$customer.Address。其拥有两个意义。一个可以表示，在哈希表里找customer的标识，然后返回指定键Address对应关联的值。但是$customer.Address也可以引用到一个方法（引用到一个方法的引用将在下一章讨论）；$customer.Address能够作为编写$customer.getAddress()的缩写。当一个页面被请求的时候，velocity将会决定哪两个可能性有意义，然后返回合适的值。

##### 方法

一个方法是被java代码定义，也能够做某些有效的事情，像是执行计算或者达成一个决定。方法充当引用是一个由一个以'$'字符开头的VTL标识符组成，接着是一个VTL方法体。一个VTL方法体由左右括号和中间的可选参数标识。这些是在VTL里的有效方法引用的例子：

```
$customer.getAddress()
$purchase.getTotal()
$page.setTitle("My Home Page")
$person.setAttributes( ["Strange", "Weird", "Excited"] )
```

头先两个例子-$customer.getAddress()和$purchase.getTotal()-可能看起来和那些属性章节叙述的，$customer.Address和$purchase.Total相似。如果你猜想这些例子必须在某种方式上相关联，你对了！

VTL属性对于VTL方法来说可以被用成缩写形式。$customer.Address属性和使用$customer.getAddress()有一样的效果。一般来说，可能的时候，使用属性是更加令人喜欢的。对于属性和方法主要的不同是你可以对方法指定一个参数列表。

以下方法可以写成缩写的形式。

```
$sun.getPlanets()
$annelid.getDirt()
$album.getPhoto()
```

我们可能希望这些方法返回属于太阳的的行星的名字，喂养我们地球，或者从相册获取图片。只有长的表示法对以下的方法有效。

```
$sun.getPlanet( ["Earth", "Mars", "Neptune"] )
## Can't pass a parameter list with $sun.Planets

$sisyphus.pushRock()
## Velocity assumes I mean $sisyphus.getRock()

$book.setTitle( "Homage to Catalonia" )
## Can't pass a parameter
```

全部数组引用被当成是有固定长度的列表。这意味着你能够在数组引用调用java.util.List方法和属性。所以，如果你有一个到数组的引用（比如String[]和三个值），你能够：

```
$myarray.isEmpty() or $myarray.empty
$myarray.size()
$myarray.get(2)
$myarray.set(1, 'test')
```

velocity还支持vararg方法。一个像azpublic void setPlanets(String... planets)的方法，或者甚至只是pulic void setPlanets(String[] planets)的方法现在能够接收任何参数数字。

```
$sun.setPlanets('Earth', 'Mars', 'Neptune')
$sun.setPlanets('Mercury')
$sun.setPlanets()
## Will just pass in an empty, zero-length array
```

在velocity2.0，方法调用现在提供了在java基本类型:数字，booleans和strings间的隐式转换。

##### 属性查找规则

像之前提及的，属性经常引用至父对象的方法。velocity是很智能的，可以查找请求属性对应的方法。其基于几个已建立的命名约定尝试不同的选择。精确的查找序列依赖于属性名称是否以一个大写字符开头。对于小写名，像是$customer.address，序列是

1. getaddress()
2. getAddress()
3. get("address")
4. isAddress()

对于大写字母属性名称像是$customer.Address,其有一点不同：

1. getAddress()
2. getaddress()
3. get("Address")
4. isAddress()

##### 渲染

每一个引用的最后值的结果在渲染到最后的输出时被转化成一个字符串对象（不管是变量，属性或是方法）。如果有一个对象代表$foo（比如整数对象），velocity将会调用.toString()方法来解析对象成为字符串。

##### 引索表示法

使用$foo[0]的表示法，能够被用来得到一个指定引索的对象。这个方式是和对一指定方法调用get(Object)方法一样的，$foo.get(0)，也为这个操作提供了语法上的缩写。这些可以很简单地调用get方法，如以下：

```
$foo[0]       ## $foo takes in an Integer look up
$foo[$i]      ## Using another reference as the index   
$foo["bar"]   ## Passing a string where $foo may be a Map
```

括号语法也对java数组有用，velocity包括了一个可访问的，提供了get(Integer)方法的，返回指定元素的对象。

在.get有效的地方，括号语法就有效：

```
$foo.bar[1].junk
$foo.callMethod()[1]
$foo["apple"][4]
```

一个引用也可以被设置为引索表示：

```
#set($foo[0] = 1)
#set($foo.bar[1] = 3)
#set($map["apple"] = "orange")
```

指定的元素被设置为指定的元素。velocity首先在元素上尝试set方法，然后put执行赋值。

### 正式的引用标识

对一个引用的简写标识在上面的例子中已使用，但是还有一个引用的正常标识，像以下所示：

```
${mudSlinger}
${customer.Address}
${purchase.getTotal()}
```

在大多数情况下，你将会使用引用的缩写标识，但是在一些条件下，为了正确的处理，正式的标识是必须的。

假设你正在飞行中构建一个$vice会在一个句子的名词被用作基础单词的句子。目标是允许某人来选择基本单词和产生下面两个结果中的一个：“Jack is a pyromaniac."或者"Jack is a kleptomaniac."。使用缩写标识对这个任务是不足的。考虑以下例子：

```
Jack is a $vicemaniac.
```

这里有歧义，velocity猜想$vicemaniac，不是$vice，是一个你想要使用的标识符。为$vicemaniac找不到值，其将会返回$vicemaniac。使用正式的标注可以解决这个问题。

```
Jack is a ${vice}maniac.
```

现在velocity知道$vice，不是$vicemaniac，是引用。在模板里，正式的标注在引用和文本挨着时是很有用的。

### 可选的值

正式的引用标注能够用来提供可选的值，在对引用的boolea验证是false的时候。

```
My name is ${name|'John Doe'}
```

如果$name是null，空的，false或者零，然后可选字符串'John Doe'将会被展示。

### 