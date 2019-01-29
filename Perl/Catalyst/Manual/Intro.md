# 名称

Catalyst::Manual::Intro - Catalyst简介

# 描述

这是Catalyst的简要介绍。它解释了Catalyst工作原理的最重要特性，并展示了如何让简单应用快速启动和运行。有关Catalyst本身的介绍（无代码）以及您应该使用它的原因，请参阅[Catalyst::Manual::About](About.md)。有关使用Catalyst编写应用程序的系统分步介绍，请参阅[Catalyst::Manual::Tutorial](Tutorial.md)。

## 什么是Catalyst？

Catalyst是一个优雅的Web应用程序框架，非常灵活且非常简单。它类似于Ruby on Rails，Spring（Java）和[Maypole][Maypole](它最初基于它)。其最重要的设计理念是提供对开发Web应用程序所需的所有工具的轻松访问，而对您需要使用这些工具的方式几乎没有限制。但是，这确实意味着总是可以以不同的方式做事。其他Web框架最初使用起来比较简单，但是通过将程序员锁定到一组工具中来实现这一点。Catalyst强调灵活性意味着您必须更多地考虑使用它。我们将此视为一项功能。例如，这导致Catalyst比其他Web框架更适合于系统集成任务。

[Maypole]:https://metacpan.org/pod/Maypole

### <span id="mvc">MVC</span>

Catalyst遵循Model-View-Controller（MVC）设计模式，允许您轻松地将关注点（如内容，表示和流控制）分离到单独的模块中。这种分离允许您修改处理一个问题的代码，而不会影响处理其他问题的代码。Catalyst促进了现有Perl模块的重用，这些模块已经很好地处理了常见的Web应用程序问题。

以下是Model，View和Controller如何映射到这些关系，以及您可能想要为每个模块使用的众所周知的Perl模块的示例。

- Model

  访问和修改内容（数据）。[DBIx::Class][DBIx]，[Class::DBI][DBI]，[Xapian][Xapian]，[Net::LDAP][LDAP]...

[DBIx]:https://metacpan.org/pod/DBIx::Class
[DBI]:https://metacpan.org/pod/Class::DBI
[Xapian]:https://metacpan.org/pod/Xapian
[LDAP]:https://metacpan.org/pod/Net::LDAP



- View

  向用户呈现内容。[Template Toolkit][TT]，[Mason][Mason]，[HTML::Template][Template]...
  
[TT]:https://metacpan.org/pod/Template
[Mason]:https://metacpan.org/pod/HTML::Mason
[Template]: https://metacpan.org/pod/HTML::Template

- Controller

  控制整个请求阶段，检查参数，调度动作，流量控制。这是Catalyst工作的核心。

如果你不熟悉MVC和设计模式，你可能想看看关于这个主题的原始书，*《Design Patterns》*，Gamma，Helm，Johnson和Vlissides所著，也被称为*Gang of Four*（GoF）。许多Web应用程序框架都基于MVC，它正在成为万维网的流行设计范例。

### 灵活性

Catalyst比许多其他框架更灵活。您可以放心将自己喜欢的Perl模块与Catalyst配合使用。

