# 名称
Catalyst::Manual::About - Catalyst的理念

# 描述
本文档是对*为什么*使用Catalyst的基本介绍。它不教您如何编写Catalyst应用程序；有关介绍请参阅[Catalyst::Manual::Intro](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Intro.pod)。相反，它解释了Catalyst典型应用的基础知识，以及为什么使用Catalyst来构建应用程序。

## 简短摘要（什么是Catalyst？)
Catalyst是一个Web应用程序框架。这意味着您可以使用它来帮助构建在Web上运行的应用程序，或者使用用于Web的协议运行的应用程序。Catalyst旨在使您可以轻松管理在Web上运行应用程序所需执行的各种任务，也可自行执行，或者让您“插入”执行所需操作的现有Perl模块。您通常使用Web应用程序执行许多操作。例如：

* 与Web服务器交互

  如果您在网络上，则依赖于Web服务器，即通过Web发送文件的程序。有很多这样的，您的应用程序必须做正确的事情，以确保您的程序与您正在使用的Web服务器一起使用。如果更改Web服务器，则不必重写整个应用程序以使用新的应用程序。

* 根据URI执行某些操作

  Web应用程序通常使用URI作为用户与应用程序其余部分进行交互的主要方式; URI的各种元素将指示应用程序需要执行的操作。因此，`http://www.mysite.com/add_record.cgi?name=John&title=President`将向您的数据库添加名为“John”的人，其标题为“President”，`http://www.mysite.com/catalog/display/23`并将转到目录中项目23的“显示”，`http://www.mysite.com/order_status/7582`并将显示订单7582的状态，`http://www.mysite.com/add_comment/?page=8`并将显示一个表单到添加注释到第8页。您的应用程序需要有一个常规的方法来处理这些URI，以便它知道在这样的请求进入时该怎么做。

* 与数据存储交互

  您可能使用数据库来跟踪您的信息。您的应用程序需要与数据库交互，因此您可以创建，编辑和检索数据。

* 处理表单

  当用户提交表单时，您会收到它，处理它以确保它已正确填写，然后根据结果执行某些操作 - 提交订单，更新记录，发送电子邮件或在有错误时返回表单。

* 显示结果

  如果您在网络上运行应用程序，人们需要查看内容。您通常希望您的应用程序显示在Web浏览器上，在这种情况下，您可能会使用模板系统来帮助生成HTML代码。但您可能需要其他类型的显示，例如PDF文件或其他形式的输出，例如RSS源或电子邮件。

* 管理用户

  您可能需要“用户”的概念，允许使用您的系统的人，并且只允许执行某些操作。也许普通用户只能查看或修改自己的信息；管理用户可以查看或修改任何内容; 普通用户只能为自己的帐户订购商品；普通用户可以查看但不能修改它们；订单处理用户可以将记录发送到系统的不同部分等等。你需要一种方法来确保人们是他们所说的人，并且人们只做他们被允许做的事情。

* 开发应用程序本身

  在编写或修改应用程序时，您希望能够访问其正在执行的操作的详细日志。您希望能够编写测试以确保它能够执行预期的操作，并且新的更改不会破坏现有代码。

Catalyst可以轻松完成所有这些任务，还有更多。它在允许您做的事情方面非常灵活，而且速度非常快。它具有大量与现有Perl模块交互的“组件”和“插件”，因此您可以在应用程序中轻松使用它们。

* 与Web服务器交互？

  Catalyst允许您使用许多不同的，甚至还带有内置服务器用于测试或本地部署。

* 根据URI做一些事情？

  Catalyst具有极其灵活的系统，可根据URI确定要执行的操作。

* 与数据存储交互？

  Catalyst有许多用于不同数据库和数据库框架的插件，以及其他非数据库存储系统。
  
* 处理表单？

  Catalyst具有可用于多个表单创建和验证系统的插件，便于程序员轻松管理。

* 显示结果？

  Catalyst具有可用于许多模板模块和其他输出包的插件。

* 管理用户？

  Catalyst具有以您需要的任何方式处理会话，身份验证和授权的插件。

