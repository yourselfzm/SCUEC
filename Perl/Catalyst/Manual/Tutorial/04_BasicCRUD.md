# 名称

Catalyst::Manual::Tutorial::04_BasicCRUD - Catalyst Tutorial - 第4章：基本CRUD

# 概述

这是Catalyst教程的第4章，共10章。

## [教程概述](README.md)

1. [Introduction](01_Intro.md)
2. [Catalyst Basics](02_CatalystBasics.md)
3. [More Catalyst Basics](03_MoreCatalystBasics.md)
4. Basic CRUD
5. [Authentication](05_Authentication.md)
6. [Authorization](06_Authorization.md)
7. [Debugging](07_Debugging.md)
8. [Testing](08_Testing.md)
9. [Advanced CRUD](09_AdvancedCRUD.md)
10. [Appendices](10_Appendices.md)

# 描述

本教程的这一章以[第3章](03_MoreCatalystBasics.md)中创建的相当原始的应用程序为基础，为Book对象的创建，读取，更新和删除（CRUD）添加基本支持。请注意，[第3章](03_MoreCatalystBasics.md)中的'list'函数已经实现了CRUD的Read部分（尽管Read通常是指读取单个对象；您可以使用下面介绍的技术实现完整的Read功能）。本节将重点介绍CRUD的创建和删除方面。更高级的功能，包括完整的更新功能，将在[第9章](09_AdvancedCRUD.md)中介绍。

