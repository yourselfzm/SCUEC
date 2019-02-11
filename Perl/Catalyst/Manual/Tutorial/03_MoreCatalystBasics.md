# 名称

Catalyst::Manual::Tutorial::03_MoreCatalystBasics - Catalyst Tutorial - 第3章：更多Catalyst应用程序开发基础

# 概述

这是Catalyst教程的第3章，共10章。

## [教程概述](README.md)

1. [Introduction](01_Intro.md)
2. [Catalyst Basics](02_CatalystBasics.md)
3. More Catalyst Basics
4. [Basic CRUD](04_BasicCRUD.md)
5. [Authentication](05_Authentication.md)
6. [Authorization](06_Authorization.md)
7. [Debugging](07_Debugging.md)
8. [Testing](08_Testing.md)
9. [Advanced CRUD](09_AdvancedCRUD.md)
10. [Appendices](10_Appendices.md)

# 描述

本教程的这一章以第2章中完成的工作为基础，探索了一些更典型的“真实世界”Web应用程序的功能。从本教程的这一章开始，我们将构建一个简单的书籍数据库应用程序。虽然应用程序太有限，无法用于任何人，但它提供了一个基本环境，我们可以在其中探索几乎所有Web应用程序中使用的各种功能。

教程的源代码包含在教程虚拟机的/home/catalyst/Final目录中（每章一个子目录）。在[Catalyst::Manual::Tutorial::01_Intro](01_Intro.md)中也有下载代码的说明。

在完成本教程的其余部分之前，请先查看[Catalyst::Manual::Tutorial::01_Intro中的“从教程虚拟机开始”](01_Intro.md#cong-shi-yong-jiao-cheng-xu-ni-ji-kai-shi)。虽然本教程应该在任何操作系统上运行的大多数最新版本的Perl下正常工作，但本教程是使用可供下载的虚拟机编写的。整个教程已经过测试，以确保它在此环境中正确运行，因此它是开始使用Catalyst的最无故障的方法。

# 创建一个新的应用程序

本教程的其余部分将构建一个名为`MyApp`的应用程序。首先使用Catalyst `catalyst.pl`脚本初始化`MyApp`应用程序的框架（确保您不在本教程前一章的`Hello`应用程序目录中或已经有“MyApp”子目录的目录中）：

```shell
$ catalyst.pl MyApp
created "MyApp"
created "MyApp/script"
created "MyApp/lib"
created "MyApp/root"
...
created "MyApp/script/myapp_create.pl"
Change to application directory and Run "perl Makefile.PL" to make sure your install is complete
```

并切换到帮助程序创建的“MyApp”目录：

```shell
$ cd MyApp
```

这创建了一个类似于我们在本教程第2章中看到的框架结构，除了`MyApp`和`myapp`替换`Hello`和`hello`。（如第2章所述，如果使用Strawberry Perl，则省略命令中的“.pl”。）

# 编辑CATALYST插件列表

Catalyst的最大好处之一是它拥有如此庞大的基类库和插件库，您可以使用这些库轻松地为应用程序添加功能。插件用于将现有的Perl模块无缝集成到整个Catalyst框架中。通常，他们通过向`context`对象（通常写为`$c`）添加其他方法来执行此操作，Catalyst将传递给整个框架中的每个组件。

看看上面帮助程序创建的`lib/MyApp.pm`文件。默认情况下，Catalyst启用三个插件/Flag：

- `-Debug` Flag

  启用我们之前启动`script/myapp_server.pl`开发服务器时看到的Catalyst调试输出。将应用程序投入生产时，可以删除此项目。
  
  从技术上讲，事实证明`-Debug`不是一个插件，而是一个*Flag*。虽然在应用程序类的`use Catalyst`行上指定的大多数项目都是插件，但Catalyst支持有限数量的Flag选项（其中`-Debug`最常见）。请参阅<https://metacpan.org/module/Catalyst>的文档以获取有关其他标志的详细信息（当前有`-Engine`，`-Home`，`-Log`，和`-Stats`）。
  
  如果您愿意，还有其他几种方法可以启用调试输出：
  
  * Catalyst上下文对象`$c`上的`$c->debug`方法
  * 在`script/myapp_server.pl`脚本上的`-d`选项
  * 环境变量`CATALYST_DEBUG=1`（或`CATALYST_DEBUG=0`临时禁用调试输出）

  **提示：**根据您的需求，从`lib/MyApp.pm`永久删除-Debug，然后使用`script/myapp_server.pl`的-d选项在需要时重新启用它，是很有用处的。我们不会在教程中使用该方法，但您可以在自己的项目中使用它。

- [Catalyst::Plugin::ConfigLoader][ConfigLoader]

[ConfigLoader]:https://metacpan.org/pod/Catalyst::Plugin::ConfigLoader

  `ConfigLoader`提供了一种从中央[Config::General][General]文件为应用程序加载可配置参数的自动方法（与在Perl模块中硬编码的值相比）。[Config::General][General]使用与Apache配置文件非常相似的语法。我们将在catalyst的使用如何在认证和授权功能的部分（[第5章](05_Authentication.md)和[第6章](06_Authorization.md)）中了解。

[General]:https://metacpan.org/pod/Config::General

  **重要说明：**如果您使用的是[Catalyst::Devel][Devel]1.06之前的版本，请注意Catalyst将默认格式从YAML更改为更直接的[`Config::General`][General]样式。本教程使用较新的[`Config::General`][General]`myapp.conf`文件。但是，Catalyst支持这两种格式，并将自动使用`myapp.conf`或`myapp.yml`（或[Catalyst::Plugin::ConfigLoader][ConfigLoader]和[Config::Any][Any]支持的任何其他格式）。如果您使用[Catalyst::Devel][Devel]1.06之前的版本，则只需手动创建`myapp.conf`文件并删除`myapp.yml`即可转换为更新的格式。您创建的`myapp.conf`默认内容应该只包含一行：

[Devel]:https://metacpan.org/pod/Catalyst::Devel
[Any]:https://metacpan.org/pod/Config::Any

  ```
  name MyApp
  ```
  
  **提示：**此脚本可用于在配置格式之间进行转换：
  
  ```
  perl -Ilib -e 'use MyApp; use Config::General;
    Config::General->new->save_file("myapp.conf", MyApp->config);'
  ```

- [Catalyst::Plugin::Static::Simple][Simple]

[Simple]:https://metacpan.org/pod/Catalyst::Plugin::Static::Simple

  `Static::Simple`提供了一种从开发服务器提供静态内容（如图像和CSS文件）的简便方法。

对于我们的应用程序，我们想要添加一个新的插件。要执行此操作，请编辑`lib/MyApp.pm`（此文件通常称为*您的应用程序类*）并删除以下行

```perl
use Catalyst qw/
    -Debug
    ConfigLoader
    Static::Simple
/;
```

然后将其替换为：

```perl
# Load plugins
use Catalyst qw/
    -Debug
    ConfigLoader
    Static::Simple
 
    StackTrace
/;
```

**注：**最新版本[`Catalyst::Devel`][Devel]使用了各种技术来加载这些插件/Flag。例如，您可能会看到以下内容：

```perl
__PACKAGE__->setup(qw/-Debug ConfigLoader Static::Simple/);
```

不要让这些变化让你感到困惑 - 它们都能达到同样的效果。

这告诉Catalyst开始使用一个额外的插件，[Catalyst::Plugin::StackTrace][StackTrace]，在标准Catalyst“调试屏幕”的顶部附近添加堆栈跟踪（当发生错误时，Catalyst将屏幕发送到您的浏览器）。请注意，[StackTrace]输出显示在浏览器中，而不是在运行应用程序的控制台窗口中，这是日志输出通常所在的位置。

[StackTrace]:https://metacpan.org/pod/Catalyst::Plugin::StackTrace

确保在添加新插件时，还将它们作为Makefile.PL文件中的新依赖项包含在内。例如，在添加StackTrace插件后，Makefile.PL应包含以下行：

```perl
requires 'Catalyst::Plugin::StackTrace';
```

**注：**

  * `__PACKAGE__`它只是引用使用它的包名称的简便方法。因此，在`MyApp.pm`，`__PACKAGE__`相当于`MyApp`。
  * 在将应用程序投入生产之前，您需要禁用[StackTrace]，但在开发过程中它可能会有所帮助。
  * 指定插件时，可以在名称中省略`Catalyst::Plugin::`。此外，您可以将插件名称分布在多行中，如前面所示，或将它们全部放在一行上。
  * 如果要查看StackTrace错误屏幕，请编辑`lib/MyApp/Controller/Root.pm`并在`sub index :Path :Args(0)`方法中输入`die "Oops";`命令。然后启动开发服务器并在浏览器中打开`http://localhost:3000/`。你应该得到一个以“`Caught exception in MyApp::Controller::Root->index`”开头的屏幕，其中包含显示堆栈跟踪的部分，有关Request和Response对象的信息，stash（我们将很快了解的内容），以及应用配置。**在继续教程之前，不要忘记删除`the die`！:-)**

