# 名称

Catalyst::Manual::Tutorial::01_Intro - Catalyst Tutorial - 第1章：简介

# 概述

这是Catalyst教程的第1章，共10页。

## [教程概述](README.md)

1. Introduction
2. [Catalyst Basics](02_CatalystBasics.md)
3. [More Catalyst Basics](03_MoreCatalystBasics.md)
4. [Basic CRUD](04_BasicCRUD.md)
5. [Authentication](05_Authentication.md)
6. [Authorization](06_Authorization.md)
7. [Debugging](07_Debugging.md)
8. [Testing](08_Testing.md)
9. [Advanced CRUD](09_AdvancedCRUD.md)
10. [Appendices](10_Appendices.md)


# 描述

本教程提供了Catalyst Web Framework的多部分简介。它旨在提供其许多最常用功能的快速概述。重点是构建几乎所有Catalyst应用程序所需的实际最佳实践。

虽然本教程的主要目标是Catalyst框架的新用户，但有经验的用户可能希望查看特定部分（例如，如何将DBIC用于其模型类，如何向现有应用程序添加身份验证和授权，和/或表格管理）。

本教程的最新代码包含在您可以从以下位置下载的教程虚拟机中：

<http://cattut.shadowcat.co.uk/>

有关获取和使用VM的说明，请参阅下面的[“从使用教程虚拟机开始”](#VM)。

如果您希望直接下载代码，可以通过以下命令获取代码（注意：可能会很快切换到git）：

```shell
svn co http://dev.catalyst.perl.org/repos/Catalyst/trunk/examples/Tutorial/ CatalystTutorial
```

这会将教程每章的最新代码下载到计算机上的CatalystTutorial目录中。

提供这些参考实现，以便当您按照教程，您可以使用代码来确保您的系统设置正确（如果您使用Tutorial虚拟机，这应该不是问题），:-)，并且您没有无意中造成任何打字错误，或意外跳过教程的一部分。

**注意：您可以使用任何Perl支持的操作系统和环境来运行Catalyst。**它应该对Catalyst的操作几乎没有影响，**但本教程是使用基于Debian的教程虚拟机编写的，**您可以下载并使用它来逐步完成整个教程。**我们强烈建议您使用该虚拟机映像通过教程工作，**以避免在使用其他配置时可能出现的问题。我们已经测试了Tutorial Virtual Machine以确保所有示例都能正常工作，但很难在其他平台和版本上保证这一点。

如果您希望直接从CPAN安装而不使用Tutorial虚拟机，则可以通过安装`Task::Catalyst::Tutorial`分发版将示例程序和所有必需的依赖项下载到本地计算机：

```shell
cpan Task::Catalyst::Tutorial
```

这也通过测试以确保依赖项正常工作。如果您在安装这些软件时遇到问题，请在#catalyst IRC频道或Catalyst邮件列表上寻求帮助。

本教程涵盖的主题包括：