虽然本教程的这一章将向您展示如何自己构建CRUD功能，但另一种选择是使用“CRUD构建器”类型的工具来自动化该过程。你可以减少控制，但它可能更快速简便。例如，请参阅[Catalyst::Plugin::AutoCRUD](https://metacpan.org/pod/Catalyst::Plugin::AutoCRUD)，[CatalystX::CRUD](https://metacpan.org/pod/CatalystX::CRUD)和[CatalystX::CRUD::YUI](https://metacpan.org/pod/CatalystX::CRUD::YUI)。

教程的源代码包含在教程虚拟机的*/home/catalyst/Final*目录中（每章一个子目录）。还有在[Catalyst::Manual::Tutorial::01_Intro](01_Intro.md)中下载代码的说明。

# 非表单提交

我们最初的对象创建尝试将利用Catalyst的“URL参数”功能（我们将在后面的部分中使用更常见的基于表单的提交）。

## 在Books Controller中包含Create Action

编辑`lib/MyApp/Controller/Books.pm`并输入以下方法：

```perl
=head2 url_create
 
Create a book with the supplied title, rating, and author
 
=cut
 
sub url_create :Local {
    # In addition to self & context, get the title, rating, &
    # author_id args from the URL.  Note that Catalyst automatically
    # puts extra information after the "/<controller_name>/<action_name/"
    # into @_.  The args are separated  by the '/' char on the URL.
    my ($self, $c, $title, $rating, $author_id) = @_;
 
    # Call create() on the book model object. Pass the table
    # columns/field values we want to set as hash values
    my $book = $c->model('DB::Book')->create({
            title  => $title,
            rating => $rating
        });
 
    # Add a record to the join table for this book, mapping to
    # appropriate author
    $book->add_to_book_authors({author_id => $author_id});
    # Note: Above is a shortcut for this:
    # $book->create_related('book_authors', {author_id => $author_id});
 
    # Assign the Book object to the stash for display and set template
    $c->stash(book     => $book,
              template => 'books/create_done.tt2');
 
    # Disable caching for this page
    $c->response->header('Cache-Control' => 'no-cache');
}
```

请注意，Catalyst从URL中获取“额外的斜杠分隔信息”并将其作为`@_`中的参数传递（只要参数的数量不是使用类似`:Args(0)`的属性“fixed”）。然后`url_create`操作使用对DBIC `create`方法的简单调用将所请求的信息添加到数据库（通过单独调用`add_to_book_authors`来更新连接表）。与几乎所有控制器方法（至少是直接处理用户输入的方法）一样，它然后设置应该处理此请求的模板。

另请注意，我们明确设置了`no-cache`“Cache-Control”标头，以强制每次使用该页面的浏览器获取新的副本。您甚至可以将其移动到`lib/MyApp/Controller/Root.pm`一个auto方法中，它将通过一行代码自动应用于整个应用程序中的每个页面（请记住第3章，每个`auto`方法都在Controller层次结构中运行）。

## 包含'url_create'操作的模板：

编辑`root/src/books/create_done.tt2`然后输入：

```html
[% # Use the TT Dumper plugin to Data::Dumper variables to the browser   -%]
[% # Not a good idea for production use, though. :-)  'Indent=1' is      -%]
[% # optional, but prevents "massive indenting" of deeply nested objects -%]
[% USE Dumper(Indent=1) -%]
 
[% # Set the page title.  META can 'go back' and set values in templates -%]
[% # that have been processed 'before' this template (here it's updating -%]
[% # the title in the root/src/wrapper.tt2 wrapper template).  Note that -%]
[% # META only works on simple/static strings (i.e. there is no variable -%]
[% # interpolation -- if you need dynamic/interpolated content in your   -%]
[% # title, set "$c->stash(title => $something)" in the controller).     -%]
[% META title = 'Book Created' %]
 
[% # Output information about the record that was added.  First title.   -%]
<p>Added book '[% book.title %]'
 
[% # Then, output the last name of the first author -%]
by '[% book.authors.first.last_name %]'
 
[% # Then, output the rating for the book that was added -%]
with a rating of [% book.rating %].</p>
 
[% # Provide a link back to the list page.  'c.uri_for' builds -%]
[% # a full URI; e.g., 'http://localhost:3000/books/list'      -%]
<p><a href="[% c.uri_for('/books/list') %]">Return to list</a></p>
 
[% # Try out the TT Dumper (for development only!) -%]
<pre>
Dump of the 'book' variable:
[% Dumper.dump(book) %]
</pre>
```

TT USE指令允许访问各种插件模块（TT插件，不是Catalyst插件），以便为基本TT功能添加额外的功能。这里，插件允许[Data::Dumper](https://metacpan.org/pod/Data::Dumper)“pretty printing”对象和变量。除此之外，第3章中的示例可以熟悉其余代码。

## 试试'url_create'功能

确保开发服务器正在使用“`-r`”restart选项运行：

```shell
$ DBIC_TRACE=1 script/myapp_server.pl -r
```

请注意，新路径`/books/url_create`出现在启动调试输出中。

接下来，使用您的浏览器输入以下URL：

```
http://localhost:3000/books/url_create/TCPIP_Illustrated_Vol-2/5/4
```

您的浏览器应显示“`Added book 'TCPIP_Illustrated_Vol-2' by 'Stevens' with a rating of 5.`” 以及DBIC返回的新书模型对象的转储。如果设置了DBIC_TRACE，还应该看到开发服务器日志消息中显示以下DBIC调试消息：

```
INSERT INTO book (rating, title) VALUES (?, ?): `5', `TCPIP_Illustrated_Vol-2'
INSERT INTO book_author (author_id, book_id) VALUES (?, ?): `4', `6'
```

这些INSERT陈述显然正在添加这本书并将其与Richard Stevens的现有记录联系起来。`SELECT`语句结果来自DBIC自动获取该书的`Dumper.dump(book)`。

如果您再单击“返回列表”链接，您会发现现在显示了六本书（如果需要，可以在浏览器`/books/list`页面上按`Shift+Reload`或`Ctrl+Reload`）。您现在应该看到类似于以下的六个DBIC调试消息（其中`N=1-6`）：

```
SELECT author.id, author.first_name, author.last_name 
    FROM book_author me  JOIN author author 
    ON author.id = me.author_id WHERE ( me.book_id = ? ): 'N'
```

# 转换为CHAINED ACTION

虽然上面的示例对我们在本教程前一章中看到的方法使用相同的`Local`操作类型，但是有一种替代方法可以使我们更具体，同时也为更高级的功能铺平道路。为在`lib/MyApp/Controller/Books.pm`的`url_create`更改方法声明，输入匹配以下内容：

```
sub url_create :Chained('/') :PathPart('books/url_create') :Args(3) {
    # In addition to self & context, get the title, rating, &
    # author_id args from the URL.  Note that Catalyst automatically
    # puts the first 3 arguments worth of extra information after the 
    # "/<controller_name>/<action_name/" into @_ because we specified
    # "Args(3)".  The args are separated  by the '/' char on the URL.
    my ($self, $c, $title, $rating, $author_id) = @_;
 
    ...
```

这会转换方法以利用链式操作/分派类型。链接允许您将一个URL自动分派给多个控制器方法，每个控制器方法都可以精确控制它将接收的参数数量。链基本上可以被认为有三个部分 - 开始，中间和结束。下面的要点总结了链中每个部分背后的关键点：

- 开始
	- 使用“`:Chained('/')`”开始链
	- 通过`CaptureArgs()`获得参数
	- 使用`PathPart()`指定要匹配的路径
- 中间
	- 使用`:Chained('_name_')`链接到链的前一部分
	- 通过`CaptureArgs()`获得参数
	- 使用`PathPart()`指定要匹配的路径
- 结束
	- 使用`:Chained('_name_')`链接到链的前一部分
	- **不要通过“`CaptureArgs()`”结束链，使用“`Args()`”代替**
	- 使用`PathPart()`指定要匹配的路径

在上面的`url_create`方法中，我们将所有三个部分组合成一个方法：`:Chained('/')`启动链，`:PathPart('books/url_create')`指定要匹配的基本URL，并且`:Args(3)`准确捕获三个参数并结束链。

正如我们将很快看到的，链可以包含任意数量的“链接”，每个部分都可以捕获一些参数并在此过程中进行一些工作。我们将继续在本教程的这一章中使用Chained操作类型，并使用下面的基本方法和删除功能探索更高级的功能。但链式调度能够做得更多。有关其他信息，请参阅[Catalyst::Manual::Intro中的“Action types”](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Intro.pod#Action-types)，[Catalyst::DispatchType::Chained](https://metacpan.org/pod/Catalyst::DispatchType::Chained)以及<http://www.catalystframework.org/calendar/2006/10>主题上的2006 Advent日历条目。

## 尝试链式操作

如果从`url_create`方法的初始版本（使用`:Local`属性的那个）回顾开发服务器启动日志，您会注意到它产生类似于以下内容的输出：

```
[debug] Loaded Path actions:
.-------------------------------------+--------------------------------------.
| Path                                | Private                              |
+-------------------------------------+--------------------------------------+
| /                                   | /default                             |
| /                                   | /index                               |
| /books                              | /books/index                         |
| /books/list                         | /books/list                          |
| /books/url_create                   | /books/url_create                    |
'-------------------------------------+--------------------------------------'
```

在我们转换为Chained dispatch后开发服务器重新启动时，调试输出应变更为以下内容：

```
[debug] Loaded Path actions:
.-------------------------------------+--------------------------------------.
| Path                                | Private                              |
+-------------------------------------+--------------------------------------+
| /                                   | /default                             |
| /                                   | /index                               |
| /books                              | /books/index                         |
| /books/list                         | /books/list                          |
'-------------------------------------+--------------------------------------'
 
[debug] Loaded Chained actions:
.-------------------------------------+--------------------------------------.
| Path Spec                           | Private                              |
+-------------------------------------+--------------------------------------+
| /books/url_create/*/*/*             | /books/url_create                    |
'-------------------------------------+--------------------------------------'
```

`url_create`已从“Loaded Path actions”部分中消失，但它现在显示在新创建的“Loaded Chained actions”部分下。而“`/*/*/*`”部分清楚地表明了我们对三个参数的要求。

与我们的非链接版本`url_create`一样，使用您的浏览器输入以下URL：

```
http://localhost:3000/books/url_create/TCPIP_Illustrated_Vol-2/5/4
```

您应该看到相同的“Added book 'TCPIP_Illustrated_Vol-2' by 'Stevens' with a rating of 5.”，以及新书模型对象的转储。单击“返回列表”链接，您会发现现在显示了七本书（两份TCPIP_Illustrated_Vol-2）。

## 重构使用'base'方法启动链

让我们快速更新我们最初的Chained动作，以显示链接的更多功能。首先，在编辑器中打开`lib/MyApp/Controller/Books.pm`并添加以下方法：

```
=head2 base
 
Can place common logic to start chained dispatch here
 
=cut
 
sub base :Chained('/') :PathPart('books') :CaptureArgs(0) {
    my ($self, $c) = @_;
 
    # Store the ResultSet in stash so it's available for other methods
    $c->stash(resultset => $c->model('DB::Book'));
 
    # Print a message to the debug log
    $c->log->debug('*** INSIDE BASE METHOD ***');
}
```

在这里，我们打印一条日志消息并在`$c->stash->{resultset}`存储DBIC ResultSet，以便它可以自动用于base链接的其他操作。如果您的控制器总是需要一个书籍ID作为其第一个参数，您可以使用基本方法捕获该参数（通过`:CaptureArgs(1)`）并使用`->find($id)`来拉取书籍对象，并将其保留在存储区中以供链的后续部分使用。因为我们有几个不需要检索书籍的操作（例如我们现在正在使用的`url_create`），我们将很快将该功能添加到一个公共`object`操作中。

至于`url_create`，让我们把它修改为第一次发送到`base`。打开`lib/MyApp/Controller/Books.pm`并编辑`url_create`声明以匹配以下内容：

```
sub url_create :Chained('base') :PathPart('url_create') :Args(3) {
```

保存`lib/MyApp/Controller/Books.pm`后，请注意开发服务器将重新启动，我们的“Loaded Chained actions”部分将略有更改：

```
[debug] Loaded Chained actions:
.-------------------------------------+--------------------------------------.
| Path Spec                           | Private                              |
+-------------------------------------+--------------------------------------+
| /books/url_create/*/*/*             | /books/base (0)                      |
|                                     | => /books/url_create                 |
'-------------------------------------+--------------------------------------'
```

“路径规范”是相同的，但现在它映射到我们期望的两个私有动作。该`base`方法由URL的`/books`部分触发。然而，继续处理该`url_create`方法，因为这个方法“链接” `base`并指定`:PathPart('url_create')`（注意我们可以在这里省略“PathPart”，因为它匹配方法的名称，但我们将包括它尽可能使逻辑显式）。

再次，在浏览器中输入以下URL：

```
http://localhost:3000/books/url_create/TCPIP_Illustrated_Vol-2/5/4
```

应显示同样的“Added book 'TCPIP_Illustrated_Vol-2' by 'Stevens' with a rating of 5.” 消息和新书对象的转储。另请注意`base`方法的开发服务器输出中的额外“INSIDE BASE METHOD”调试消息。单击“返回列表”链接，您会发现现在显示了八本书。（如果您不止一次重复任何“创建”操作，您可能会有更多的书籍。只要书籍的数量适合您添加新书的次数，就不要担心... 每次运行上面的url_create变体之一时，应该通过`myapp01.sql`添加原始的五本书以及另外一本书。）

## 手动建立一个创建表单

虽然上一步中的`url_create`操作确实开始揭示Catalyst和DBIC的强大功能和灵活性，但它显然不是一个非常现实的例子，说明用户应该如何输入数据。本节开始解决这个问题（但是，为了更好地处理基于Web的表单的选项，请参阅[第9章](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Tutorial/09_AdvancedCRUD.pod)）。

### 添加方法以显示表单

编辑`lib/MyApp/Controller/Books.pm`并添加以下方法：

```
=head2 form_create
 
Display form to collect information for book to create
 
=cut
 
sub form_create :Chained('base') :PathPart('form_create') :Args(0) {
    my ($self, $c) = @_;
 
    # Set the TT template to use
    $c->stash(template => 'books/form_create.tt2');
}
```

此操作仅调用包含表单的视图来创建书籍。

### 为表单添加模板

在编辑器中打开`root/src/books/form_create.tt2`并输入：

```
[% META title = 'Manual Form Book Create' -%]
 
<form method="post" action="[% c.uri_for('form_create_do') %]">
<table>
  <tr><td>Title:</td><td><input type="text" name="title"></td></tr>
  <tr><td>Rating:</td><td><input type="text" name="rating"></td></tr>
  <tr><td>Author ID:</td><td><input type="text" name="author_id"></td></tr>
</table>
<input type="submit" name="Submit" value="Submit">
</form>
```

请注意，我们已将表单数据的目标指定为在随后的部分中创建的`form_create_do`方法。

### 添加处理表单值和更新数据库的方法

编辑`lib/MyApp/Controller/Books.pm`并添加以下方法以将表单信息保存到数据库：

```
=head2 form_create_do
 
Take information from form and add to database
 
=cut
 
sub form_create_do :Chained('base') :PathPart('form_create_do') :Args(0) {
    my ($self, $c) = @_;
 
    # Retrieve the values from the form
    my $title     = $c->request->params->{title}     || 'N/A';
    my $rating    = $c->request->params->{rating}    || 'N/A';
    my $author_id = $c->request->params->{author_id} || '1';
 
    # Create the book
    my $book = $c->model('DB::Book')->create({
            title   => $title,
            rating  => $rating,
        });
    # Handle relationship with author
    $book->add_to_book_authors({author_id => $author_id});
    # Note: Above is a shortcut for this:
    # $book->create_related('book_authors', {author_id => $author_id});
 
    # Store new model object in stash and set template
    $c->stash(book     => $book,
              template => 'books/create_done.tt2');
}
```

### 测试表格

请注意，服务器启动日志反映了我们添加的两个新的链式方法：

```
[debug] Loaded Chained actions:
.-------------------------------------+--------------------------------------.
| Path Spec                           | Private                              |
+-------------------------------------+--------------------------------------+
| /books/form_create                  | /books/base (0)                      |
|                                     | => /books/form_create                |
| /books/form_create_do               | /books/base (0)                      |
|                                     | => /books/form_create_do             |
| /books/url_create/*/*/*             | /books/base (0)                      |
|                                     | => /books/url_create                 |
'-------------------------------------+--------------------------------------'
```

将浏览器指向`http://localhost:3000/books/form_create`并输入“TCP/IP Illustrated, Vol 3”作为标题，评级为5，作者ID为4。然后您应该看到在前面的例子中看到的相同`create_done.tt2`模板的输出。最后，单击“返回列表”以查看完整的书籍列表。

**注意：**让用户输入作者的主键ID显然很粗糙; 我们将通过下拉列表解决这个问题，并在[第9章](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Tutorial/09_AdvancedCRUD.pod)中为表单添加验证。

## 一个简单的删除功能

将注意力转向CRUD的删除部分，本节说明了可用于从数据库中删除信息的一些基本技术。

### 在列表中包含删除链接

编辑`root/src/books/list.tt2`并更新它，以匹配以下内容（两个部分已更改：1)附加的“`<th>Links</th>`”表头，以及2)底部附近的删除链接的五行）：

```html
[% # This is a TT comment. -%]
 
[%- # Provide a title -%]
[% META title = 'Book List' -%]
 
[% # Note That the '-' at the beginning or end of TT code  -%]
[% # "chomps" the whitespace/newline at that end of the    -%]
[% # output (use View Source in browser to see the effect) -%]
 
[% # Some basic HTML with a loop to display books -%]
<table>
<tr><th>Title</th><th>Rating</th><th>Author(s)</th><th>Links</th></tr>
[% # Display each book in a table row %]
[% FOREACH book IN books -%]
  <tr>
    <td>[% book.title %]</td>
    <td>[% book.rating %]</td>
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
    <td>
      [% # Add a link to delete a book %]
      <a href="[%
        c.uri_for(c.controller.action_for('delete'), [book.id]) %]">Delete</a>
    </td>
  </tr>
[% END -%]
</table>
```

附加代码显然是设计 在表的右侧用一个`Delete`“按钮”添加一个新列（为简单起见，将使用链接而不是完整的HTML按钮；但实际上，任何修改数据的内容都应该使用发送POST请求的表单）。

另请注意，我们使用的是比以前更先进的`uri_for`表单。这里我们使用`$c->controller->action_for`在将`book.id`值插入适当的位置时，根据我们想要链接的方法自动生成适合该操作的URI。现在，如果您将控制器中的`:PathPart('delete')`方法更改为类似`:PathPart('kill')`的内容，那么您的链接将自动更新，而不会对.tt2模板文件进行任何更改。只要您的方法名称没有更改（此处为“delete”），您的链接仍然是正确的。使用`action_for()`时有一些快捷方式和选项action_for()：

- 如果您指的是当前控制器中的方法，则可以使用`$self->action_for('_method_name_')`。
- 如果您指的是不同控制器中的方法，则需要将该控制器的名称作为参数包含在`controller()`内，如下所示`$c->controller('_controller_name_')->action_for('_method_name_')`。

**注意：**在实践中，您**永远不要**使用GET请求来删除记录 - 始终使用POST来修改数据的操作。我们这里只是为了说明和简单的目的。

### 添加一个通用方法来检索书的链

如前所述，由于我们混合了对单个书籍ID进行操作的操作，而有些操作则没有，我们不应该有`base`捕获书籍ID，在数据库中找到相应的书籍并将其保存在藏匿处以供以后链接使用。连锁，链条。但是，仅仅因为该逻辑不属于`base`并不意味着我们无法创建另一个位置来集中书籍查找代码。在我们的例子中，我们将创建一个名为`object`的方法，将特定的书存储在存储中。总是在单个现有书籍上运行的链可以链接这种方法，但是诸如不对现有书籍进行操作的`url_create`方法可以直接链接到base之外。

要添加`object`方法，请编辑`lib/MyApp/Controller/Books.pm`并添加以下代码：

```
=head2 object
 
Fetch the specified book object based on the book ID and store
it in the stash
 
=cut
 
sub object :Chained('base') :PathPart('id') :CaptureArgs(1) {
    # $id = primary key of book to delete
    my ($self, $c, $id) = @_;
 
    # Find the book object and store it in the stash
    $c->stash(object => $c->stash->{resultset}->find($id));
 
    # Make sure the lookup was successful.  You would probably
    # want to do something like this in a real app:
    #   $c->detach('/error_404') if !$c->stash->{object};
    die "Book $id not found!" if !$c->stash->{object};
 
    # Print a message to the debug log
    $c->log->debug("*** INSIDE OBJECT METHOD for obj id=$id ***");
}
```

现在，任何其他链接的`object`方法都会自动让相应的书等待`$c->stash->{object}`。

### 向Controller中添加删除操作

在编辑器中打开`lib/MyApp/Controller/Books.pm`并添加以下方法：

```
=head2 delete
 
Delete a book
 
=cut
 
sub delete :Chained('object') :PathPart('delete') :Args(0) {
    my ($self, $c) = @_;
 
    # Use the book object saved by 'object' and delete it along
    # with related 'book_author' entries
    $c->stash->{object}->delete;
 
    # Set a status message to be displayed at the top of the view
    $c->stash->{status_msg} = "Book deleted.";
 
    # Forward to the list action/method in this controller
    $c->forward('list');
}
```

此方法首先删除`object`方法保存的book对象。但是，它还会使用级联删除从`book_author`表中删除相应的条目。

然后，不像我们使用之前的创建示例那样转发到“删除完成”页面，它只是设置status_msg呈现普通列表视图时向用户显示通知。

该`delete`操作使用上下文`forward`方法将用户返回到book列表。该`detach`方法也可以使用。而`forward`*返回*到原来的动作一旦完成，`detach`就*不能*返回。除此之外，两者是等价的。

### 尝试删除功能

保存Books控制器后，服务器应自动重新启动。该`delete`方法现在应出现在启动调试输出的“Loaded Chained actions”部分中：

```
[debug] Loaded Chained actions:
.-------------------------------------+--------------------------------------.
| Path Spec                           | Private                              |
+-------------------------------------+--------------------------------------+
| /books/id/*/delete                  | /books/base (0)                      |
|                                     | -> /books/object (1)                 |
|                                     | => /books/delete                     |
| /books/form_create                  | /books/base (0)                      |
|                                     | => /books/form_create                |
| /books/form_create_do               | /books/base (0)                      |
|                                     | => /books/form_create_do             |
| /books/url_create/*/*/*             | /books/base (0)                      |
|                                     | => /books/url_create                 |
'-------------------------------------+--------------------------------------'
```

然后将浏览器指向http://localhost:3000/books/list，然后单击第一个“TCPIP_Illustrated_Vol-2”旁边的“Delete”链接。绿色的“Book deleted”状态消息应显示在页面顶部，以及剩余的八本书的列表。您还将通过DBIC_TRACE输出看到级联删除操作：

```
SELECT me.id, me.title, me.rating FROM book me WHERE ( ( me.id = ? ) ): '6'
DELETE FROM book WHERE ( id = ? ): '6'
```

如果您收到`file error - books/delete.tt2: not found`错误，那么您可能忘记在第3章末尾`sub list`取消注释模板行。

### 修复危险的URL

在前一步骤中执行删除后，请注意浏览器中的URL - 它仍然引用删除操作：

```
http://localhost:3000/books/id/6/delete
```

如果用户按此重新加载此URL仍然有效，该怎么办？在这种情况下，冗余删除是无害的（虽然它确实生成了异常屏幕，但它不会对应用程序或数据库执行任何不良操作），但在其他情况下，这显然会导致麻烦。

我们可以通过转换为重定向来改进逻辑。不像$c->forward('list'))或$c->detach('list'))在处理的流程执行服务器侧变更，重定向是使浏览器发出一个全新的请求的客户端的机构。因此，浏览器中的URL将更新以匹配重定向URL的目标。