# 创建一个CATALYST CONTROLLER

如前所述，控制器是您编写与用户输入交互的方法的地方。通常，控制器方法响应用户从Web浏览器的GET和POST请求。

使用Catalyst `create`脚本，为与书籍相关的操作添加控制器：

```shell
$ script/myapp_create.pl controller Books
 exists "/home/catalyst/MyApp/script/../lib/MyApp/Controller"
 exists "/home/catalyst/MyApp/script/../t"
created "/home/catalyst/MyApp/script/../lib/MyApp/Controller/Books.pm"
created "/home/catalyst/MyApp/script/../t/controller_Books.t"
```

然后编辑`lib/MyApp/Controller/Books.pm`（如在所讨论的教程的[第2章](02_CatalystBasics.md)，catalyst在`lib/MyApp`下为每个MVC的三个部分分配一个单独的目录：`Model`，`View`和`Controller`），并添加以下方法给控制器：

```perl
=head2 list
 
Fetch all book objects and pass to books/list.tt2 in stash to be displayed
 
=cut
 
sub list :Local {
    # Retrieve the usual Perl OO '$self' for this object. $c is the Catalyst
    # 'Context' that's used to 'glue together' the various components
    # that make up the application
    my ($self, $c) = @_;
 
    # Retrieve all of the book records as book model objects and store in the
    # stash where they can be accessed by the TT template
    # $c->stash(books => [$c->model('DB::Book')->all]);
    # But, for now, use this code until we create the model later
    $c->stash(books => '');
 
    # Set the TT template to use.  You will almost always want to do this
    # in your action methods (action methods respond to user input in
    # your controllers).
    $c->stash(template => 'books/list.tt2');
}
```

**提示：**有关在从基于POD的文档中剪切和粘贴示例代码时删除前导空格的提示，请参阅[附录1](10_Appendices.md)。

对面向对象的Perl有经验的程序员应该认识`$self`作为调用此方法的对象的引用。另一方面，`$c`对于之前没有使用过Catalyst的许多Perl程序员来说，这将是全新的。这是“Catalyst Context对象”，它自动作为第二个参数传递给所有Catalyst操作方法。它用于在组件之间传递信息，并提供对Catalyst和插件功能的访问。

Catalyst Controller操作是常规Perl方法，但它们使用属性（上面代码中`“sub list”`旁边的`“ :Local”`）为Catalyst调度程序逻辑提供附加信息（请注意冒号和属性名称之间可以有一个可选空格；您将看到两种方式写入的属性）。大多数Catalyst控制器使用以下五种操作类型之一：

- **:Private** - `:Private`用于要将其作为操作的方法，但您不希望Catalyst直接将该方法公开给您的用户。Catalyst不会将`:Private`方法映射到URI。他们使用各种种类的“特殊”的方法（`begin`，`auto`等，会在下面讨论）或要通过`forward`或`detach`使用的方法。（如果该方法是一个“简单的旧方法”，你根本不想成为一个动作，那么只需定义没有任何属性的方法 - 你可以在你的代码中调用它，但是Catalyst调度程序会忽略它。如果要访问方法中的上下文对象，还必须手动包含`$c`，如果它是一个完整的操作，则Catalyst会自动包含`$c`在参数列表中。）

  有五种类型的“特殊”的内置`:Private`操作：`begin`，`end`，`default`，`index`，和`auto`。
  
  * 随着`begin`，`end`，`default`，`index` private action，只是每种类型的最具体的操作将被调用。例如，如果你在controller定义了一个`begin`操作，将在你的application/root controller*覆盖*一个`begin`操作 - *只有*在你的controller中的操作将被调用。
  * 与为每个请求仅调用单个方法的其他操作不同，将调用沿命名空间链的每个`auto`操作。每个`auto`操作都将被调用，*从the application/root controller到最特定的类*。

- **:Path** - `:Path`actions允许您将方法映射到显式URI路径。例如，`lib/MyApp/Controller/Books.pm`中的“` :Path('list')`” 将匹配URL `http://localhost:3000/books/list`，但“` :Path('/list')`”将匹配`http://localhost:3000/list`（因为前导斜杠）。您可以使用`:Args()`来指定操作应接受的参数数量。有关更多信息和示例，请参阅[Catalyst::Manual::Intro](../Intro.md)中的“Action Types”。

- **:Local** - `:Local`仅仅是“` :Path('_name_of_method_')`” 的简写。例如，这些是等价的：“`sub create_book :Local {...}`”和“`sub create_book :Path('create_book') {...}`”。

- **:Global** - `:Global`仅仅是“` :Path('/_name_of_method_')`” 的简写。例如，这些是等价的：“`sub create_book :Global {...}`”和“`sub create_book :Path('/create_book') {...}`”。

- **:Chained** - 由于其功能和灵活性，较新的Catalyst应用程序倾向于使用链式调度形式的操作类型。它允许在为单个用户请求提供服务时自动分派一系列控制器方法。有关chained actions的更多信息，请参阅[Catalyst::Manual::Tutorial::04_BasicCRUD](04_BasicCRUD.md)和[Catalyst::DispatchType::Chained][Chained]。

[Chained]:https://metacpan.org/pod/Catalyst::DispatchType::Chained

您可以参考[Catalyst::Manual::Intro](../Intro.md)中的“Action-types”以获取更多信息，并了解此处未讨论的一些较少使用的操作类型（`Regex`和`LocalRegex`）。

# CATALYST VIEWS

如本教程的[第2章](02_CatalystBasics.md)所述，视图是您呈现输出的位置，通常用于在用户的Web浏览器中显示（但可以生成其他类型的输出，如PDF或JSON）。`lib/MyApp/View`中的代码选择要使用的视图类型，以及在`root`目录中找到的实际呈现模板。与Catalyst的几乎所有方面一样，当您在应用程序中采用特定的视图技术时，选项也比比皆是。但是，大多数Catalyst应用程序使用模板工具包（称为TT）（有关TT的更多信息，请参阅<http://www.template-toolkit.org>）。其他一些颇受欢迎的视图技术包括Mason（<http://www.masonhq.com>和<http://www.masonbook.com>）和[HTML::Template][Template]（<http://html-template.sourceforge.net>）。

[Template]:https://metacpan.org/pod/HTML::Template

## 创建Catalyst视图