- 一个列出和添加书籍的简单应用程序。
- 采用[DBIx::Class][DBIC]（DBIC）为模型（包括一些你可能会想在你的应用程序来使用更先进的技术）。
- 如何在Catalyst中编写CRUD（创建，读取，更新和删除）操作。
- 身份验证（“auth”）。
- 基于角色的授权（“authz”）。
- 尝试提供一个显示当前（5.9）Catalyst实践的示例。
- 使用Template Toolkit（TT）。
- 用于故障排除和调试Catalyst应用程序的有用技术。
- 使用SQLite作为数据库（代码也支持MySQL和PostgreSQL）。（注意：因为我们使用了[DBIx::Class][DBIC]对象关系映射[ORM]层，所以我们的应用程序将与数据库无关，并且可以被[DBIx::Class][DBIC]支持的任何数据库轻松使用。）
- 使用[HTML::FormFu](https://metacpan.org/pod/HTML::FormFu)或[HTML::FormHandler](https://metacpan.org/pod/HTML::FormHandler)进行自动表单处理和验证。

[DBIC]:https://metacpan.org/pod/DBIx::Class

本教程将学习过程作为主要优先事项。例如，这里找到的代码中的注释级别可能在“正常项目”中被认为是过多的。由于它们的上下文价值，本教程通常倾向于对文本中单独讨论的内联注释。它还故意尝试演示各种功能的多种方法（通常，您应尽可能与自己的生产代码保持一致）。

此外，本教程还尝试最小化控制器，模型，TT模板和数据库表的数量。虽然这确实会导致事情有时变得有点人为，但这些概念应该适用于更复杂的环境。可以在<http://wiki.catalystframework.org/wiki/resources/catalystexamples>以及<http://dev.catalyst.perl.org/repos/Catalyst/trunk/examples/>上的Catalyst Subversion存储库`examples`区域找到更完整和复杂的示例应用程序。

# <span id="VM">从使用教程虚拟机开始</span>

以下步骤简要介绍了如何下载Tutorial Virtual Machine。本文档使用术语“主机”来指代您将运行虚拟化软件并启动VM的物理机。术语“客户机”或“虚拟机”指的是虚拟机本身 - 您实际执行教程的内容（以及您在“主机”上启动的内容）。

**注意：**在整个教程中，我们将UNIX shell提示符显示为“`$`”。如果您正在使用Tutorial VM，则提示符将为“`catalyst@catalyst:~$`”（其中“`~`"将更改为显示您当前的目录），但我们将保持简短并只使用“`$`”。

1. 从<http://cattut.shadowcat.co.uk/>下载教程虚拟机映像

  **非常感谢Shadowcat Systems托管虚拟机 （以及他们为Perl社区所做的一切）！**

2. 解压缩“主机”上的映像：

  ```shell
  MAINCOMPUTER:~$ tar zxvf CatalystTutorial.tgz
  ```

3. 使用VMWare Player <http://www.vmware.com/products/player>或VirtualBox <http://www.virtualbox.org/>等工具启动虚拟机。

4. 获得登录提示后，输入用户名**catalyst**和密码**catalyst**。您现在应该处于如下提示：

  ```shell
  catalyst login: catalyst
  Password: catalyst
  ...
  catalyst@catalyst:~$
  ```
  
5. 键入“`ifconfig`”以获取分配给虚拟机的IP地址。您应该得到下列几行的输出：

  ```shell
  eth0  Link encap:Ethernet  HWaddr 00:01:22:3b:45:69
        inet addr:192.168.0.12  Bcast:192.168.0.255  Mask:255.255.255.0
  ...
  ```
  
  您希望的IP地址位与`eth0`接口下面第二行。该映像被设计成使用DHCP自动分配地址。
  
  尝试从“主机”（主桌面）ping此IP地址：
  
  ```shell
  MAINCOMPUTER:~$ ping 192.168.0.12
  PING 192.168.0.12 (192.168.0.12) 56(84) bytes of data.
  64 bytes from 192.168.0.12: icmp_req=1 ttl=255 time=4.97 ms
  64 bytes from 192.168.0.12: icmp_req=2 ttl=255 time=3.43 ms
  ...
  ```
  
  **注：**上面的ping正从您的主机（主桌面）为源，到你的客户虚拟机，而不是其他方式。
  
  如果您没有看到有效的IP地址或它没有响应ping（例如，您收到“请求超时”，“100％丢包”或“目标主机无法访问”）的错误消息，则可能与您可能需要解决的一些与网络相关的问题。有关其他信息和故障排除建议，请参阅下面的[“排除虚拟机网络相关问题”](#Network)部分。
  
  **注：**请记住此IP地址......您将在整个教程中使用它。

6. 在主桌面计算机上，打开SSH客户端并连接到上一步中找到的IP地址。您应该获得登录提示（如果您收到有关该提示的警告消息，请接受SSH密钥）。使用与步骤4中使用的用户名和密码相同的用户名和密码登录：**catalyst / catalyst**

  ```shell
  catalyst login: catalyst
  Password: catalyst
  ...
  catalyst@catalyst:~$
  ```

7. 使用SSH会话，切换到Tutorial Virtual Machine附带的第3章的示例代码目录，然后启动Catalyst Development Server：

  ```shell
  $ cd Final/Chapter03/MyApp
  $ perl script/myapp_server.pl
  ```
  
8. **从主桌面计算机**（“主机”）打开Web浏览器并转至**http://A.B.C.D:3000/**，其中`A.B.C.D`是您在步骤5中查找到的虚拟机的IP地址。例如，如果您的虚拟机正在使用IP地址`192.168.0.12`，您可以将以下URL输入Web浏览器：

  ```
  http://192.168.0.12:3000/
  ```

9. 可选：此外，为了减少下载大小，Tutorial VM只包含一个最小的命令行环境。您可以自由地使用Debian非常强大的`apt`包管理器来安装其他包。您首先要使用`aptitude update`（或者`apt-get update`如果您更喜欢apt-get）来获取apt缓存文件。

  VI/VIM编辑器已安装在Tutorial Virtual Machine上。为了减小下载映像文件的大小，未预安装Emacs。由于人们对哪个编辑器最好有很明显的个人意见，:-)，幸运的是，很容易安装Emacs：
  
  ```shell
  $ sudo aptitude update
  $ sudo aptitude install emacs
  ```
  
  通常，人们期望人们在他们的主桌面（使用上面的术语“主机”）启动Tutorial VM，然后使用该主桌面计算机进行SSH和Web浏览到“客户虚拟机VM”，通过它们进行教程。如果您希望安装X Windows（或任何其他软件包），只需使用Debian的`aptitude`（或`apt-get`）命令。