* 开发应用程序？

  Catalyst内置了详细的日志记录，您可以根据需要进行配置，并支持轻松创建新测试 - 其中一些测试在您开始编写新应用程序时自动创建。

### 哪些不属于Catalyst？

Catalyst不是一个开箱即用的解决方案，它允许您在十分钟内设置完整的工作电子商务应用程序。（但是，有几个系统构建在Catalyst之上，可以让你非常接近工作应用程序。）

Catalyst的设计灵活和有效；在某种程度上，这是以简单为代价的。程序员可以为他们需要做的几乎所有事情提供多种选择，这意味着任何给定的需求都可以通过多种方式完成，找到适合您的方法，并学习正确的方法，可能需要时间。TIMTOWDI可以双向工作。

Catalyst不是为最终用户设计的，而是为程序员工作的。

## 网络编程：旧时代

Perl长期以来一直受到Web应用程序的青睐。在Web上使用Perl的方法有很多种，而且随着时间的推移，事情也发生了变化。可以使用非常原始的Perl代码处理所有内容：

```perl
print "Content-type: text/html\n\n<center><h1>Hello
World!</h1></center>";
```

例如，或者：

```perl
my @query_elements = split(/&/, $ENV{'QUERY_STRING'});
foreach my $element (@query_elements) {
    my ($name, $value) = split(/=/, $element);
    # do something with your parameters, or kill yourself
    # in frustration for having to program like this
}
```

比这更好的是使用Lincoln Stein的伟大CGI模块，它可以平滑地处理各种常见任务 - 参数解析，从Perl数据结构生成表单元素，打印http标题，转义文本等等，所有这些都与您的选择功能或面向对象的风格。虽然CGI是革命性的并且仍然被广泛使用，但它具有各种缺点，使其不适合大型应用：它很慢；你的代码通常结合了应用程序逻辑和显示代码；这使得处理复杂控制流程的大型应用变得非常困难。