要将上一节中使用的转发转换为重定向，请打开`lib/MyApp/Controller/Books.pm`并编辑现有`sub delete`方法以匹配：

```
=head2 delete
 
Delete a book
 
=cut
 
sub delete :Chained('object') :PathPart('delete') :Args(0) {
    my ($self, $c) = @_;
 
    # Use the book object saved by 'object' and delete it along
    # with related 'book_author' entries
    $c->stash->{object}->delete;
 
    # Set a status message to be displayed at the top of the view
    $c->stash->{status_msg} = "Book deleted.";
 
    # Redirect the user back to the list page.  Note the use
    # of $self->action_for as earlier in this section (BasicCRUD)
    $c->response->redirect($c->uri_for($self->action_for('list')));
}
```

### 尝试删除和重定向逻辑

将浏览器指向http://localhost:3000/books/list（不要只是在浏览器中点击“刷新”，因为我们在上一节中将URL保持为无效状态！）并删除剩余两本“TCPIP_Illustrated_Vol-2”书籍的第一份副本。浏览器中的URL应返回到http://localhost:3000/books/list URL，这是一项改进，但请注意，*不会显示绿色的“Book deleted”状态消息*。因为存储在每个请求上都被重置（并且重定向涉及第二个请求），所以在显示之前`status_msg`被清除。

