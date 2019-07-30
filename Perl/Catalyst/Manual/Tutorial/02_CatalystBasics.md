# 名称

Catalyst::Manual::Tutorial::02_CatalystBasics - Catalyst Tutorial - 第2章：Catalyst应用程序开发基础

# 概述

这是Catalyst教程的第2章，共10章。

## [教程概述](README.md)

1. [Introduction](01_Intro.md)
2. Catalyst Basics
3. [More Catalyst Basics](03_MoreCatalystBasics.md)
4. [Basic CRUD](04_BasicCRUD.md)
5. [Authentication](05_Authentication.md)
6. [Authorization](06_Authorization.md)
7. [Debugging](07_Debugging.md)
8. [Testing](08_Testing.md)
9. [Advanced CRUD](09_AdvancedCRUD.md)
10. [Appendices](10_Appendices.md)

# 描述

在本教程的这一章中，我们将创建一个非常基本的Catalyst Web应用程序，演示了许多强大的功能，例如：

- Helper Scripts

  Catalyst助手脚本，可用于快速引导应用程序的框架结构。

- MVC

  Model/View/Controller（MVC）提供了一种架构，有助于在应用程序的不同部分之间实现干净的“控制分离”。鉴于许多其他文档都详细介绍了这个主题，MVC将不在这里深入讨论（有关MVC和一般Catalyst概念的优秀介绍，请参阅[Catalyst::Manual::About](../About.md)）。简而言之：
  
  - Model

    该模型通常表示数据存储。在大多数应用程序中，模型等同于从SQL数据库创建并保存到SQL数据库的对象。
    
  - View

    视图采用模型对象并将其呈现为供最终用户查看的内容。通常，这涉及为用户的Web浏览器创建HTML的模板生成工具，但它可以很容易地生成其他表单的代码，例如PDF文档，电子邮件，电子表格，甚至是“幕后”格式，如XML和JSON。
    
  - Controller

    正如其名称所示，控制器接收用户请求并将它们路由到必要的模型和视图。

- ORM

  使用Object-Relational Mapping（对象关系映射ORM）技术进行数据库访问。具体来说，ORM提供了一种自动化和标准化的方法来持久化和从关系数据库恢复对象，并将自动创建我们的Catalyst模型以用于数据库。

您可以按照[Catalyst::Manual::Tutorial::01_Intro](01_Intro.md)中的说明从catalyst subversion存储库中检查此示例的源代码。

# 创建一个CATALYST项目