随后是各种框架，其中使用最广泛的可能是[CGI::Application](https://metacpan.org/pod/CGI::Application)，它鼓励开发模块化代码，具有易于理解的控制流处理，插件和模板系统的使用等。其他系统包括[AxKit](https://metacpan.org/pod/AxKit)，它是设计用于在mod_perl下运行的XML；[Maypole](https://metacpan.org/pod/Maypole) - Catalyst最初基于的框架 - 专为轻松开发功能强大的Web数据库而设计；[Jifty](https://metacpan.org/pod/Jifty)，在帮助建立具有许多复杂功能的网站方面做了大量的自动化；还有Ruby on Rails（请参阅<http://www.rubyonrails.org>），当然是用Ruby编写的，也是最流行的Web开发系统之一。本文件的目的不是批评或者简要评估这些其他框架；它们可能对您有用，如果是这样，我们鼓励您尝试一下。

### MVC模式

MVC，或者Model-View-Controller，是目前广泛用于Web应用程序的模型。这种设计模式最初来自Smalltalk编程语言。基本思想是应用程序的三个主要区域 - 处理应用程序流（Controller），处理信息（Model）和输出结果（View） - 是分开的，因此可以更改或替换任何一个不影响其他人，所以如果你对一个特定的方面感兴趣，你知道在哪里找到它。

关于MVC的讨论经常退化为关于模式历史的挑剔论证，以及“通常”或“应该”进入控制器或模型的确切内容。我们没有兴趣参加这样的辩论。在任何情况下，Catalyst都不会强制执行任何特定的设置; 您可以自由地在应用程序的任何部分中放置任何类型的代码，此讨论以及Catalyst文档中的其他内容仅是基于我们认为运行良好的建议。在大多数Catalyst应用程序中，MVC的每个分支都将由几个Perl模块组成，这些模块可以处理应用程序中的不同需求。

Model的目的是访问和修改数据。通常，模型将与关系数据库交互，但使用其他数据源（例如[Xapian](https://metacpan.org/pod/Catalyst::Model::Xapian)搜索引擎或LDAP服务器）也很常见。

View的目的是向用户显示数据。典型视图使用模板模块生成HTML代码，使用[Template Toolkit](https://metacpan.org/pod/Template)，[Mason](https://metacpan.org/pod/HTML::Mason)，[HTML::Template](https://metacpan.org/pod/HTML::Template)等，也可以从View生成PDF输出，发送电子邮件等。在Catalyst应用程序中，View通常是一个小模块，只需将其他模块粘贴到Catalyst中；显示逻辑写在模板自身内。

Controller是Catalyst本身。当向Catalyst发出请求时，它将由您的一个Controller模块接收;该模块将确定用户尝试做什么，从Model中收集必要的数据，并将其发送到View中显示。

#### 一个简单的例子

一般的想法是，您应该能够在不影响应用程序其余部分的情况下更改内容。让我们看一个非常简单的例子（请记住，有很多方法可以做到这一点，我们讨论的是一种可能的方式，而不是唯一的方法）。假设您有要显示的记录。如果它是商品，图书馆书籍，音乐CD，人事记录或其他任何内容都没关系，但让我们假设它是一个商品。为用户提供类似`http://www.mysite.com/catalog/display/2782`的URL。怎么办？

首先，Catalyst发现你正在使用“catalog” Controller（Catalyst如何解决这个问题完全取决于你;在Catalyst中，URL调度非常灵活）。然后Catalyst确定您要在“catalog” Controller中使用`display`方法。（在其他控制器中也可能存在其他`display`方法。）在此过程的某个地方，您可能会拥有身份验证和授权例程，以确保用户已注册并允许显示记录。然后Controller的`display`方法将提取“2782”作为您要检索的记录，并向该记录的模型发出请求。然后Controller将查看Model返回的内容：如果没有记录，Controller将要求View显示错误消息，否则它将提交查看记录并要求View显示它。在任何一种情况下，View都将生成一个HTML页面，Catalyst将使用您配置的任何Web服务器将其发送到用户的浏览器。

#### 这对你有什么帮助？

在许多方面。假设您现在有一个小目录，并且您正在使用轻量级数据库，例如SQLite，或者只是一个文本文件。但最终随着你的网站增长，你需要升级到更强大的东西 - MySQL或Postgres，甚至是Oracle或DB2。如果你的Model是独立的，你只需改变一件事，Model；可以预期，如果您的Controller向Model发出查询，它将获得正确的结果。

View怎么样？我们的想法是，您的模板几乎完全与显示有关，因此您可以将其交给不必担心如何编写代码的设计人员。如果您获得Controller中的所有数据，然后将其传递给View，则模板不负责任何类型的数据处理。如果你想改变你的输出，那很简单：只需写一个新的View。如果您的Controller已经获得了所需的数据，您可以以相同的方式传递它，无论是将结果显示到Web浏览器，生成PDF还是将结果通过电子邮件发送给用户，Controller几乎不会更改 - 它取决于View。

在整个过程中，您需要的大多数工具都是Catalyst的一部分（例如，从URL中提取“2782”的参数处理例程）或者很容易插入它（验证例程或使用Template Toolkit插件作为您的视图）。

现在，Catalyst根本没有强制执行。Template Toolkit是一个非常强大的模板系统，您可以连接到数据库，发出查询，并在基于TT的View中对其进行操作。您可以在Controller或模型中处理分页（即仅检索可能的总记录的一部分）。在上面的示例中，您的Controller查看了查询结果，确定是否要求View查看无结果错误消息，或者是否显示结果；但是完全可以将查询结果直接传递给View，让模板决定做什么。由你决定；Catalyst没有强制执行任何操作。

在某些情况下，以某种方式做事情可能有很好的理由（从模板发出数据库查询会破坏关注点分离的整个目的，并会让设计师疯狂），而在其他情况下，这只是个人问题偏好（也许你的模板，而不是你的Controller，是在你得到一个空结果时决定显示什么的更好的地方）。Catalyst只为您提供工具。

# 参见

[Catalyst](https://metacpan.org/pod/Catalyst)，[Catalyst::Manual::Intro](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Intro.pod)