### 使用'uri_for'传递查询参数

有几种方法可以通过重定向传递信息。一种选择是使用我们将在本教程的[第5章](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Tutorial/05_Authentication.pod)中看到的`flash`技术；但是，这里我们将通过重定向本身的查询参数传递信息。打开`lib/MyApp/Controller/Books.pm`并更新现有`sub delete`方法以匹配以下内容：

```
=head2 delete
 
Delete a book
 
=cut
 
sub delete :Chained('object') :PathPart('delete') :Args(0) {
    my ($self, $c) = @_;
 
    # Use the book object saved by 'object' and delete it along
    # with related 'book_author' entries
    $c->stash->{object}->delete;
 
    # Redirect the user back to the list page with status msg as an arg
    $c->response->redirect($c->uri_for($self->action_for('list'),
        {status_msg => "Book deleted."}));
}
```

此修改只是利用`uri_for`在哈希引用中包含任意数量的名称/值对的能力。接下来，我们需要更新`root/src/wrapper.tt2`以`status_msg`作为查询参数进行处理：

```
...
<div id="content">
    [%# Status and error messages %]
    <span class="message">[%
        status_msg || c.request.params.status_msg | html %]</span>
    <span class="error">[% error_msg %]</span>
    [%# This is where TT will stick all of your template's contents. -%]
    [% content %]
</div><!-- end content -->
...
```