Catalyst提供了许多帮助程序脚本，可用于快速充实应用程序的基本结构。所有Catalyst项目都以`catalyst.pl`帮助程序开头（有关帮助程序的更多信息，请参阅[Catalyst::Helper](https://metacpan.org/pod/Catalyst::Helper)）。另请注意，从Catalyst 5.7000开始，除非同时安装[Catalyst::Runtime](https://metacpan.org/pod/Catalyst::Runtime)和[Catalyst::Devel](https://metacpan.org/pod/Catalyst::Devel)，否则不会有辅助脚本。

在本教程的第一章中，使用Catalyst`catalyst.pl`脚本初始化名为`Hello`的应用程序框架：

```shell
$ catalyst.pl Hello
created "Hello"
created "Hello/script"
created "Hello/lib"
created "Hello/root"
...
created "Hello/script/hello_create.pl"
Change to application directory and Run "perl Makefile.PL" to make sure your install is complete
$ cd Hello
```

注意：如果您在Win32上使用Strawberry Perl，请从“catalyst.pl”命令的末尾删除“.pl”，然后使用“catalyst Hello”。

该`catalyst.pl`辅助脚本将显示它创建的目录和文件名称：

```
.
├── Changes               # Record of application changes
├── lib                   # Lib directory for your app's Perl modules
│   ├── Hello             # Application main code directory
│   │   ├── Controller    # Directory for Controller modules 
│   │   ├── Model         # Directory for Models
│   │   └── View          # Directory for Views
│   └── Hello.pm          # Base application module
├── Makefile.PL           # Makefile to build application
├── hello.conf            # Application configuration file
├── README                # README file
├── root                  # Equiv of htdocs, dir for templates, css, javascript
│   ├── favicon.ico
│   └── static            # Directory for static files
│       └── images        # Directory for image files used in welcome screen
├── script                # Directory for Perl scripts
│   ├── hello_cgi.pl      # To run your app as a cgi (not recommended)
│   ├── hello_create.pl   # To create models, views, controllers
│   ├── hello_fastcgi.pl  # To run app as a fastcgi program
│   ├── hello_server.pl   # The normal development server
│   └── hello_test.pl     # Test your app from the command line
└── t                     # Directory for tests
    ├── 01app.t           # Test scaffold       
    ├── 02pod.t           
    └── 03podcoverage.t
```

Catalyst将在Controller，Model和View目录中“自动发现”模块。当您使用该`hello_create.pl`脚本时，它将在这些目录中创建Perl模块框架，以及“t”目录中的测试文件。模板的默认位置位于“root”目录中。脚本目录中的脚本将始终以应用程序名称的小写版本开头。如果你的应用程序是MaiTai，那么创建脚本将是“maitai_create.pl”。

虽然现在对于任何重要的庆祝活动来说还为时过早，但我们已经有了一个功能正常的应用程序。我们可以使用Catalyst提供的脚本启动开发服务器并在浏览器中查看默认的Catalyst页面。脚本目录中的所有脚本都应该从应用程序的基本目录运行，因此请更改为Hello目录。

运行以下命令以启动内置开发Web服务器（确保您没有忘记上一步中的“`cd Hello`”）：

注意：“-r”参数允许在代码更改时重新加载，因此在更新代码时不必停止和启动服务器。查看`perldoc script/hello_server.pl`或`script/hello_server.pl --help`了解您可能会发现有用的其他选项。本教程的其余部分大部分假设您在启动开发服务器时使用“-r”，但如果您愿意，可以随意手动启动和停止它（使用`Ctrl-C`终止开发服务器）。

```shell
$ script/hello_server.pl -r
[debug] Debug messages enabled
[debug] Statistics enabled
[debug] Loaded plugins:
.----------------------------------------------------------------------------.
| Catalyst::Plugin::ConfigLoader  0.30                                       |
'----------------------------------------------------------------------------'
 
[debug] Loaded dispatcher "Catalyst::Dispatcher"
[debug] Loaded engine "Catalyst::Engine"
[debug] Found home "/home/catalyst/Hello"
[debug] Loaded Config "/home/catalyst/Hello/hello.conf"
[debug] Loaded components:
.-----------------------------------------------------------------+----------.
| Class                                                           | Type     |
+-----------------------------------------------------------------+----------+
| Hello::Controller::Root                                         | instance |
'-----------------------------------------------------------------+----------'
 
[debug] Loaded Private actions:
.----------------------+--------------------------------------+--------------.
| Private              | Class                                | Method       |
+----------------------+--------------------------------------+--------------+
| /default             | Hello::Controller::Root              | default      |
| /end                 | Hello::Controller::Root              | end          |
| /index               | Hello::Controller::Root              | index        |
'----------------------+--------------------------------------+--------------'
 
[debug] Loaded Path actions:
.-------------------------------------+--------------------------------------.
| Path                                | Private                              |
+-------------------------------------+--------------------------------------+
| /                                   | /index                               |
| /                                   | /default                             |
'-------------------------------------+--------------------------------------'
 
[info] Hello powered by Catalyst 5.90002
HTTP::Server::PSGI: Accepting connections at http://0:3000/
```

将您的Web浏览器指向`http://localhost:3000`（根据需要替换不同的主机名或IP地址），您应该受到Catalyst欢迎屏幕的欢迎（如果您有其他欢迎屏幕或“索引”屏幕，您可能忘了在你的URL中指定端口3000）。开发服务器的日志记录输出中应有类似于以下内容的信息：

```
[info] Hello powered by Catalyst 5.90002
HTTP::Server::PSGI: Accepting connections at http://0:3000/
[info] *** Request 1 (0.067/s) [19026] [Tue Aug 30 17:24:32 2011] ***
[debug] "GET" request for "/" from "192.168.245.2"
[debug] Path is "/"
[debug] Response Code: 200; Content-Type: text/html; charset=utf-8; Content-Length: 5613
[info] Request took 0.040895s (24.453/s)
.------------------------------------------------------------+-----------.
| Action                                                     | Time      |
+------------------------------------------------------------+-----------+
| /index                                                     | 0.000916s |
| /end                                                       | 0.000877s |
'------------------------------------------------------------+-----------'
```

注意：如有必要，请按`Ctrl-C`以终止开发服务器。

# HELLO WORLD

## 最简单的方法

Root.pm控制器是放置通常在根URL上执行的全局操作的地方。在编辑器中打开`lib/Hello/Controller/Root.pm`文件。您将看到“index”子例程，它负责显示您在浏览器中看到的欢迎屏幕。

```perl
sub index :Path :Args(0) {
    my ( $self, $c ) = @_;
 
    # Hello World
    $c->response->body( $c->welcome_message );
}
```

稍后您将要将其更改为更合理的内容，例如“404”消息或重定向，但现在暂时不管它。

这里的“`$c`”指的是Catalyst上下文，它用于访问Catalyst应用程序。除了许多其他内容之外，Catalyst上下文还提供对“response”和“request”对象的访问。（请参阅[Catalyst::Runtime][Runtime]，[Catalyst::Response][Response]和[Catalyst::Request][Request]）

[Runtime]:https://metacpan.org/pod/Catalyst::Runtime
[Response]:https://metacpan.org/pod/Catalyst::Response
[Request]:https://metacpan.org/pod/Catalyst::Request

`$c->response->body`设置HTTP响应（请参阅[Catalyst::Response][Response]），`$c->welcome_message`是一种返回您在浏览器中看到的欢迎消息的特殊方法。

方法名称后面的“:Path:Args(0)”是确定将哪些URL分派给此方法的属性。（如果您使用的是旧版本的Catalyst，则可能会看到“:Private”，但目前不推荐使用“default”或“index”。如果是这样，您还应该在继续教程之前进行升级。）

一些MVC框架处理中心位置的调度。根据策略，Catalyst更喜欢使用控制器方法上的属性来处理URL调度。指定要匹配的URL有很大的灵活性。此特定方法将匹配所有URL，因为它不指定路径（“Path”之后没有任何内容），但由于“:Args(0)”，它只接受没有任何参数的URL。

URL默认映射到控制器名称，并且由于Perl通过包名称处理命名空间的方式，因此在Catalyst中创建层次结构很简单。这意味着您可以以干净和逻辑的方式创建具有深度嵌套操作的控制器。例如，URL `http://hello.com/admin/articles/create`映射到包`Hello::Controller::Admin::Articles`的`create`方法。

当您在一个窗口中保留运行开发服务器的`script/hello_server.pl -r`命令时（不要忘记最后的“-r”！），打开另一个窗口并将以下子例程添加到您的`lib/Hello/Controller/Root.pm`文件中：

```perl
sub hello :Global {
    my ( $self, $c ) = @_;
 
    $c->response->body("Hello, World!");
}
```

**提示：**有关在从基于POD的文档中剪切和粘贴示例代码时删除前导空格的提示，请参阅附录1。

请注意，在运行Development Server的窗口中，您应该获得类似于以下内容的输出：

```
Saw changes to the following files:
 - /home/catalyst/Hello/lib/Hello/Controller/Root.pm (modify)
 
Attempting to restart the server
...
[debug] Loaded Private actions:
.----------------------+--------------------------------------+--------------.
| Private              | Class                                | Method       |
+----------------------+--------------------------------------+--------------+
| /default             | Hello::Controller::Root              | default      |
| /end                 | Hello::Controller::Root              | end          |
| /index               | Hello::Controller::Root              | index        |
| /hello               | Hello::Controller::Root              | hello        |
'----------------------+--------------------------------------+--------------'
...
```

开发服务器注意到了`Hello::Controller::Root`更改并自动重新启动。

转到`http://localhost:3000/hello`查看“Hello，World！”。另请注意，新定义的“hello”操作列在开发服务器调试输出中的“Loaded Private actions”下。

## Hello, World! 使用视图和模板

在Catalyst世界中，“View”本身不是XHTML的页面或用于向浏览器呈现页面的模板。相反，它是确定视图类型的模块 - HTML，PDF，XML等。对于生成该视图内容的东西（例如Template Toolkit模板文件），实际模板位于“root”目录下。

要创建TT视图，请运行：

```shell
$ script/hello_create.pl view HTML TT
```

这创建了`lib/Hello/View/HTML.pm`模块，它是`Catalyst::View::TT`的子类。

- “view”关键字告诉创建脚本您正在创建视图。
- 第一个参数“HTML”告诉脚本将View模块命名为“HTML.pm”，这是TT视图的常用名称。您可以将其命名为任何名称，例如“MyView.pm”。如果您有多个视图，请务必在Hello.pm中设置default_view（有关设置此内容的更多详细信息，请参阅[Catalyst::View::TT](https://metacpan.org/pod/Catalyst::View::TT)）。
- 最终的“TT”告诉Catalyst 视图的类型，“TT”表示您要使用Template Toolkit视图。

如果你看`lib/Hello/View/HTML.pm`，你会发现它只包含一个配置语句来将TT扩展名设置为“.tt”。

现在HTML.pm“View”存在，Catalyst将自动发现它，并能够使用它从`Catalyst::View::TT`类继承的“process”方法显示视图模板。

Template Toolkit是一个功能非常全面的模板工具，在<http://template-toolkit.org/>上有很好的文档，但由于这不是TT教程，我们将坚持只使用基本的TT（并探讨一些本教程后面章节中更常见的TT功能）。

创建`root/hello.tt`模板文件（将其放在作为应用程序基础`Hello`目录下的`root`中）。这是一个简单的示例：

```html
<p>
    This is a TT view template, called '[% template.name %]'.
</p>
```

[％和％]是模板的TT部分的标记。在里面你可以访问Perl变量和类，并使用TT指令。在这种情况下，我们使用一个特殊的TT变量来定义模板文件的名称（`hello.tt`）。模板的其余部分是普通的HTML。

在`lib/Hello/Controller/Root.pm`中将hello方法更改为以下内容：

```perl
sub hello :Global {
    my ( $self, $c ) = @_;
 
    $c->stash(template => 'hello.tt');
}
```

这一次，不是执行`$c->response->body()`，而是在Catalyst“stash”中设置“template”哈希键的值，这是一个用于放置信息以与应用程序的其他部分共享的区域。“template”键确定在请求周期结束时将显示哪个模板。Catalyst控制器对所有方法都有一个默认的“结束”操作，导致第一个（或默认）视图被呈现（除非有一个`$c->response->body()`语句）。因此，您的模板将在方法结束时神奇地显示出来。

保存文件后，开发服务器应该自动重启（同样，编写教程假设您使用的是“-r”选项 - 如果不是，请手动重新启动它），并再次在您的Web浏览器中输入`http://localhost:3000/hello`查看。您应该看到刚刚创建的模板。

**提示：**如果您在“后台窗口”中使用“-r”运行服务器，请不要让该窗口完全隐藏...如果您的代码中存在语法错误，则调试服务器输出将包含错误信息。

**注：**您可能会遇到上面“stash”语句的变体，如下所示：

```perl
$c->stash->{template} = 'hello.tt';
```

虽然这种风格仍然比较常见，但我们之前使用的方法变得越来越普遍，因为它允许您在一行中设置多个stash变量。例如：

```perl
$c->stash(template => 'hello.tt', foo => 'bar', 
          another_thing => 1);
```

您还可以使用hashref设置多个stash值：

```perl
$c->stash({template => 'hello.tt', foo => 'bar', 
          another_thing => 1});
```

任何这些格式都有效，但`$c->stash(name => value);`风格越来越受欢迎 - 您可能希望始终使用它（即使您只设置单个值）。

## 创建一个简单的控制器和一个动作

通过执行create script创建名为“Site”的控制器：

```shell
$ script/hello_create.pl controller Site
```

这将创建一个`lib/Hello/Controller/Site.pm`文件（和一个测试文件）。如果您在编辑器中打开Site.pm，您会发现没有太多东西可以去看。

在`lib/Hello/Controller/Site.pm`，添加以下方法：

```perl
sub test :Local {
    my ( $self, $c ) = @_;
 
    $c->stash(username => 'John',
              template => 'site/test.tt');
}
```

注意`test`方法的“Local”属性。这将导致`test`操作（现在我们已将“操作类型”分配给它的方法，它显示为对Catalyst的“控制器操作”）在“控制器/方法”URL上执行，或者，在本例中为“site/test”。我们将在本教程的其余部分中看到有关控制器操作的其他信息，但如果您好奇，请查看[Catalyst::Manual::Intro中的“Actions”](../Intro.md#Actions)。

实际上并不像我们这样设置模板值。默认情况下，TT将尝试呈现遵循命名模式“controller/method.tt”的模板，我们在此处遵循该模式。但是，在其他情况下，您需要指定模板（例如，如果您已“forward”到该方法，或者它不遵循默认命名约定）。

我们还将变量“username”放入存储中，以便在模板中使用。

在“root”目录中创建一个子目录“site”。

```shell
$ mkdir root/site
```

在该目录中创建一个名为的新模板文件，root/site/test.tt并包含如下行：

```html
<p>Hello, [% username %]!</p>
```

服务器自动重新启动后，请注意`/site/test`“已加载路径”操作中列出的服务器输出。在浏览器中转到http://localhost:3000/site/test，您应该看到显示的test.tt文件，包括您在控制器中设置的名称“John”。

您可以在此处跳转到本教程的下一章：[More Catalyst Basics](03_MoreCatalystBasics.md)

# 作者

Gerda Shank，`gerda.shank@gmail.com` Kennedy Clark，`hkclark@gmail.com`

如有任何错误或建议，请随时与作者联系，但报告问题的最佳方式是通过<https://rt.cpan.org/Public/Dist/Display.html?Name=Catalyst-Manual>上的CPAN RT Bug系统。

版权所有2006-2011，Kennedy Clark，知识共享署名相同许可协议版本3.0（<http://creativecommons.org/licenses/by-sa/3.0/us/>）。