- 多个Models, Views, and Controllers

  要构建Catalyst应用程序，您需要处理名为[“组件（Components）”](#component)的特殊模块中的每种类型的关注点。通常这段代码非常简单，只需调用Perl模块，如上面[“MVC”](#mvc)中列出的那些。Catalyst以非常灵活的方式处理这些组件。根据需要使用尽可能多的模型，视图和控制器，使用尽可能多的不同Perl模块，所有这些都在同一个应用程序中。想要操纵多个数据库，并通过LDAP检索一些数据？没问题。想要使用[Template Toolkit][TT]和[PDF::Template][PDF]来呈现来自同一模型的数据吗？简单。

[PDF]:https://metacpan.org/pod/PDF::Template

- 可重复使用的组件

  Catalyst不仅促进了现有Perl模块的重用，还允许您在多个Catalyst应用程序中重用Catalyst组件。

- 无限制的URL-to-Action调度

  Catalyst允许您将任何URL分发到任何应用程序[“操作(Actions)”](#action)，甚至通过正则表达式！与大多数其他框架不同，它不需要URL中的mod_rewrite或类和方法名称。
  
  使用Catalyst，您可以注册您的actions并直接调用它们。例如：

   ```perl
sub hello : Local {
    my ( $self, $context ) = @_;
    $context->response->body('Hello World!');
}
```

  现在`http://localhost:3000/hello` 输出 "Hello World!"
  
  注意，具有该`:Local`属性的操作等同于使用`:Path('action_name')`属性，因此我们可以等效操作如下：
  
  ```perl
sub hi : Path('hello') {
    my ( $self, $context ) = @_;
    $context->response->body('Hello World!');
}
```

- 支持CGI，mod_perl，Apache :: Request，FastCGI

  使用[Catalyst::Engine::Apache][Apache]或[Catalyst::Engine::CGI][CGI]。另一个有趣的引擎是[Catalyst::Engine::HTTP::Prefork][Prefork] - 可以单独从CPAN获得 - 它将构建的服务器变成一个完全成熟的生产就绪服务器（如果你最终使用它，你可能希望在前端代理后面运行它）。

[Apache]:https://metacpan.org/pod/Catalyst::Engine::Apache
[CGI]:https://metacpan.org/pod/Catalyst::Engine::CGI
[Prefork]:https://metacpan.org/pod/Catalyst::Engine::HTTP::Prefork
  
- PSGI支持

  从Catalyst 5.9版开始，Catalyst附带[PSGI][PSGI]集成，可提供更强大，更灵活的测试和部署选项。有关详细信息，请参阅[Catalyst::PSGI][CPSGI]。

[PSGI]:https://metacpan.org/pod/PSGI
[CPSGI]:https://metacpan.org/pod/Catalyst::PSGI

### 简便性

最好的部分是Catalyst以非常简单的方式实现所有这些灵活性。
  
- 构建块接口

  组件非常顺畅地互操作。例如，Catalyst自动为每个组件提供[“Context”](#context)对象。通过context，您可以访问请求对象，在组件之间共享数据，以及控制应用程序的流程。构建一个Catalyst应用程序感觉很像将玩具构建块拼凑在一起，而且一切正常。

- 组件自动发现

  无需`use`所有组件。Catalyst自动查找并加载它们。

- 流行模块的预制组件

  请参阅为[DBIx::Class][DBIx]预制的[Catalyst::Model::DBIC::Schema][Schema]，或为[Template Toolkit][TT]预制的[Catalyst::View::TT][CTT]。

[Schema]:https://metacpan.org/pod/Catalyst::Model::DBIC::Schema
[CTT]:https://metacpan.org/pod/Catalyst::View::TT

- 内置测试框架

  Catalyst附带了一个内置的轻量级http服务器和测试框架，可以轻松地从Web浏览器和命令行测试应用程序。

- 帮助程序脚本

  Catalyst提供帮助程序脚本，以快速生成组件和单元测试的运行入门代码。安装[Catalyst::Devel][Devel]并查看[Catalyst::Helper][Helper]。

[Devel]:https://metacpan.org/pod/Catalyst::Devel
[Helper]:https://metacpan.org/pod/Catalyst::Helper

### 快速开始

以下是使用上述帮助程序脚本安装Catalyst并启动并运行简单应用程序的方法。

#### 安装

Catalyst的安装很简单：

```shell
# perl -MCPAN -e 'install Catalyst::Runtime'
# perl -MCPAN -e 'install Catalyst::Devel'
# perl -MCPAN -e 'install Catalyst::View::TT'
```

#### 建立

```shell
$ catalyst.pl MyApp
# output omitted
$ cd MyApp
$ script/myapp_create.pl controller Library::Login
```

#### Frank Speiser的Amazon EC2 Catalyst SDK

目前有两种公开可用的亚马逊机器映像（AMI），包括您需要在几分钟内在功能齐全的Catalyst环境中开始开发所需的所有元素。有关详细信息，请参阅[Catalyst::Manual::Installation](https://metacpan.org/pod/Catalyst::Manual::Installation)。

#### 运行

```shell
$ script/myapp_server.pl
```

现在，使用您喜欢的浏览器或用户代理访问这些位置，以查看Catalyst的运行情况：

（注：虽然我们在这里创建了一个控制器，但我们实际上并没有使用它。这两个URL都会带你到欢迎页面。）

##### http://localhost:3000/

##### http://localhost:3000/library/login/

### 它如何工作

通过仔细研究Catalyst应用程序的组件（component）和其他部分，让我们看看Catalyst的工作原理。

#### <span id="component">components</span>

Catalyst具有非常灵活的组件系统。您可以根据需要定义任意数量的[“Models”](#Models)，[“Views”](#Views)和[“Controllers”](#Controllers)。如前所述，一般的想法是View负责向用户输出数据（通常通过Web浏览器，但View也可以生成PDF或电子邮件）；Model负责提供数据（通常来自关系数据库）；Controller负责与用户交互，并决定用户输入如何确定应用程序采取的操作。

在MVC的世界中，关于每个元素的性质经常存在讨论和分歧 - 某些类型的逻辑是否属于模型或控制器等。Catalyst的灵活性意味着这个决定完全取决于你，程序员；Catalyst没有强制执行任何操作。有关这些问题的一般性讨论，请参阅[Catalyst::Manual::About](About.md)。

Model，View和Controller组件必须分别从[Catalyst::Model](https://metacpan.org/pod/Catalyst::Model)，[Catalyst::View](https://metacpan.org/pod/Catalyst::View)和[Catalyst::Controller](https://metacpan.org/pod/Catalyst::Controller)继承。反过来，这些继承自[Catalyst::Component](https://metacpan.org/pod/Catalyst::Component)，它提供了一个简单的类结构和一些常见的类方法，如`config`和`new`（构造函数）。

```perl
package MyApp::Controller::Catalog;
use Moose;
use namespace::autoclean;
 
BEGIN { extends 'Catalyst::Controller' }
 
__PACKAGE__->config( foo => 'bar' );
 
1;
```

您不必以`use`或其他方式注册Models，Views和Controllers。当您在主应用程序中调用`setup`时，Catalyst会自动发现并实例化它们。您需要做的就是将它们放在为每个Component类型命名的目录中。您可以为每个使用短别名。

- MyApp/Model/
- MyApp/View/
- MyApp/Controller/

#### <span id="Views">Views</span>

为了演示如何定义视图，我们将使用已有的基础类用于[Template Toolkit][TT]，[Catalyst::View::TT][CTT]。我们需要做的就是继承这个类：

```perl
package MyApp::View::TT;
 
use strict;
use base 'Catalyst::View::TT';
 
1;
```

（您也可以使用帮助程序脚本自动生成它：

```shell
script/myapp_create.pl view TT TT
```

第一个`TT`告诉脚本view的名称是`TT`，第二个说明它是Template Toolkit视图。）

这给了我们一个process()方法，我们现在只需要执行`$c->forward('MyApp::View::TT')`来渲染我们的模板。基类使得process()是隐式的，所以我们不必说`$c->forward(qw/MyApp::View::TT process/)`。

```perl
sub hello : Global {
    my ( $self, $c ) = @_;
    $c->stash->{template} = 'hello.tt';
}
 
sub end : Private {
    my ( $self, $c ) = @_;
    $c->forward( $c->view('TT') );
}
```

您通常在请求结束时呈现模板，因此它非常适合全局（global）`end`操作。

但实际上，您将使用[Catalyst::Action::RenderView][RenderView]提供的缺省`end`操作。

[RenderView]:https://metacpan.org/pod/Catalyst::Action::RenderView

另外，请务必将模板放在指定的目录下`$c->config->{root}`，否则最终将会看到调试屏幕。

#### <span id="Models">Models</span>

Models是数据的提供者。这些数据可以来自任何地方 - 搜索引擎索引，电子表格，文件系统 - 但通常Model表示数据库表。数据源本质上与Web应用程序或Catalyst无关 - 它可以很容易地用于编写脱机报表生成器或命令行工具。

为了展示如何定义Models，我们将再次使用已经存在的基类，这次是针对[DBIx::Class][DBIx]，[Catalyst::Model::DBIC::Schema][Schema]。我们还需要[DBIx::Class::Schema::Loader][Loader]。

[Loader]:https://metacpan.org/pod/DBIx::Class::Schema::Loader

首先，我们需要一个数据库。

```shell
-- myapp.sql
CREATE TABLE foo (
    id INTEGER PRIMARY KEY,
    data TEXT
);
 
CREATE TABLE bar (
    id INTEGER PRIMARY KEY,
    foo INTEGER REFERENCES foo,
    data TEXT
);
 
INSERT INTO foo (data) VALUES ('TEST!');
 
% sqlite3 /tmp/myapp.db < myapp.sql
```

现在我们可以为这个数据库创建一个DBIC::Schema模型。

```shell
script/myapp_create.pl model MyModel DBIC::Schema MySchema create=static 'dbi:SQLite:/tmp/myapp.db'
```

[DBIx::Class::Schema::Loader][Loader]可以自动加载表格布局和关系，并将它们转换为静态模式定义MySchema，以后可以编辑。

使用`stash`将数据传递到模板。

我们将以下内容添加到`MyApp/Controller/Root.pm`中:

```perl
sub view : Global {
    my ( $self, $c, $id ) = @_;
 
    $c->stash->{item} = $c->model('MyModel::Foo')->find($id);
}
 
1;
 
sub end : Private {
    my ( $self, $c ) = @_;
 
    $c->stash->{template} ||= 'index.tt';
    $c->forward( $c->view('TT') );
}
```

然后我们创建一个新的模板文件`root/index.tt`，其中包含：

```
The Id's data is [% item.data %]
```

Models不是Catalyst应用程序的必要部分; 您可以随时调用外部模块作为您的Model：

```perl
# in a Controller
sub list : Local {
  my ( $self, $c ) = @_;
 
  $c->stash->{template} = 'list.tt';
 
  use Some::Outside::Database::Module;
  my @records = Some::Outside::Database::Module->search({
    artist => 'Led Zeppelin',
    });
 
  $c->stash->{records} = \@records;
}
```

但是通过使用作为Catalyst应用程序一部分的Model，您可以获得以下几点：您不必需要`use`每个组件，Catalyst将在编译时自动查找并加载它；你可以通过`forward`使用模块，这只能用于Catalyst组件。只能使用`$c->model('SomeModel')`获取Catalyst组件。

令人高兴的是，由于许多人都有他们想要与Catalyst一起使用的现有Model类（或者相反，他们想要编写可以在Catalyst之外使用的Catalyst Model，例如在cron作业中），在外部Model中编写一个简单的Catalyst组件是容易的。

```perl
package MyApp::Model::DB;
use base qw/Catalyst::Model::DBIC::Schema/;
__PACKAGE__->config(
    schema_class => 'Some::DBIC::Schema',
    connect_info => ['dbi:SQLite:foo.db', '', '', {AutoCommit=>1}]
);
1;
```

就是这样！现在`Some::DBIC::Schema`是像`MyApp::Model::DB`一样的Catalyst应用程序的一部分。

在Catalyst中，为应用程序编写Model的常用方法是使用包含配置数据，便捷方法等的对象包装通用Model（例如DBIx::Class::Schema，一堆XML，或者其他任何东西）等等。因此，您实际上将拥有两个Model - 一个了解Catalyst和Web应用程序的包装Model，以及完全独立于这些需求的通用Model。

从技术上讲，在Catalyst中，Model是一个组件 - 属于应用程序的model类的实例。重要的是要强调这些对象的生命周期是每个应用程序，而不是每个请求。

虽然model基类（[Catalyst::Model](https://metacpan.org/pod/Catalyst::Model)）提供了`config`将模型更好地集成到应用程序中的功能，但有时这还不够，model需要访问`$c`自身。

可能出现这种情况的需求包括：

- 与另一个model交互
- 使用每个请求数据来控制行为
- 使用Model中的插件（例如[Catalyst::Plugin::Cache](https://metacpan.org/pod/Catalyst::Plugin::Cache)）

从风格的角度来看，通常认为使model“过于聪明”的不良形式 - 它应该担心业务逻辑并将集成细节留给controller。但是，如果您发现在Model周围使用辅助controller根本没有意义，并且Model访问`$c`的需求无法回避，则存在一个名为[“ACCEPT_CONTEXT”](#ACCEPT_CONTEXT)的有效工具。

#### <span id="Controllers">Controllers</span>

多个controllers是分离应用程序逻辑域的好方法。

```perl
package MyApp::Controller::Login;
 
use base qw/Catalyst::Controller/;
 
sub sign_in : Path("sign-in") { }
sub new_password : Path("new-password") { }
sub sign_out : Path("sign-out") { }
 
package MyApp::Controller::Catalog;
 
use base qw/Catalyst::Controller/;
 
sub view : Local { }
sub list : Local { }
 
package MyApp::Controller::Cart;
 
use base qw/Catalyst::Controller/;
 
sub add : Local { }
sub update : Local { }
sub order : Local { }
```

请注意，只要在要导出的子参数上至少有一个属性（`:Action`通常用于此操作），您也可以通过Controller的配置提供属性 - 例如，以下内容相当于上面的同一个controller：

```perl
package MyApp::Controller::Login;
 
use base qw/Catalyst::Controller/;
 
__PACKAGE__->config(
  actions => {
    'sign_in' => { Path => 'sign-in' },
    'new_password' => { Path => 'new-password' },
    'sign_out' => { Path => 'sign-out' },
  },
);
 
sub sign_in : Action { }
sub new_password : Action { }
sub sign_out : Action { }
```

#### <span id="ACCEPT_CONTEXT">ACCEPT_CONTEXT</span>

每当你调用`$c->component("Foo")`时，你都会得到一个对象 - model的实例。如果组件支持该`ACCEPT_CONTEXT`方法而不是返回model本身，则将使用返回值`$model->ACCEPT_CONTEXT( $c )`。

这意味着无论何时model/view/controller需要`$c`与之交谈，都有机会在需要时进行此操作。

一种典型的`ACCEPT_CONTEXT`方法是克隆model并返回一个带有上下文对象集的model，否则它将返回一个包含`$c`并委托给每个应用程序模型对象的瘦包装器。

通常，在模型或视图代码中公开上下文对象（`$c`）是个坏主意。相反，您使用ACCEPT_CONTEXT子例程来获取所需的上下文对象的位，并在模型中为它​​们提供访问器。这确保了`$c`仅在需要的范围内，这减少了维护和调试的麻烦。因此，如果您在同一个Catalyst模型代码中需要两个[Catalyst::Model::DBIC::Schema][Schema]模型，则可能会执行以下操作：

```perl
__PACKAGE__->mk_accessors(qw(model1_schema model2_schema));
sub ACCEPT_CONTEXT {
    my ( $self, $c, @extra_arguments ) = @_;
    $self = bless({ %$self,
            model1_schema  => $c->model('Model1')->schema,
            model2_schema => $c->model('Model2')->schema
        }, ref($self));
    return $self;
}
```

这有效地将`$self`视为获取新参数的**原型对象**。`@extra_arguments`来自任何尾随参数`$c->component( $bah, @extra_arguments )`（或`$c->model(...)`，`$c->view(...)`等）。

在model代码的子例程中，我们可以这样做：

```perl
sub whatever {
    my ($self) = @_;
    my $schema1 = $self->model1_schema;
    my $schema2 = $self->model2_schema;
    ...
}
```

请注意，我们仍然希望Catalyst models成为可以独立于Catalyst应用程序工作类的瘦包装器，以提高代码的可重用性。在这里，我们可能只想获取`$c->model('DB')->schema`，以便从Catalyst应用程序的配置中获取连接信息。

该值的生命周期是**每次使用**，而不是每次请求。要根据每次请求进行此操作，您可以使用以下技术：

添加一个字段`$c`，比如`my_model_instance`。然后编写您的`ACCEPT_CONTEXT`方法，如下所示：

```perl
sub ACCEPT_CONTEXT {
  my ( $self, $c ) = @_;
 
  if ( my $per_request = $c->my_model_instance ) {
    return $per_request;
  } else {
    my $new_instance = bless { %$self, c => $c }, ref($self);
    Scalar::Util::weaken($new_instance->{c}); # or we have a circular reference
    $c->my_model_instance( $new_instance );
    return $new_instance;
  }
}
```

有关在每个请求上获取新组件实例的类似技术，请参阅[Catalyst::Component::InstancePerContext](https://metacpan.org/pod/Catalyst::Component::InstancePerContext)。

#### Application Class

除了Model，View和Controller组件之外，还有一个代表您的应用程序本身的类。您可以在此处配置应用程序，加载插件和扩展Catalyst。

```perl
package MyApp;
 
use strict;
use parent qw/Catalyst/;
use Catalyst qw/-Debug ConfigLoader Static::Simple/;
MyApp->config(
    name => 'My Application',
 
    # You can put anything else you want in here:
    my_configuration_variable => 'something',
);
1;
```

在旧版本的Catalyst中，应用程序类是放置全局操作的位置。但是，从版本5.66开始，建议的做法是将此类操作放在特殊的Root Controller中（请参阅下面的[“Actions”](#Actions)），以避免命名空间冲突。

- name

  应用的名称。

您也可以选择，为模板和静态数据指定**root**参数。如果省略，Catalyst将尝试自动检测目录的位置。您可以根据需要为插件或任何需要定义任意数量的参数。您可以通过应用程序的任何位置访问它们`$context->config->{$param_name}`。

#### Context

Catalyst会自动将Context对象保存到您的应用程序类中，并使其在您的应用程序中的任何位置都可用。使用Context直接与Catalyst交互并将[“Components”](#component)粘合在一起。例如，如果您需要在Template Toolkit模板中使用Context，它已经存在，可以直接调用，如下所示：

```html
<h1>Welcome to [% c.config.name %]!</h1>
```

如我们的URL-to-Action调度示例所示，Context始终是第二个方法参数，位于Component对象引用或类名本身之后。为了清晰所示，之前我们称之为`$context`，但大多数Catalyst开发人员只是称之为`$c`：

```perl
sub hello : Global {
    my ( $self, $c ) = @_;
    $c->res->body('Hello World!');
}
```

Context包含几个重要对象：

- [Catalyst::Request](https://metacpan.org/pod/Catalyst::Request)

  ```perl
$c->request
$c->req # alias
```

  request对象包含各种特定请求的信息，如查询参数，cookie，upload，header等。
  
  ```perl
$c->req->params->{foo};
$c->req->cookies->{sessionid};
$c->req->headers->content_type;
$c->req->base;
$c->req->uri_with( { page = $pager->next_page } );
```

- [Catalyst::Response](https://metacpan.org/pod/Catalyst::Response)

  ```perl
$c->response
$c->res # alias
```

  response与request类似，但仅包含特定响应的信息。
  
  ```perl
$c->res->body('Hello World');
$c->res->status(404);
$c->res->redirect('http://oook.de');
```

- config

  ```perl
$c->config
$c->config->{root};
$c->config->{name};
```

- [Catalyst::Log](https://metacpan.org/pod/Catalyst::Log)

  ```perl
$c->log
$c->log->debug('Something happened');
$c->log->info('Something you should know');
```

- Stash

  ```perl
$c->stash
$c->stash->{foo} = 'bar';
$c->stash->{baz} = {baz => 'qox'};
$c->stash->{fred} = [qw/wilma pebbles/];
```

  等等。

最后一个，stash，是用于在应用程序组件之间共享数据的通用哈希。举个例子，我们回到'hello' action：

```perl
sub hello : Global {
    my ( $self, $c ) = @_;
    $c->stash->{message} = 'Hello World!';
    $c->forward('show_message');
}
 
sub show_message : Private {
    my ( $self, $c ) = @_;
    $c->res->body( $c->stash->{message} );
}
```

请注意，stash应仅用于在单个请求周期中传递数据; 它会在新的请求中被清除。如果需要维护持久数据，请使用会话。有关一组全面的Catalyst友好会话处理工具，请参阅[Catalyst::Plugin::Session][Session]。

[Session]:https://metacpan.org/pod/Catalyst::Plugin::Session

#### <span id="Actions">Actions</span>

您已经在本文档中看到了一些action示例：附加了`:Path`和`:Local`属性的子例程。在这里，我们解释了什么是行为以及这些属性如何影响正在发生的事情。

当Catalyst处理网页请求时，它会查找要处理传入请求并产生响应（如网页）的操作。您可以通过在controller中编写子例程并使用特殊属性对其进行标记来为应用程序创建这些操作。属性，命名空间和函数名称确定Catalyst何时调用子例程。

这些action子例程调用某些函数来说明Web服务器将给Web请求的响应。他们还可以告诉Catalyst在请求上运行其他操作（其中一个例子称为转发请求；稍后将对此进行讨论）。

Action子例程必须具有特殊属性才能显示它们是操作 - 以及标记何时调用它们，这表明它们采用一组特定的参数并以特定方式运行。在启动时，Catalyst会查找控制器中的所有操作，注册它们并创建描述它们的[Catalyst::Action][Action]对象。当请求进入时，Catalyst会选择应该调用哪些操作来处理请求。

[Action]:https://metacpan.org/pod/Catalyst::Action

（有时，您可能直接使用action对象，但一般来说，当我们谈论action时，我们讨论的是应用程序中用于处理请求的子例程。）

您可以为action子例程选择几个属性之一；这些指定了该子例程处理的请求。Catalyst将查看它正在处理的URL及其找到的action，并自动调用它找到的与请求的环境匹配的action。

URL（例如`http://localhost:3000/foo/bar`）由两部分组成，基础：描述如何连接到服务器（`http://localhost:3000/`），和路径：服务器使用该路径来决定返回什么（`foo/bar`）。请注意，`hostname[:port]`之后的尾部斜杠`/`始终属于base而不是路径。在尝试查找要处理的action时，Catalyst仅使用路径部分。

根据所使用的action类型，URL可以匹配controller namespace，传递给action属性的参数和子例程的名称的组合。

- **Controller namespaces**

  namespace是组件的类（包）名称的修改形式。此修改后的类名不包括Catalyst中具有预定义含义的部分（上例中的“MyApp::Controller”），将“::”替换为“/”，并将名称转换为小写。有关Catalyst component类名称的预定义含义的完整说明，请参阅[“components”](#component)。

- **Overriding the namespace**

  请注意，`__PACKAGE__->config->(namespace => ... )`可以在匹配时用于覆盖当前namespace。所以：

  ```perl
  package MyApp::Controller::Example;
  ```

  通常会使用'example'作为匹配的namespace，但如果经过特别重写
  
  ```perl
  __PACKAGE__->config( namespace => 'thing' );
  ```

  它将使用`'thing'`来匹配namespace。

- **Application-Wide Actions**

  由catalyst.pl脚本创建的MyApp::Controller::Root通常包含为应用程序的顶层调用的action（例如`http://localhost:3000/`）：
  
  ```perl
  package MyApp::Controller::Root;
  use base 'Catalyst::Controller';
  
  # Sets the actions in this controller to be registered with no prefix
  # so they function identically to actions created in MyApp.pm

  __PACKAGE__->config( namespace => '');
  
  sub default : Path  {
      my ( $self, $context ) = @_;
      $context->response->status(404);
      $context->response->body('404 not found');
  }

  1;
  ```

  代码
  
  ```perl
  __PACKAGE__->config( namespace => '' );
  ```

  使controller的行为就像它的namespace是空的一样。正如您将在下面看到的，空命名空间会产生许多URL匹配属性，例如`:path`和`:Local`匹配开头的URL路径（即应用程序根目录）。
  
#### Action types

Catalyst支持多种类型的action。这些主要对应于将URL与action子例程匹配的方式。在内部，这些匹配类型由[Catalyst::DispatchType](https://metacpan.org/pod/Catalyst::DispatchType)派生的类实现；那些文档可以帮助我们了解它们的工作原理。

他们都会尝试匹配路径的开头。路径的其余部分作为参数传递。

- Namespace-prefixed (`:Local`)

  ```perl
  package MyApp::Controller::My::Controller;
  sub foo : Local { }
  ```
  
  匹配以>`http://localhost:3000/my/controller/foo`开头的任何网址。namespace和子例程名称一起确定路径。

- Root-level (`:Global`)

  ```perl
  package MyApp::Controller::Foo;
  
  sub bar : Global {
      my ($self, $c) = @_;
      $c->res->body(
        $c->res->body('sub bar in Controller::Foo triggered on a request for '
                       . $c->req->uri));
  }
  
  1;
  ```
  
  匹配`http://localhost:3000/bar` - 即，action直接映射到方法名称，忽略controller namespace。
  
  `:Global`始终匹配应用程序根目录：它只是`:Path('/methodname')`的简写。`:Local`是`:Path('methodname')`的简写，它采用如上所述的controller namespace。
  
  `Global`的使用，除了在非常旧的Catalyst应用程序（例如在Catalyst 5.7之前）之外，处理程序的使用很少见。`Global`过去有意义的用例现在很大程度上被`Chained`调度类型或controller action的空`Path`声明所取代。`Global`虽然合法的用例可能仍然存在，但它仍然包含在Catalyst中以实现向后兼容。
  
- 改变处理程序行为：eating arguments (`:Args`)

  `:Args`不是动作类型本身，而是动作修饰符 - 它为其提供的任何动作添加匹配限制，另外需要为要匹配的action指定的路径部分。例如，在MyApp::Controller::Foo中，
  
  ```perl
  sub bar :Local
  ```
  
  将匹配任何以`/foo/bar`开始的URL。要限制这个，你可以做
  
  ```perl
  sub bar :Local :Args(1)
  ```
  
  仅匹配以`/foo/bar/*`开头的网址 - 在'bar'后需要一个额外的路径元素。
  
  请注意，添加`:Args(0)`和遗漏`:Args`是**不**一样的东西。
  
  `:Args(0)`意味着不采取任何参数。因此，URL和路径必须精确匹配。
  
  无`:Args`，这意味着有**任何数量**的理由。因此，任何以controller路径**开始**的URL都将匹配。显然，这意味着你不能从没有指定args的动作链接，因为链中的下一个动作将作为第一个arg！

- Literal match (`:Path`)
  
  `Path` action匹配以精确指定路径开始的事物，而不是其他事物。
  
  `Path`没有前导斜杠的action匹配相对于其当前namespace的指定路径。下列示例匹配以`http://localhost:3000/my/controller/foo/bar`开头的网址：
  
  ```perl
  package MyApp::Controller::My::Controller;
  sub bar : Path('foo/bar') { }
  ```
  
  以斜线开头`Path` action忽视他们的namespace，并匹配从开始的URL路径。例：
  
  ```perl
  package MyApp::Controller::My::Controller;
  sub bar : Path('/foo/bar') { }
  ```
  
  这匹配以`http://localhost:3000/foo/bar`开头的URL网址。
  
  空`Path`定义仅匹配命名空间，与`:Global`完全相同。
  
  ```perl
  package MyApp::Controller::My::Controller;
  sub bar : Path { }
  ```
  
  以上代码匹配`http://localhost:3000/my/controller`。
  
  具有该`:Local`属性的action同样等同于`:Path('action_name')`：
  
  ```perl
  sub foo : Local { }
  ```
  
  相当于
  
  ```perl
  sub foo : Path('foo') { }
  ```

- Pattern match (:Regex and :LocalRegex)

  **状态：已弃用。**使用链式方法或其他技术。如果您真的依赖于此，请安装独立的[Catalyst::DispatchType::Regex](https://metacpan.org/pod/Catalyst::DispatchType::Regex)发行版。
  
  ```perl
  package MyApp::Controller::My::Controller;
  sub bar : Regex('^item(\d+)/order(\d+)$') { }
  ```
  
  这匹配与action key中的模式匹配的任何URL，例如`http://localhost:3000/item23/order42`。正则表达式周围的''是可选的，但perltidy喜欢它。:)
  
  `:Regex`匹配全局action，即不参考调用它们的namespace。所以上面的内容不匹配`http://localhost:3000/my/controller/item23/order42` - 改为使用`:LocalRegex` action。
  
  ```perl
  package MyApp::Controller::My::Controller;
  sub bar : LocalRegex('^widget(\d+)$') { }
  ```
  
  `:LocalRegex` action在本地起作用，即首先匹配namespace。以上示例将匹配网址`http://localhost:3000/my/controller/widget23`。
  
  如果从任何一种正则表达式中省略“`^`”，那么它将匹配基本路径中的任何深度：
  
  ```perl
  package MyApp::Controller::Catalog;
  sub bar : LocalRegex('widget(\d+)$') { }
  ```
  
  这与前一个示例的不同之处在于它将匹配`http://localhost:3000/my/controller/foo/widget23` - 以及许多其他路径。
  
  对于这两个`:LocalRegex`和`:Regex`action，如果使用在匹配的URL中捕获括号提取值，则这些值在`$c->req->captures`数组中可用。在上面的例子中，“widget23”将在上面的例子中捕获“23”，并且`$c->req->captures->[0]`将是“23”。如果要在URL的末尾传递参数，则必须使用正则表达式action key。请参阅下面的“URL Path Handling”。
  
- Chained handlers (`:Chained`)

  Catalyst还提供了一种构建和分派action链的方法，例如
  
  ```perl
  sub catalog : Chained : CaptureArgs(1) {
      my ( $self, $c, $arg ) = @_;
      ...
  }
  
  sub item : Chained('catalog') : Args(1) {
      my ( $self, $c, $arg ) = @_;
      ...
  }
  ```
  
  处理一条`/catalog/*/item/*`路径。匹配action被一个接一个地调用 - `catalog()`被调用并传递一个路径元素，然后`item()`被另一个路径元素调用。有关此调度类型的更多信息，请参阅[Catalyst::DispatchType::Chained](https://metacpan.org/pod/Catalyst::DispatchType::Chained)。
  
- Private

  ```perl
  sub foo : Private { }
  ```
  
  这将永远不会匹配URL - 它提供了一个私有action，可以在Catalyst中以编程方式调用，但URL被请求时永远不会自动调用。
  
  Catalyst的`:Private`属性是独占的，不能与其他属性一起使用（例如，因此不能与`:Path`或`:Chained`属性结合使用）。
  
  Private actions只能从Catalyst应用程序内部显式执行。您可以通过在controller中调用catalyst方法（例如`forward`或`detach`触发它们）执行此action：
  
  ```perl
  $c->forward('foo');
  # or
  $c->detach('foo');
  ```
  
  有关如何将请求传递给其他操作的完整说明，请参阅[“Flow Control”](#Flow_Control)。请注意，正如在那里讨论的那样，当从另一个组件转发时，必须使用该方法的绝对路径，以便在`MyApp::Controller::Catalog::Order::Process` controller中使用private `bar`方法，如果从其他地方调用，必须使用`$c->forward('/catalog/order/process/bar')`。

注意：看到这些示例后，您可能想知道为正则表达式和路径操作定义子例程名称的重点。但是，每个public action也是一个private action，其路径对应于其命名空间和子例程名称，因此您可以使用一种统一的方式来处理`forward`中的组件。

#### Built-in special actions

如果存在，该special action `index`，`auto`，`begin`，`end`和`default`被称为在请求周期的访问点。

为响应特定的应用程序状态，Catalyst将自动在您的应用程序类中调用这些内置action：

- **default : Path**

  当没有其他动作匹配时调用此方法。例如，它可用于显示主应用程序的通用首页，或用于单个controller的错误页面。**注**：在较旧的Catalyst应用程序中，您将看到`default : Private`，与此大致相当。

- **index : Path : Args (0)**

  `index`很像`default`，但是它不需要参数，并且在匹配过程中加权稍高。它可用作controller的静态入口点，例如具有静态欢迎页面。请注意，它的权重也高于Path。实际上，子名称`index`可以被命名为任何你想要的。子属性决定了操作的行为。注意：在较旧的Catalyst应用程序中，您将看到使用`index : Private`，与此大致相当。

- **begin : Private**

  在请求开始时调用，一旦确定了将运行的controller，但在调用任何URL匹配action之前。Catalyst将调用controller中的`begin`函数，该函数包含与URL匹配的action。

- **end : Private**

  在调用所有URL匹配的action后，在请求结束时调用。Catalyst将调用controller中的`end`函数，该函数包含与URL匹配的action。

- **auto : Private**

  除了正常的内置action外，您还可以使用特殊的action `auto`来制作链。`auto` action将在任何`begin` action之后运行，但在处理URL匹配action之前。与其他内置函数不同，多重`auto` action可被调用；它们将被依次调用，从应用程序类开始并进入最具体的类。

#### Built-in actions in controllers/autochaining

```perl
package MyApp::Controller::Foo;
sub begin : Private { }
sub default : Path  { }
sub end : Path  { }
```

您可以在controller以及应用程序类中定义Built-in action。换句话说，对于上述三个内置动作中的每一个，只有一个将在任何请求周期中运行。因此，如果`MyApp::Controller::Catalog::begin`存在，如果您在`catalog` namespace中运行它将代替`MyApp::begin`，并且`MyApp::Controller::Catalog::Order::begin`将依次覆盖它。

```perl
sub auto : Private { }
```

然而`auto`不会像这样覆盖：如果它们存在，`MyApp::Controller::Root::auto`，`MyApp::Controller::Catalog::auto`以及`MyApp::Catalog::Order::auto`将依次调用。

以下是调用各种内置函数的顺序的一些示例：

##### for a request for /foo/foo

```perl
MyApp::Controller::Foo::auto
MyApp::Controller::Foo::default # in the absence of MyApp::Controller::Foo::Foo
MyApp::Controller::Foo::end
```
  
##### for a request for /foo/bar/foo

```perl
MyApp::Controller::Foo::Bar::begin
MyApp::Controller::Foo::auto
MyApp::Controller::Foo::Bar::auto
MyApp::Controller::Foo::Bar::default # for MyApp::Controller::Foo::Bar::foo
MyApp::Controller::Foo::Bar::end
```

该`auto` action的特点还在于您可以通过返回0来中断处理链。如果`auto` action返回0，则将跳过任何除`end`之外其余的action。因此，对于上面的请求，如果第一个auto返回false，则链将如下所示：

##### for a request for /foo/bar/foo where first auto returns false

```perl
MyApp::Controller::Foo::Bar::begin
MyApp::Controller::Foo::auto # returns false, skips some calls:
# MyApp::Controller::Foo::Bar::auto - never called
# MyApp::Controller::Foo::Bar::foo - never called
MyApp::Controller::Foo::Bar::end
```

你也可以在`auto` action中执行`die`；在这种情况下，请求将直接进入最终阶段，而不处理进一步的action。所以在上面的例子中，`MyApp::Controller::Foo::Bar::end`也被跳过了。

一个示例可以使用`auto`作为身份验证action：您可以设置一个`auto` action来处理应用程序类中的身份验证（始终首先调用），如果身份验证失败，则返回0将跳过该URL的任何剩余方法。

**注**：从另一个角度来看，`auto` action必须返回一个真值才能继续处理！

#### URL Path Handling

您可以将参数作为URL路径的一部分传递，并使用正斜杠（/）分隔。如果action是Regex或LocalRegex，则必须使用'`$`'锚点。例如，假设您想要处理`/foo/$bar/$baz`，在哪里`$bar`和`$baz`可能会有所不同：

```perl
sub foo : Regex('^foo$') { my ($self, $context, $bar, $baz) = @_; }
```

但是，如果您还为`/foo/boo`和定义了操作`/foo/boo/hoo`呢？

```perl
sub boo : Path('foo/boo') { .. }
sub hoo : Path('foo/boo/hoo') { .. }
```

Catalyst以从最具体到最不具体的顺序匹配action - 也就是说，匹配最多数路径的获胜：

```perl
/foo/boo/hoo
/foo/boo
/foo # might be /foo/bar/baz but won't be /foo/boo/hoo
```

因此，Catalyst永远不会错误地将前两个URL分配给'`^foo$`' action。

如果Regex或LocalRegex操作不使用'`$`'锚点，则操作仍将匹配包含参数的URL；但是这些参数不会通过`@_`，因为正则表达式会“吃掉”它们。

谨防！如果你编写两个匹配相同路径的匹配器，具有相同的特性（即它们匹配相同数量的路径），则无法保证实际调用它。首先尝试非正则表达式匹配器，然后是正则表达式匹配器，但是如果你有，比如：

```perl
package MyApp::Controller::Root;
 
sub match1 :Path('/a/b') { }
 
package MyApp::Controller::A;
 
sub b :Local { } # Matches /a/b
```

然后Catalyst将调用它首先找到的那个。总之，不要这样做。

#### Query Parameter Processing

处理URL查询字符串中传递的参数使用[Catalyst::Request](https://metacpan.org/pod/Catalyst::Request)类中的方法。该`param`方法在功能上等同于CGI.pm中的`param`方法，并且可以在需要它的模块中使用。

```perl
# http://localhost:3000/catalog/view/?category=hardware&page=3
my $category = $c->req->param('category');
my $current_page = $c->req->param('page') || 1;
 
# multiple values for single parameter name
my @values = $c->req->param('scrolling_list');
 
# DFV requires a CGI.pm-like input hash
my $results = Data::FormValidator->check($c->req->params, \%dfv_profile);
```

#### Flow Control

您可以使用`forward`方法控制应用程序流，该方法接受要执行的action的key。这可以是相同或另一个Catalyst controller中的action，也可以是类名称，可选地后面跟上一个方法名称。在`forward`之后，控制流程将返回到`forward`发出的方法。

`forward`类似于方法调用。主要区别在于它将调用包装在允许异常处理的`eval`中；它会自动传递上下文对象（`$c`或`$context`）；它允许分析每个调用（显示在启用调试模式的日志中）。

```perl
sub hello : Global {
    my ( $self, $c ) = @_;
    $c->stash->{message} = 'Hello World!';
    $c->forward('check_message'); # $c is automatically included
}
 
sub check_message : Private {
    my ( $self, $c ) = @_;
    return unless $c->stash->{message};
    $c->forward('show_message');
}
 
sub show_message : Private {
    my ( $self, $c ) = @_;
    $c->res->body( $c->stash->{message} );
}
```

`forward`不会创建新请求，因此您的请求对象（`$c->req`）将保持不变。这是使用`forward`和发布重定向之间的关键区别。

您可以通过在匿名数组中添加新参数来将它们传递给`forward`。在这种情况下，`$c->req->args`将只在`forward`持续时间内更改；返回时，原始值`$c->req->args`将被重置。

```perl
sub hello : Global {
    my ( $self, $c ) = @_;
    $c->stash->{message} = 'Hello World!';
    $c->forward('check_message',[qw/test1/]);
    # now $c->req->args is back to what it was before
}
 
sub check_message : Action {
    my ( $self, $c, $first_argument ) = @_;
    my $also_first_argument = $c->req->args->[0]; # now = 'test1'
    # do something...
}
```

从这些示例中可以看出，只要引用同一controller中的方法，就可以使用该方法名称。如果要转发到另一个controller或主应用程序中的方法，则必须通过绝对路径引用该方法。

```perl
$c->forward('/my/controller/action');
$c->forward('/default'); # calls default in main application
```

您还可以转发到类和方法。

```perl
sub hello : Global {
    my ( $self, $c ) = @_;
    $c->forward(qw/MyApp::View:Hello say_hello/);
}
 
sub bye : Global {
    my ( $self, $c ) = @_;
    $c->forward('MyApp::Model::Hello'); # no method: will try 'process'
}
 
package MyApp::View::Hello;
 
sub say_hello {
    my ( $self, $c ) = @_;
    $c->res->body('Hello World!');
}
 
sub process {
    my ( $self, $c ) = @_;
    $c->res->body('Goodbye World!');
}
```

[Catalyst::Action::RenderView][RenderView]使用此机制转发到视图类中的`process`方法。

应该注意的是，虽然forward是有用的，但它不是在Catalyst中调用其他代码的唯一方法。Forward只是在调试屏幕中显示统计信息，将您正在调用的代码包装在异常处理程序中并进行本地化`$c->request->args`。

如果您不想要或不需要这些功能，那么做这样的事情是完全可以接受的（并且更快）：

```perl
sub hello : Global {
    my ( $self, $c ) = @_;
    $c->stash->{message} = 'Hello World!';
    $self->check_message( $c, 'test1' );
}
 
sub check_message {
    my ( $self, $c, $first_argument ) = @_;
    # do something...
}
```

请注意，`forward`返回到调用action并在action完成后继续处理。如果您希望调用action中的所有进一步处理后停止，请改为使用`detach`，这将执行`detach` action而不返回到调用子程序。在这两种情况下，如果省略该方法，Catalyst将自动尝试调用`process()`。

#### Testing

Catalyst有一个内置的http服务器，用于测试或本地部署。（稍后，您可以在生产环境中轻松使用功能更强大的服务器，例如Apache/mod_perl或FastCGI。）

在命令行上启动您的应用程序...

```shell
script/myapp_server.pl
```

...然后在浏览器中访问`http://localhost:3000/`以查看输出。

您也可以从命令行执行以下操作：

```shell
script/myapp_test.pl http://localhost/
```

Catalyst有许多工具可用于应用程序的实际回归测试。帮助程序脚本将自动生成可在开发项目时扩展的基本测试。要编写自己的综合测试脚本，[Test::WWW::Mechanize::Catalyst](https://metacpan.org/pod/Test::WWW::Mechanize::Catalyst)是一个非常宝贵的工具。

有关更多测试想法，请参阅[Catalyst::Manual::Tutorial::08_Testing](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Tutorial/08_Testing.pod)。

用得开心！

## 参见

- [Catalyst::Manual::About](About.md)
- [Catalyst::Manual::Tutorial](Tutorial.md)
- [Catalyst](https://metacpan.org/pod/Catalyst)

### 支持

#### IRC:

```
Join #catalyst on irc.perl.org.
Join #catalyst-dev on irc.perl.org to help with development.
```

#### Mailing lists:

```
http://lists.scsys.co.uk/mailman/listinfo/catalyst
http://lists.scsys.co.uk/mailman/listinfo/catalyst-dev
```

#### Wiki:

```
http://dev.catalystframework.org/wiki
```

#### FAQ:

```
http://dev.catalystframework.org/wiki/faq
```


  
  
  
  
  