虽然上面的示例仅显示了`content`div，但保留文件的其余部分 - 我们对`wrapper.tt2`进行的唯一更改是向行`<span class="message">`添加“`|| c.request.params.status_msg`” 。请注意，我们肯定需要`| html`的“TT”过滤器，因为用户很容易修改URL上的消息，如果我们将其关闭，可能会将有害代码注入应用程序。

### 尝试使用查询参数逻辑删除和重定向

将浏览器指向http://localhost:3000/books/list（您现在应该可以在浏览器中安全地点击“refresh”）。然后删除“TCPIP_Illustrated_Vol-2”的剩余副本。应返回绿色的“Book deleted”状态消息。但请注意，您现在可以点击浏览器中的“Reload”按钮，它只是重新显示图书清单（并且在重新显示时正确显示它而没有“Book deleted”消息）。

**注意：**请务必查看[Authentication](https://metacpan.org/pod/distribution/Catalyst-Manual/lib/Catalyst/Manual/Tutorial/05_Authentication.pod)，我们使用的改进技术更适合您的实际应用。

## 探索DBIC的力量

在本节中，我们将探讨[DBIx::Class](https://metacpan.org/pod/DBIx::Class)提供的一些其他功能。虽然这些功能与Catalyst本身相关性很小，但您几乎肯定希望在应用程序中利用它们。

### 将日期时间列添加到现有书籍表

让我们在现有`books`表中添加两列，以跟踪每本书的添加时间以及每本书的更新时间：

```
$ sqlite3 myapp.db
sqlite> ALTER TABLE book ADD created TIMESTAMP;
sqlite> ALTER TABLE book ADD updated TIMESTAMP;
sqlite> UPDATE book SET created = DATETIME('NOW'), updated = DATETIME('NOW');
sqlite> SELECT * FROM book;
1|CCSP SNRS Exam Certification Guide|5|2010-02-16 04:15:45|2010-02-16 04:15:45
2|TCP/IP Illustrated, Volume 1|5|2010-02-16 04:15:45|2010-02-16 04:15:45
3|Internetworking with TCP/IP Vol.1|4|2010-02-16 04:15:45|2010-02-16 04:15:45
4|Perl Cookbook|5|2010-02-16 04:15:45|2010-02-16 04:15:45
5|Designing with Web Standards|5|2010-02-16 04:15:45|2010-02-16 04:15:45
9|TCP/IP Illustrated, Vol 3|5|2010-02-16 04:15:45|2010-02-16 04:15:45
sqlite> .quit
$
```

以下是没有sqlite3环境提示和输出的命令，以防你想将它们剪切并粘贴为单个块（但在粘贴它们之前先启动sqlite3）：

```
ALTER TABLE book ADD created TIMESTAMP;
ALTER TABLE book ADD updated TIMESTAMP;
UPDATE book SET created = DATETIME('NOW'), updated = DATETIME('NOW');
SELECT * FROM book;
```

这将修改books表以包含两个新字段，并使用当前时间填充这些字段。

### 更新DBIx::Class以自动处理日期时间列

接下来，我们应该重新运行DBIC帮助程序以使用新字段更新Result Classes：

```
$ script/myapp_create.pl model DB DBIC::Schema MyApp::Schema \
    create=static components=TimeStamp dbi:SQLite:myapp.db \
    on_connect_do="PRAGMA foreign_keys = ON"
 exists "/home/catalyst/dev/MyApp/script/../lib/MyApp/Model"
 exists "/home/catalyst/dev/MyApp/script/../t"
Dumping manual schema for MyApp::Schema to directory /home/catalyst/dev/MyApp/script/../lib ...
Schema dump completed.
 exists "/home/catalyst/dev/MyApp/script/../lib/MyApp/Model/DB.pm"
```

请注意，我们稍微修改了对助手的使用：我们告诉它在结果类的`load_components`行中包含[DBIx::Class::TimeStamp](https://metacpan.org/pod/DBIx::Class::TimeStamp)。

如果在编辑器中打开*lib/MyApp/Schema/Result/Book.pm*，您应该会看到这些`created`和`updated`字段现在包含在`add_columns()`调用中。但是，请注意我们手动添加到“`# DO NOT MODIFY...`”行下方的`many_to_many`关系会自动保留。

当我们打开*lib/MyApp/Schema/Result/Book.pm*时，让我们用一些额外的信息更新它，让DBIC自动为我们处理这两个字段的更新。在该文件的底部插入下面的代码（它**必须**在行“`# DO NOT MODIFY...`”**之下**以及在最后一行的`1;`**之上**）：

```
#
# Enable automatic date handling
#
__PACKAGE__->add_columns(
    "created",
    { data_type => 'timestamp', set_on_create => 1 },
    "updated",
    { data_type => 'timestamp', set_on_create => 1, set_on_update => 1 },
);
```

这将覆盖Schema::Loader放置在文件顶部的这些字段的定义。`set_on_create`和`set_on_update`选项将导致每当创建或修改行时DBIx::Class自动更新这些列中的时间戳。

**请注意**，如果使用“-r”选项运行开发服务器，则添加上述行将导致开发服务器自动重新启动。换句话说，开发服务器非常智能，不仅可以重新启动*MyApp/Controller/*，*MyApp/Model/*和*MyApp/View/*目录下的代码，还可以重启其他目录，例如*MyApp/Schema/*中的 “外部DBIC模型”。但是，也请注意，它足够聪明，当您编辑*root/*下的`.tt2`文件时**不必**重新启动。

然后在Web浏览器中输入以下URL：

```
http://localhost:3000/books/url_create/TCPIP_Illustrated_Vol-2/5/4
```

你应该得到我们之前看到的相同的“Book Created”屏幕。但是，如果您现在使用sqlite3命令行工具转储`books`表，您将看到我们添加的新书具有适当的日期和时间（请参阅下面清单中的最后一行）：

```
$ sqlite3 myapp.db "select * from book"
1|CCSP SNRS Exam Certification Guide|5|2010-02-16 04:15:45|2010-02-16 04:15:45
2|TCP/IP Illustrated, Volume 1|5|2010-02-16 04:15:45|2010-02-16 04:15:45
3|Internetworking with TCP/IP Vol.1|4|2010-02-16 04:15:45|2010-02-16 04:15:45
4|Perl Cookbook|5|2010-02-16 04:15:45|2010-02-16 04:15:45
5|Designing with Web Standards|5|2010-02-16 04:15:45|2010-02-16 04:15:45
9|TCP/IP Illustrated, Vol 3|5|2010-02-16 04:15:45|2010-02-16 04:15:45
10|TCPIP_Illustrated_Vol-2|5|2010-02-16 04:18:42|2010-02-16 04:18:42
```

请注意，在调试日志中，生成的SQL DBIC已更改为包含datetime逻辑：

```
INSERT INTO book ( created, rating, title, updated ) VALUES ( ?, ?, ?, ? ):
'2010-02-16 04:18:42', '5', 'TCPIP_Illustrated_Vol-2', '2010-02-16 04:18:42'
INSERT INTO book_author ( author_id, book_id ) VALUES ( ?, ? ): '4', '10'
```

### 创建ResultSet类

DBIC经常被忽视但功能非常强大的特性是它允许您提供自己的[DBIx::Class::ResultSet](https://metacpan.org/pod/DBIx::Class::ResultSet)子类。这可用于从控制器中提取复杂且难看的“查询代码”，并将其封装在自己的ResultSet类方法中。然后，你的ResultSet类中的这些“预制查询”可以通过单个调用，从而使控制器代码更清晰，更易于读取（或者查看代码，如果您想调用它的话）。

为了说明这个概念，用一个相当简单的例子，让我们创建一个方法来返回在过去10分钟内添加的书籍。首先创建一个DBIx::Class将可以查找我们ResultSet类的目录：

```
$ mkdir lib/MyApp/Schema/ResultSet
```

然后打开*lib/MyApp/Schema/ResultSet/Book.pm*并输入以下内容：

```
package MyApp::Schema::ResultSet::Book;
 
use strict;
use warnings;
use base 'DBIx::Class::ResultSet';
 
=head2 created_after
 
A predefined search for recently added books
 
=cut
 
sub created_after {
    my ($self, $datetime) = @_;
 
    my $date_str = $self->result_source->schema->storage
                          ->datetime_parser->format_datetime($datetime);
 
    return $self->search({
        created => { '>' => $date_str }
    });
}
 
1;
```

然后将以下方法添加到*lib/MyApp/Controller/Books.pm*：

```
=head2 list_recent
 
List recently created books
 
=cut
 
sub list_recent :Chained('base') :PathPart('list_recent') :Args(1) {
    my ($self, $c, $mins) = @_;
 
    # Retrieve all of the book records as book model objects and store in the
    # stash where they can be accessed by the TT template, but only
    # retrieve books created within the last $min number of minutes
    $c->stash(books => [$c->model('DB::Book')
                            ->created_after(DateTime->now->subtract(minutes => $mins))]);
 
    # Set the TT template to use.  You will almost always want to do this
    # in your action methods (action methods respond to user input in
    # your controllers).
    $c->stash(template => 'books/list.tt2');
}
```

现在尝试使用“minutes”（最终数字值）不同的值为浏览器中的URL`http://localhost:3000/books/list_recent/_#_`的参数。例如，这将列出过去十五分钟内添加的所有书籍：

```
http://localhost:3000/books/list_recent/15
```

根据您最近添加图书的时间，您可能需要尝试更高或更低的分钟值。

### 链式结果集


















  
the Highwayman 劫匪  