在Catalyst视图中使用TT时，主帮助程序脚本是[Catalyst::Helper::View::TT](https://metacpan.org/pod/Catalyst::Helper::View::TT)。您可能还会遇到对[Catalyst::Helper::View::TTSite](https://metacpan.org/pod/Catalyst::Helper::View::TTSite)的引用，但现在不推荐使用它。

对于我们的书籍应用程序，请输入以下命令以启用`TT`视图呈现样式：

```shell
$ script/myapp_create.pl view HTML TT
 exists "/home/catalyst/MyApp/script/../lib/MyApp/View"
 exists "/home/catalyst/MyApp/script/../t"
 created "/home/catalyst/MyApp/script/../lib/MyApp/View/HTML.pm"
 created "/home/catalyst/MyApp/script/../t/view_HTML.t"
```

这将在一个使用[Catalyst::View::TT](https://metacpan.org/pod/Catalyst::View::TT)（第二个参数）作为“渲染引擎”的名为`HTML.pm`的文件中创建一个名为`HTML`（第一个参数）的视图。

现在由您决定如何构建视图布局。对于本教程，我们将从一个非常简单的TT模板开始，以初步演示概念，但快速迁移到更典型的“wrapper page”类型的配置（其中“wrapper”控制您网站的整体“外观和感觉”，通过单个文件或一组文件）。

编辑`lib/MyApp/View/HTML.pm`，你应该看到类似于以下内容：

```perl
__PACKAGE__->config(
    TEMPLATE_EXTENSION => '.tt',
    render_die => 1,
);
```

并更新它用以匹配：

```perl
__PACKAGE__->config(
    # Change default TT extension
    TEMPLATE_EXTENSION => '.tt2',
    render_die => 1,
);
```

这会将Template Toolkit的默认扩展名从“.tt”更改为“.tt2”。

您还可以在应用程序类中配置组件。例如，编辑`lib/MyApp.pm`，您应该看到调用上方的默认配置`__PACKAGE__->setup`（您的默认值可能会有所不同，具体取决于您使用的Catalyst版本）：

```perl
__PACKAGE__->config(
    name => 'MyApp',
    # Disable deprecated behavior needed by old applications
    disable_component_resolution_regex_fallback => 1,
);
```

更改此项以匹配以下内容（`__PACKAGE__->config`在现有语句下方插入新内容）：

```perl
__PACKAGE__->config(
    name => 'MyApp',
    # Disable deprecated behavior needed by old applications
    disable_component_resolution_regex_fallback => 1,
);
__PACKAGE__->config(
    # Configure the view
    'View::HTML' => {
        #Set the location for TT files
        INCLUDE_PATH => [
            __PACKAGE__->path_to( 'root', 'src' ),
        ],
    },
);
```

这会将模板文件的基本目录`root`更改为`root/src`。

在本教程的持续时间内，请坚持使用上述设置，但随意在应用程序中使用您想要的任何选项（与Perl中的大多数内容一样，有多种方法可以执行此操作...）。

**注：**我们将使用`root/src`作为模板文件的基本目录，其完整命名约定为`root/src/_controller_name_/_action_name_.tt2`。另一个流行的选择是使用`root/`作为基本目录（完整的文件名模式`root/_controller_name_/_action_name_.tt2`）。

## 创建一个TT模板页面

首先为书籍相关的TT模板创建一个目录：

```shell
$ mkdir -p root/src/books
```

然后在编辑器中创建`root/src/books/list.tt2`并输入：

```html
[% # This is a TT comment. -%]
 
[%- # Provide a title -%]
[% META title = 'Book List' -%]
 
[% # Note That the '-' at the beginning or end of TT code  -%]
[% # "chomps" the whitespace/newline at that end of the    -%]
[% # output (use View Source in browser to see the effect) -%]
 
[% # Some basic HTML with a loop to display books -%]
<table>
<tr><th>Title</th><th>Rating</th><th>Author(s)</th></tr>
[% # Display each book in a table row %]
[% FOREACH book IN books -%]
  <tr>
    <td>[% book.title %]</td>
    <td>[% book.rating %]</td>
    <td></td>
  </tr>
[% END -%]
</table>
```

正如上面的内联注释所示，该`META title`行使用TT的META功能为我们稍后创建的“wrapper”提供标题（此时基本上什么都不必做）。同时，`FOREACH`循环遍历每个`book`模型对象并打印`title`和`rating`字段。

`[%`和`%]`标签用于分隔模板工具包代码。TT支持各种循环，条件逻辑等的指令用于“调用”其他文件。通常，TT简化了Perl运算符的常用范围，直到单点（“.”）运算符。这适用于各种操作，如方法调用，哈希查找和列表索引值（有关详细信息和示例，请参阅[Template::Manual::Variables](https://metacpan.org/pod/Template::Manual::Variables)）。除了通常的[Template::Toolkit](https://metacpan.org/pod/Template::Toolkit)模块Pod文档之外，您还可以访问<https://metacpan.org/module/Template::Manual>上的TT手册。

**提示：**虽然您可以在TT模板中构建各种复杂的逻辑，但您通常应该尽可能简化模板中的“代码”部分。如果您需要更复杂的逻辑，请在模型中创建辅助方法，将一组代码抽象为来自TT模板的单个调用。（请注意，控制器逻辑也是如此 - 控制器中复杂的代码段通常应该被拉出并放入模型对象中。）在本教程的[第4章](04_BasicCRUD.md)中，我们将探讨一些非常有用和强大的功能的[DBIx::Class][DBIC]，让你使用其代码的视图和控制器，并理所当然地将其放置在模型类。

[DBIC]:https://metacpan.org/pod/DBIx::Class

## 测试运行应用程序

要测试到目前为止的工作，首先启动开发服务器：

```shell
$ script/myapp_server.pl -r
```

然后将浏览器指向`http://localhost:3000`，您仍然应该获得Catalyst欢迎页面。接下来，将浏览器中的URL更改为`http://localhost:3000/books/list`。如果你到目前为止一切都工作，你应该看到一个网页，除了我们的“标题”，“评级”和“作者”的列标题之外，什么也没有显示 - 在我们获取在下面工作的数据库和模型之前我们不会看到任何书籍。

如果您在使应用程序正确运行时遇到问题，那么参考本教程[“Debugging”](07_Debugging.md)一章中介绍的一些调试技术可能会有所帮助。

# 创建一个SQLITE数据库

在此步骤中，我们使用所需的SQL命令创建一个文本文件，以创建数据库表并加载一些示例数据。我们将使用SQLite（<http://www.sqlite.org>），这是一个轻量级且易于使用的流行数据库。确保至少获得版本3. 在编辑器中打开`myapp01.sql`并输入：

```sql
--
-- Create a very simple database to hold book and author information
--
PRAGMA foreign_keys = ON;
CREATE TABLE book (
        id          INTEGER PRIMARY KEY,
        title       TEXT ,
        rating      INTEGER
);
-- 'book_author' is a many-to-many join table between books & authors
CREATE TABLE book_author (
        book_id     INTEGER REFERENCES book(id) ON DELETE CASCADE ON UPDATE CASCADE,
        author_id   INTEGER REFERENCES author(id) ON DELETE CASCADE ON UPDATE CASCADE,
        PRIMARY KEY (book_id, author_id)
);
CREATE TABLE author (
        id          INTEGER PRIMARY KEY,
        first_name  TEXT,
        last_name   TEXT
);
---
--- Load some sample data
---
INSERT INTO book VALUES (1, 'CCSP SNRS Exam Certification Guide', 5);
INSERT INTO book VALUES (2, 'TCP/IP Illustrated, Volume 1', 5);
INSERT INTO book VALUES (3, 'Internetworking with TCP/IP Vol.1', 4);
INSERT INTO book VALUES (4, 'Perl Cookbook', 5);
INSERT INTO book VALUES (5, 'Designing with Web Standards', 5);
INSERT INTO author VALUES (1, 'Greg', 'Bastien');
INSERT INTO author VALUES (2, 'Sara', 'Nasseh');
INSERT INTO author VALUES (3, 'Christian', 'Degu');
INSERT INTO author VALUES (4, 'Richard', 'Stevens');
INSERT INTO author VALUES (5, 'Douglas', 'Comer');
INSERT INTO author VALUES (6, 'Tom', 'Christiansen');
INSERT INTO author VALUES (7, 'Nathan', 'Torkington');
INSERT INTO author VALUES (8, 'Jeffrey', 'Zeldman');
INSERT INTO book_author VALUES (1, 1);
INSERT INTO book_author VALUES (1, 2);
INSERT INTO book_author VALUES (1, 3);
INSERT INTO book_author VALUES (2, 4);
INSERT INTO book_author VALUES (3, 5);
INSERT INTO book_author VALUES (4, 6);
INSERT INTO book_author VALUES (4, 7);
INSERT INTO book_author VALUES (5, 8);
```

然后使用以下命令构建`myapp.db`SQLite数据库：

```shell
$ sqlite3 myapp.db < myapp01.sql
```

如果需要多次创建数据库，可能需要在使用`sqlite3 myapp.db < myapp01.sql`命令之前发出删除数据库的`rm myapp.db`命令。

一旦`myapp.db`数据库文件被创建和初始化，您可以使用SQLite的命令行环境做的数据库内容的快速转储：

```shell
$ sqlite3 myapp.db
SQLite version 3.7.3
Enter ".help" for instructions
Enter SQL statements terminated with a ";"
sqlite> select * from book;
1|CCSP SNRS Exam Certification Guide|5
2|TCP/IP Illustrated, Volume 1|5
3|Internetworking with TCP/IP Vol.1|4
4|Perl Cookbook|5
5|Designing with Web Standards|5
sqlite> .q
$
```

或者

```shell
$ sqlite3 myapp.db "select * from book"
1|CCSP SNRS Exam Certification Guide|5
2|TCP/IP Illustrated, Volume 1|5
3|Internetworking with TCP/IP Vol.1|4
4|Perl Cookbook|5
5|Designing with Web Standards|5
```

与大多数其他SQL工具一样，如果您使用的是完整的“交互式”环境，则需要使用“;”来终止SQL命令。（如果在命令行上执行单个SQL语句，则不需要）。使用“.q”从SQLite交互模式退出SQLite并返回到OS命令提示符。

请注意，我们选择使用“单数”表名。这是因为旧版本的[DBIx::Class::Schema::Loader][Loader]的默认变形代码不处理复数。关于表名是复数还是单数，已经有很多哲学讨论。没有一个正确的答案，只要有人做出选择并与之保持一致。如果您更喜欢复数表名（例如，您认为它们更容易阅读），请参阅[DBIx::Class::Schema::Loader::Base](https://metacpan.org/pod/DBIx::Class::Schema::Loader::Base#naming)（版本0.05或更高版本）中的“命名”文档。

[Loader]:https://metacpan.org/pod/DBIx::Class::Schema::Loader

有关使用其他数据库（如PostgreSQL或MySQL）的信息，请参阅[附录2](10_Appendices.md)。

# 使用[DBIx::Class][DBIC]进行数据库访问

Catalyst可以与Perl提供的几乎任何形式的数据存储一起使用。例如，[Catalyst::Model::DBI][DBI]可用于通过传统的Perl DBI接口访问数据库，或者您可以使用模型访问文件系统上任何类型的文件。但是，大多数Catalyst应用程序使用某种形式的object-relational mapping（对象关系映射ORM）技术来创建与关系数据库中的表相关联的对象，并且Matt Trout的[DBIx::Class][DBIC]（缩写为“DBIC”）是通常的选择（本教程将使用[DBIx::Class][DBIC]）。

[DBI]:https://metacpan.org/pod/Catalyst::Model::DBI

虽然[DBIx::Class][DBIC]包含了对每次应用程序启动时自动读取数据库结构的`create=dynamic`模式的支持，但不再推荐使用它。虽然它可以用于“华而不实”的演示，但我们在下面使用的`create=static`模式的使用可以同样快速地实现并提供许多优点（例如能够将自己的方法添加到整个DBIC框架中，我们在[第4章](04_BasicCRUD.md)见到的技术）。

## 创建静态DBIx::Class Schema文件

**注意：**如果您没有在Tutorial虚拟机中进行操作，请确保您具有版本1.27或更高版本的DBD::SQLite以及版本0.39或更高版本的[Catalyst::Model::DBIC::Schema][Schema]。（Tutorial VM已经有已知可用的版本。）您可以使用以下命令获取当前安装的版本号。

[Schema]:https://metacpan.org/pod/Catalyst::Model::DBIC::Schema

```shell
$ perl -MCatalyst::Model::DBIC::Schema\ 999
$ perl -MDBD::SQLite\ 999
```

在继续之前，请确保您的`myapp.db`数据库文件位于应用程序的最顶层目录中。现在使用带`create=static`选项的模型助手，用[DBIx::Class::Schema::Loader][Loader]读取数据库，并为我们自动构建所需的文件：

```shell
$ script/myapp_create.pl model DB DBIC::Schema MyApp::Schema \
    create=static dbi:SQLite:myapp.db \
    on_connect_do="PRAGMA foreign_keys = ON"
 exists "/home/catalyst/MyApp/script/../lib/MyApp/Model"
 exists "/home/catalyst/MyApp/script/../t"
Dumping manual schema for MyApp::Schema to directory /home/catalyst/MyApp/script/../lib ...
Schema dump completed.
created "/home/catalyst/MyApp/script/../lib/MyApp/Model/DB.pm"
created "/home/catalyst/MyApp/script/../t/model_DB.t"
```

请注意上面的“`\`”。根据您的环境，您可能能够如图所示剪切和粘贴文本，或者需要删除“`\`”字符以使命令全部在一行上。

该`script/myapp_create.pl`命令分解如下所示：

- `DB`是`lib/MyApp/Model`目录中助手创建的模型类的名称。
- `DBIC::Schema`是要创建的模型的类型。这相当于[Catalyst::Model::DBIC::Schema][Schema]，这是在Catalyst中使用基于DBIC的模型的标准方法。
- `MyApp::Schema`是写入`lib/MyApp/Schema.pm`的DBIC schema文件的名称。
- `create=static`导致[DBIx::Class::Schema::Loader][Loader]在运行时加载schema，然后将该信息写入`lib/MyApp/Schema.pm`和目录`lib/MyApp/Schema`下的文件。
- `dbi:SQLite:myapp.db`是与SQLite一起使用的标准DBI连接字符串。
- 最后，`on_connect_do`字符串请求[DBIx::Class::Schema::Loader][Loader]为我们创建外键关系（这对于PostgreSQL和MySQL等数据库不是必需的，但SQLite需要这样做）。如果您查看一下`lib/MyApp/Model/DB.pm`，您将看到SQLite pragma传播到Model，以便在每个数据库连接的开头SQLite最近启用实施（可选）外键。

如果查看`lib/MyApp/Schema.pm`文件，您会发现它只包含对`load_namespaces`方法的调用。您还会发现`lib/MyApp`包含一个`Schema`子目录，该子目录有一个名为“Result”的子目录。然后，这个“Result”子目录有根据每个在我们简单数据库的表命名的文件（`Author.pm`，`BookAuthor.pm`，和`Book.pm`）。这三个文件在[DBIx::Class][DBIC]命名法中称为“Result Classes”（或“[ResultSource Classes](https://metacpan.org/pod/DBIx::Class::ResultSource)”）。尽管Result Class文件是以数据库中的表命名的，但这些类对应于DBIC返回的*行级*数据（稍后会详细介绍，特别是在[Catalyst::Manual::Tutorial::04_BasicCRUD](04_BasicCRUD.md)中“探索DBIC的强大功能”）。

使用带`create=static`选项的`lib/MyApp/Schema/Result`创建Result Source文件的想法是仅编辑`# DO NOT MODIFY THIS OR ANYTHING ABOVE!`警告下方的文件。如果将所有更改放在文件中的该点之下，则可以在数据库结构更新时在每个文件的顶部重新生成自动创建的信息。

还要注意各种文件和目录中模型信息的“流程”。Catalyst最初会从`lib/MyApp/Model/DB.pm`中加载模型。此文件包含对`lib/MyApp/Schema.pm`文件的引用，以便下次加载该文件。最后，调用`Schema.pm`的`load_namespacesin`将从`lib/MyApp/Schema/Result`子目录中加载每个“Result Class”文件。最终结果是Catalyst将在每次应用程序启动时动态创建三个特定表的Catalyst模型（您可以看到启动应用程序时生成的调试输出中列出的这三个模型文件）。

此外，`lib/MyApp/Schema.pm`模型可以在Catalyst外部轻松加载，例如，在命令行实用程序和/或cron作业中。`lib/MyApp/Model/DB.pm`在Catalyst和这个外部数据库模型之间提供了一个非常薄的“桥梁”。一旦你看到我们如何在[第4章](04_BasicCRUD.md)中为我们的DBIC模型添加一些强大的功能，这种方法的优雅将开始变得更加明显。

**注：**较早版本的[Catalyst::Model::DBIC::Schema][Schema]使用不再推荐使用的[DBIx::Class][DBIC]`load_classes`技术而不是较新的`load_namespaces`。对于新的应用程序，请尝试使用`load_namespaces`，因为它更容易支持称为“ResultSet类”的非常有用的DBIC技术。如果你需要对现有应用程序从“`load_classes`”转换至“`load_namespaces`”，您可以使用此过程自动化的迁移，但首先要确保你有版本0.39的[Catalyst::Model::DBIC::Schema][Schema]和版本0.05000或更高版本的[DBIx::Class::Schema::Loader][Loader]。

```perl
$ # Re-run the helper to upgrade for you
$ script/myapp_create.pl model DB DBIC::Schema MyApp::Schema \
    create=static naming=current use_namespaces=1 \
    dbi:SQLite:myapp.db \
    on_connect_do="PRAGMA foreign_keys = ON"
```

# 启用CONTROLLER中的模型

打开`lib/MyApp/Controller/Books.pm`并取消注释我们之前禁用的模型代码，以便您的版本与以下内容匹配（取消注释包含的行`[$c->model('DB::Book')->all]`并删除接下来的2行）：

```perl
=head2 list
 
Fetch all book objects and pass to books/list.tt2 in stash to be displayed
 
=cut
 
sub list :Local {
    # Retrieve the usual Perl OO '$self' for this object. $c is the Catalyst
    # 'Context' that's used to 'glue together' the various components
    # that make up the application
    my ($self, $c) = @_;
 
    # Retrieve all of the book records as book model objects and store
    # in the stash where they can be accessed by the TT template
    $c->stash(books => [$c->model('DB::Book')->all]);
 
    # Set the TT template to use.  You will almost always want to do this
    # in your action methods (action methods respond to user input in
    # your controllers).
    $c->stash(template => 'books/list.tt2');
}
```

**提示：**您可能会看到`$c->model('DB::Book')`上面未注释的内容为`$c->model('DB')->resultset('Book')`。两者是等价的。无论哪种方式，`$c->model`都返回一个[DBIx::Class::ResultSet][ResultSet]，它处理对数据库的查询并迭代返回的结果集。

[ResultSet]:https://metacpan.org/pod/DBIx::Class::ResultSet

我们正在使用`->all`来获取所有书籍。DBIC支持各种更高级的操作，可轻松执行过滤和排序结果等操作。例如，以下内容可用于通过降序标题对结果进行排序：

```perl
$c->model('DB::Book')->search({}, {order_by => 'title DESC'});
```

[DBIx::Class::Manual::Cookbook的"Complex WHERE clauses"](https://metacpan.org/pod/DBIx::Class::Manual::Cookbook#Complex-WHERE-clauses)中提供了一些其他的例子，更多信息参考[DBIx::Class::ResultSet的"search"](https://metacpan.org/pod/DBIx::Class::ResultSet#search)，[DBIx::Class::Manual::FAQ的"Searching"](https://metacpan.org/pod/DBIx::Class::Manual::FAQ#Searching)， [DBIx::Class::Manual::Intro](../Intro.md)和[Catalyst::Model::DBIC::Schema][Schema]。

# 测试运行应用程序

首先，让我们启用一个环境变量，使[DBIx::Class][DBIC]用于转储访问数据库的SQL语句。当您尝试调试面向数据库的代码时，这是一个有用的技巧。按下`Ctrl-C`以终止开发服务器并输入：

```shell
$ export DBIC_TRACE=1
$ script/myapp_server.pl -r
```

这假设您使用bash作为shell - 如果您使用不同的shell（例如，在tcsh下，使用`setenv DBIC_TRACE 1`），则需相应地进行调整。

**注：**您也可以使用代码在代码中进行设置`$class->storage->debug(1);`。有关详细信息，请参阅[DBIx::Class::Manual::Troubleshooting](https://metacpan.org/pod/DBIx::Class::Manual::Troubleshooting)（包括日志记录到文件而不是显示到Catalyst开发服务器日志的选项）。

然后启动Catalyst开发服务器。日志输出应显示如下内容：

```shell
$ script/myapp_server.pl -r
[debug] Debug messages enabled
[debug] Statistics enabled
[debug] Loaded plugins:
.----------------------------------------------------------------------------.
| Catalyst::Plugin::ConfigLoader  0.30                                       |
| Catalyst::Plugin::StackTrace  0.11                                         |
'----------------------------------------------------------------------------'
 
[debug] Loaded dispatcher "Catalyst::Dispatcher"
[debug] Loaded engine "Catalyst::Engine"
[debug] Found home "/home/catalyst/MyApp"
[debug] Loaded Config "/home/catalyst/MyApp/myapp.conf"
[debug] Loaded components:
.-----------------------------------------------------------------+----------.
| Class                                                           | Type     |
+-----------------------------------------------------------------+----------+
| MyApp::Controller::Books                                        | instance |
| MyApp::Controller::Root                                         | instance |
| MyApp::Model::DB                                                | instance |
| MyApp::Model::DB::Author                                        | class    |
| MyApp::Model::DB::Book                                          | class    |
| MyApp::Model::DB::BookAuthor                                    | class    |
| MyApp::View::HTML                                               | instance |
'-----------------------------------------------------------------+----------'
 
[debug] Loaded Private actions:
.----------------------+--------------------------------------+--------------.
| Private              | Class                                | Method       |
+----------------------+--------------------------------------+--------------+
| /default             | MyApp::Controller::Root              | default      |
| /end                 | MyApp::Controller::Root              | end          |
| /index               | MyApp::Controller::Root              | index        |
| /books/index         | MyApp::Controller::Books             | index        |
| /books/list          | MyApp::Controller::Books             | list         |
'----------------------+--------------------------------------+--------------'
 
[debug] Loaded Path actions:
.-------------------------------------+--------------------------------------.
| Path                                | Private                              |
+-------------------------------------+--------------------------------------+
| /                                   | /default                             |
| /                                   | /index                               |
| /books                              | /books/index                         |
| /books/list                         | /books/list                          |
'-------------------------------------+--------------------------------------'
 
[info] MyApp powered by Catalyst 5.80020
HTTP::Server::PSGI: Accepting connections at http://0:3000
```

**注：**确保从应用程序的“base”目录运行`script/myapp_server.pl`命令，而不是在`script`目录本身内运行，否则它将无法找到`myapp.db`数据库文件。您可以使用完全限定或相对路径来查找数据库文件，但是在我们运行模型助手之前没有指定。

您应该在上面的输出中注意一些事项：

- [Catalyst::Model::DBIC::Schema][Schema]动态地创建在我们的数据库中三个模型类，一个代表数据库中的这三个表（`MyApp::Model::DB::Author`，`MyApp::Model::DB::BookAuthor`和`MyApp::Model::DB::Book`）。
- 我们的Books Controller中的“list” action通过路径`/books/list`显示。

将浏览器指向`http://localhost:3000`，您仍然应该获得Catalyst欢迎页面。

接下来，要查看图书清单，请将浏览器中的URL更改为`http://localhost:3000/books/list`。您应该获得`myapp01.sql`脚本加载的五本书的列表，而不进行任何格式化。每本书的评级应出现在每一行，但“作者”栏仍然是空白的（我们将在稍后填写）。

还要注意`script/myapp_server.pl`的输出，[DBIx::Class][DBIC]使用下面的SQL检索数据：

```
SELECT me.id, me.title, me.rating FROM book me
```

因为我们启用了DBIC_TRACE。

您现在拥有一个简单但可行的Web应用程序的开头。继续讨论未来的部分，我们将更全面地开发应用程序。

# 为VIEW创建WRAPPER

使用TT时，您可以（并且应该）创建一个包装器，它将在每个模板周围包装内容。这当然很有用，因为您有一个主要来源可以更改整个站点/应用程序中显示的内容，而不必编辑许多单个文件。

## 为WRAPPER配置HTML.pm

要创建包装器，必须先编辑TT视图并告诉它在哪里找到包装器文件。

编辑`lib/MyApp/View/HTML.pm`的TT视图并更改它，以匹配以下内容： 

```perl
__PACKAGE__->config(
    # Change default TT extension
    TEMPLATE_EXTENSION => '.tt2',
    # Set the location for TT files
    INCLUDE_PATH => [
            MyApp->path_to( 'root', 'src' ),
        ],
    # Set to 1 for detailed timer stats in your HTML as comments
    TIMER              => 0,
    # This is your wrapper template located in the 'root/src'
    WRAPPER => 'wrapper.tt2',
);
```

## 创建WRAPPER模板文件和样式表

接下来，您需要设置包装器模板。基本上，您需要采用网站的整体布局并将其放入此文件中。对于本教程，请打开`root/src/wrapper.tt2`并输入以下内容：

```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" [%#
    %]"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
<head>
<title>[% template.title or "My Catalyst App!" %]</title>
<link rel="stylesheet" href="[% c.uri_for('/static/css/main.css') %]" />
</head>
 
<body>
<div id="outer">
<div id="header">
    [%# Your logo could go here -%]
    <img src="[% c.uri_for('/static/images/btn_88x31_powered.png') %]" />
    [%# Insert the page title -%]
    <h1>[% template.title or site.title %]</h1>
</div>
 
<div id="bodyblock">
<div id="menu">
    Navigation:
    <ul>
        <li><a href="[% c.uri_for('/books/list') %]">Home</a></li>
        <li><a href="[% c.uri_for('/')
            %]" title="Catalyst Welcome Page">Welcome</a></li>
    </ul>
</div><!-- end menu -->
 
<div id="content">
    [%# Status and error messages %]
    <span class="message">[% status_msg %]</span>
    <span class="error">[% error_msg %]</span>
    [%# This is where TT will stick all of your template's contents. -%]
    [% content %]
</div><!-- end content -->
</div><!-- end bodyblock -->
 
<div id="footer">Copyright (c) your name goes here</div>
</div><!-- end outer -->
 
</body>
</html>
```

请注意上面代码中的状态和错误消息部分：

```html
<span class="status">[% status_msg %]</span>
<span class="error">[% error_msg %]</span>
```

如果我们在Catalyst stash中设置任一消息（例如`$c->stash->{status_msg} = 'Request was successful!'`），则只要呈现该请求使用的任何视图，就会显示该消息。通过我们在下面创建的`root/static/css/main.css`文件，可以定制`message`和`error`CSS样式，以满足需求。

**注：**

- Catalyst存储仅持续一个HTTP请求。如果您需要跨请求保留信息，可以使用[Catalyst::Plugin::Session][Session]（我们将在本教程的Authentication章节中使用Catalyst会话）。
- 虽然它超出了本教程的范围，但您可能希望使用JavaScript或AJAX工具，例如jQuery（http://www.jquery.com）或Dojo（http://www.dojotoolkit.org）。

[Session]:https://metacpan.org/pod/Catalyst::Plugin::Session

### 创建基本样式表

首先在静态目录下为样式表创建一个中心位置：

```shell
$ mkdir root/static/css
```

然后打开文件root/static/css/main.css（上面我们的包装器的样式表href链接中引用的文件）并添加以下内容：

```html
#header {
    text-align: center;
}
#header h1 {
    margin: 0;
}
#header img {
    float: right;
}
#footer {
    text-align: center;
    font-style: italic;
    padding-top: 20px;
}
#menu {
    font-weight: bold;
    background-color: #ddd;
}
#menu ul {
    list-style: none;
    float: left;
    margin: 0;
    padding: 0 0 50% 5px;
    font-weight: normal;
    background-color: #ddd;
    width: 100px;
}
#content {
    margin-left: 120px;
}
.message {
    color: #390;
}
.error {
    color: #f00;
}
```

您可能希望查看像“Emastic”这样的“CSS框架”（<http://code.google.com/p/emastic/>），以快速提供大量高质量的CSS功能。

## 测试运行应用程序

在您的网络浏览器中点击“重新加载”，您现在应该看到我们的基本图书清单的格式化版本。（同样，开发服务器应该自动重新启动当您进行更改`lib/MyApp/View/HTML.pm`时。如果您不使用“-r”选项，您需要点击`Ctrl-C`并手动重新启动它。还要注意的是开发服务器*不*需要重新启动，当更改我们在root目录中创建和编辑的TT和静态文件 - 这些更新是基于每个请求进行处理的。）

虽然我们的包装器和样式表显然非常简单，但您应该看到它如何允许我们从两个中央文件控制整个网站的整体外观。要向站点添加新页面，只需提供一个填充我们的包装器模板`content`部分的模板 - 包装器将提供页面的整体感觉。

## 更新生成的[DBIx::Class][DBIC]结果类文件

如果你看一下[DBIx::Class::Schema::Loader][Loader]自动生成的Schema文件，你会看到它已经在我们的外键的每一面定义了`has_many`和`belongs_to`关系。例如，看一看`lib/MyApp/Schema/Result/Book.pm`并注意以下代码：

```perl
=head1 RELATIONS
 
=head2 book_authors
 
Type: has_many
 
Related object: L<MyApp::Schema::Result::BookAuthor>
 
=cut
 
__PACKAGE__->has_many(
  "book_authors",
  "MyApp::Schema::Result::BookAuthor",
  { "foreign.book_id" => "self.id" },
  { cascade_copy => 0, cascade_delete => 0 },
);
```

每个`Book`“has_many”`book_authors`，其中`BookAuthor`是多对多表，允许每本书有多个作者，每个作者有多本书。参数`has_many`是：

- `book_authors` - 这种关系的名称。DBIC将使用此名称在`Books`DBIC Row对象上创建一个访问器。
- `MyApp::Schema::Result::BookAuthor` - 此`has_many`关系引用的DBIC模型类的名称。
- `foreign.book_id` - `book_id`是外部表的外键列名，它指向这个表。
- `self.id` - `id`是此表中由外键引用的列的名称。

有关其他信息，请参阅[DBIx::Class::Relationship中的“has_many”](https://metacpan.org/pod/DBIx::Class::Relationship#has_many)。请注意，您可能会看到上述`has_many`关系的“hand coded”版本表示为：

```perl
__PACKAGE__->has_many(
  "book_authors",
  "MyApp::Schema::Result::BookAuthor",
  "book_id",
);
```

其中第三个参数只是外表中列的名称。但是，[DBIx::Class::Schema::Loader][Loader]使用的hashref语法更灵活（例如，它可以处理“多列外键”）。

**注：**如果您使用的是旧版本的SQLite和相关的DBIC工具，则需要手动定义`has_many`和`belongs_to`关系。我们建议升级到上面指定的版本。:-)

看看`lib/MyApp/Schema/Result/BookAuthor.pm`并注意定义的`belongs_to`关系充当了我们刚才看到的`has_many`关系的“镜像” ：

```perl
=head1 RELATIONS
 
=head2 book
 
Type: belongs_to
 
Related object: L<MyApp::Schema::Result::Book>
 
=cut
 
__PACKAGE__->belongs_to(
  "book",
  "MyApp::Schema::Result::Book",
  { id => "book_id" },
  { join_type => "LEFT", on_delete => "CASCADE", on_update => "CASCADE" },
);
```

参数类似，但请参阅[DBIx::Class::Relationship中的“belongs_to”](https://metacpan.org/pod/DBIx::Class::Relationship#belongs_to)以获取详细信息。

虽然SQLite和[DBIx::Class::Schema::Loader][Loader]的最新版本自动处理`has_many`和`belongs_to`关系，但是当前需要手动插入`many_to_many`关系桥（在技术上不是关系）。要添加`many_to_many`关系桥，请首先编辑`lib/MyApp/Schema/Result/Book.pm`并在`# You can replace this text...`注释下添加以下文本：

```perl
# many_to_many():
#   args:
#     1) Name of relationship bridge, DBIC will create accessor with this name
#     2) Name of has_many() relationship this many_to_many() is shortcut for
#     3) Name of belongs_to() relationship in model class of has_many() above
#   You must already have the has_many() defined to use a many_to_many().
__PACKAGE__->many_to_many(authors => 'book_authors', 'author');
```

**注：**小心把这段代码上面的`1;`放在文件的结尾。与任何Perl包一样，我们需要使用一个计算结果为`true`的语句来结束最后一行。这通常是在单独一行上的`1;`完成。

该`many_to_many`关系桥是可选的，但它可以更容易地映射书其作者集。没有它，我们将不得不“遍历” `book_author`表格,像在`$book->book_author->first->author->last_name`中一样（我们将看到关于如何在代码中尽快使用[DBIx::Class][DBIC]对象的示例，但请注意，因为`$book->book_author`可以返回多个作者，我们必须使用`first`显示单一作者）。`many_to_many`允许我们使用更短的`$book->author->first->last_name`。请注意，如果没有`has_many`关系，则无法定义`many_to_many`关系桥。

然后编辑`lib/MyApp/Schema/Result/Author.pm`并添加反向`many_to_many`关系桥，`Author`如下所示（再次注意放在`# DO NOT MODIFY THIS OR ANYTHING ABOVE!`注释下方和`1;`上面）：

```perl
# many_to_many():
#   args:
#     1) Name of relationship bridge, DBIC will create accessor with this name
#     2) Name of has_many() relationship this many_to_many() is shortcut for
#     3) Name of belongs_to() relationship in model class of has_many() above
#   You must already have the has_many() defined to use a many_to_many().
__PACKAGE__->many_to_many(books => 'book_authors', 'book');
```

## 运行应用程序

使用`DBIC_TRACE`选项运行Catalyst开发服务器脚本（它可能仍在本教程前面的版本中启用，但以防万一，这里是另一种指定跟踪选项的方法）：

```shell
$ DBIC_TRACE=1 script/myapp_server.pl -r
```

确保应用程序正确加载，并且您可以看到三个动态创建的模型类（我们创建的每个结果类都有一个）。

然后使用浏览器点击URL`http://localhost:3000/books/list`，并确保图书列表仍然正确显示。

**注：**您还没有看到作者，因为视图没有利用这些关系。请继续阅读我们更新模板的下一部分。

# 更新视图

让我们在我们的图书列表页面添加一个新列，它利用我们手动添加到上一节中的模式文件的关系信息。编辑`root/src/books/list.tt2`并使用以下内容并替换“空”表格单元格“`<td></td>`”：

```html
...
<td>
  [% # NOTE: See Chapter 4 for a better way to do this!                      -%]
  [% # First initialize a TT variable to hold a list.  Then use a TT FOREACH -%]
  [% # loop in 'side effect notation' to load just the last names of the     -%]
  [% # authors into the list. Note that the 'push' TT vmethod doesn't return -%]
  [% # a value, so nothing will be printed here.  But, if you have something -%]
  [% # in TT that does return a value and you don't want it printed, you     -%]
  [% # 1) assign it to a bogus value, or                                     -%]
  [% # 2) use the CALL keyword to call it and discard the return value.      -%]
  [% tt_authors = [ ];
     tt_authors.push(author.last_name) FOREACH author = book.authors %]
  [% # Now use a TT 'virtual method' to display the author count in parens   -%]
  [% # Note the use of the TT filter "| html" to escape dangerous characters -%]
  ([% tt_authors.size | html %])
  [% # Use another TT vmethod to join & print the names & comma separators   -%]
  [% tt_authors.join(', ') | html %]
</td>
...
```

**重要说明：**同样，您应该在视图之外保留尽可能多的“逻辑代码”。这种逻辑属于您的模型（控制器也是如此 - 尽可能保持“thin”并将所有“复杂代码”推送到模型对象中）。避免使用你在上一个例子中看到的代码 - 我们只是在这里使用它来显示TT中的一些额外功能，直到我们得到更高级的模型功能，我们将在第4章中看到（参见 "EXPLORING THE POWER OF DBIC" in Catalyst::Manual::Tutorial::04_BasicCRUD）。

然后在浏览器中点击“Reload”（注意您不需要重新加载开发服务器或在更新TT模板时使用`-r`选项），现在您应该看到每本书的作者数量以及以逗号分隔的列表作者的姓氏。（如果您没有让开发服务器从上一步开始运行，您显然需要在刷新浏览器窗口之前启动它。）

如果您仍在运行`DBIC_TRACE`启用的开发服务器，您现在还应该在调试输出中看到另外五个`SELECT`语句（每个书一个，因为DBIx::Class检索出作者）：

```
SELECT me.id, me.title, me.rating FROM book me:
SELECT author.id, author.first_name, author.last_name FROM book_author me  
JOIN author author ON author.id = me.author_id WHERE ( me.book_id = ? ): '1'
SELECT author.id, author.first_name, author.last_name FROM book_author me  
JOIN author author ON author.id = me.author_id WHERE ( me.book_id = ? ): '2'
SELECT author.id, author.first_name, author.last_name FROM book_author me  
JOIN author author ON author.id = me.author_id WHERE ( me.book_id = ? ): '3'
SELECT author.id, author.first_name, author.last_name FROM book_author me  
JOIN author author ON author.id = me.author_id WHERE ( me.book_id = ? ): '4'
SELECT author.id, author.first_name, author.last_name FROM book_author me  
JOIN author author ON author.id = me.author_id WHERE ( me.book_id = ? ): '5'
```

另请注意`root/src/books/list.tt2`，我们使用“`| html`”（一种TT过滤器）将`<`和`>`之类的字符转义为`&lt;`和`&gt;`并避免各种类型的危险黑客攻击您的应用程序。在实际应用程序中，您可能希望在每个字段的末尾放置“| html”，用户可以控制该字段中可能出现的信息（如果不“neutralize”那些字段，则可以注入标记或代码。）除了“`| html`”之外，Template Toolkit还有许多其他有用的过滤器，可以在[Template::Filters](https://metacpan.org/pod/Template::Filters)的文档中找到。（虽然我们的主题是安全性和逃避危险值，但使用DBIC等工具进行数据库访问或[HTML::FormFu](https://metacpan.org/pod/HTML::FormFu)进行表单管理[参考[第九章](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Tutorial/09_AdvancedCRUD/09_FormFu.pod)]，是他们为你自动处理大多数逃逸，因此大大提高了应用程序的安全性。）

# 从命令行运行应用程序

在某些情况下，运行应用程序并在不使用浏览器的情况下显示页面会很有用。Catalyst允许您使用`script/myapp_test.pl`脚本执行此操作。只需提供您希望显示的URL，它将通过普通的控制器调度逻辑运行该请求，并使用适当的视图来呈现输出（显然，复杂的页面可能会将大量文本转储到您的终端窗口）。例如，如果`Ctrl+C`终止开发服务器之后，则键入：

```shell
$ script/myapp_test.pl "/books/list"
```

您应该获得与使用普通开发服务器访问`http://localhost:3000/books/list`相同的文本，并要求浏览器查看页面源。您甚至可以使用以下命令将此HTML文本输出传输到基于文本的浏览器：

```shell
$ script/myapp_test.pl "/books/list" | lynx -stdin
```

您应该会看到一个完全呈现的基于文本的页面视图。（如果您在Debian 6中进行操作，请键入`sudo aptitude -y install lynx`以安装lynx。）如果您确实启动了lynx，则可以使用“Q”键退出。

# 可选信息

**注意：本教程本章的其余部分是可选的。如果您愿意，可以跳到[第4章基本CRUD](04_BasicCRUD.md)。**

## 使用“RenderView”作为默认视图

一旦您的控制器逻辑处理了来自用户的请求，它就会将处理转发到您的视图，以生成适当的响应输出。默认情况下，Catalyst使用[Catalyst::Action::RenderView](https://metacpan.org/pod/Catalyst::Action::RenderView)自动执行此操作。如果您查看`lib/MyApp/Controller/Root.pm`，您应该看到`sub end`方法的空定义：

```perl
sub end : ActionClass('RenderView') {}
```

以下要点提供了该`RenderView`过程的快速概述：

- `Root.pm`旨在保持应用程序范围的逻辑。
- 在给定用户请求结束时，Catalyst将调用适当最具体的`end`方法。例如，如果请求的控制器定义了一个`end`方法，则会调用它。但是，如果控制器未定义特定于控制器的`end`方法，则将调用`Root.pm`中的“global”`end`方法。
- 因为定义包含一个`ActionClass`属性，所以在运行`sub end`定义中的任何代码**之后**将执行[Catalyst::Action::RenderView](https://metacpan.org/pod/Catalyst::Action::RenderView)逻辑。有关`ActionClass`更多信息，请参阅[Catalyst::Manual::Actions](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Actions.pod)。
- 因为`sub end`是空的，所以这实际上只运行默认逻辑`RenderView`。但是，当我们第一次运行初始化应用程序`catalyst.pl`时，您可以通过在Catalyst Helpers创建RenderView的空方法体（`{}`）中添加自己的代码来轻松扩展逻辑。关于如何在`sub end`扩展`RenderView`更详细的信息见[Catalyst::Action::RenderView](https://metacpan.org/pod/Catalyst::Action::RenderView)。

## RenderView的“dump_info”功能

`RenderView`其中一个很好的功能是它可以自动添加`dump_info=1`到应用程序的任何URL的末尾，它将强制显示“exception dump”屏幕到客户端浏览器。您可以通过将浏览器指向此URL来尝试此操作：

```
http://localhost:3000/books/list?dump_info=1
```

您应该在顶部获得一个包含以下消息的页面：

```
Caught exception in MyApp::Controller::Root->end "Forced debug - 
Scrubbed output at /usr/share/perl5/Catalyst/Action/RenderView.pm line 46."
```

以及在处理该请求结束时应用程序状态的摘要。“Stash”部分应显示DBIC书籍模型对象的摘要版本。如果需要，您可以调整摘要逻辑（称为“scrubbing”逻辑） - 有关详细信息，请参阅[Catalyst::Action::RenderView][RenderView]。

[RenderView]:https://metacpan.org/pod/Catalyst::Action::RenderView

请注意，您不必担心使用此技术“normal clients”来“reverse engineer”您的应用程序 - `RenderView`仅在您的应用程序以`-Debug`模式运行时支持该`dump_info=1`功能（部署应用程序后您将无法在生产中执行此操作）。

## 使用默认模板名称

默认情况下，`Catalyst::View::TT`将查找与控制器操作使用相同名称的模板，允许您保存在每个操作中手动指定模板名称的步骤。例如，这将允许我们在Books控制器list操作中删除`$c->stash->{template} = 'books/list.tt2';`行。在您的编辑器中打开`lib/MyApp/Controller/Books.pm`并注释掉该行以匹配以下内容（仅更改了`$c->stash->{template}`行）：

```perl
=head2 list
 
Fetch all book objects and pass to books/list.tt2 in stash to be displayed
 
=cut
 
sub list :Local {
    # Retrieve the usual Perl OO '$self' for this object. $c is the Catalyst
    # 'Context' that's used to 'glue together' the various components
    # that make up the application
    my ($self, $c) = @_;
 
    # Retrieve all of the book records as book model objects and store in the
    # stash where they can be accessed by the TT template
    $c->stash(books => [$c->model('DB::Book')->all]);
 
    # Set the TT template to use.  You will almost always want to do this
    # in your action methods (actions methods respond to user input in
    # your controllers).
    #$c->stash(template => 'books/list.tt2');
}
```

您现在应该能够像以前一样访问`http://localhost:3000/books/list`URL。

**注：**如果使用默认模板技术，则**无法**使用`$c->forward`或`$c->detach`机制（这些将在教程的第2章和第9章中讨论）。

**重要信息：**在继续下一章4基本CRUD之前，请确保**不要**跳过以下部分。

## 返回手动指定的模板

为了在后面的教程中能够使用`$c->forward`和`$c->detach`，你应该从`lib/MyApp/Controller/Books.pm`的`sub list`语句中删除注释：

```perl
$c->stash(template => 'books/list.tt2');
```

然后删除`lib/MyApp/View/HTML.pm`中的`TEMPLATE_EXTENSION`行。

检查浏览器中的`http://localhost:3000/books/list`URL。它应该与前面部分看起来相同。

您可以在此处跳转到本教程的下一章：[Basic CRUD](04_BasicCRUD.md)

# 作者

Kennedy Clark, `hkclark@gmail.com`

如有任何错误或建议，请随时与作者联系，但报告问题的最佳方式是通过<https://rt.cpan.org/Public/Dist/Display.html?Name=Catalyst-Manual>上的CPAN RT Bug系统。

Copyright 2006-2011, Kennedy Clark, under the Creative Commons Attribution Share-Alike License Version 3.0 (<http://creativecommons.org/licenses/by-sa/3.0/us/>).







