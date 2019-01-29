# 名称

Catalyst::Manual::Tutorial::01_Intro - Catalyst Tutorial - 第1章：简介

# 概述

这是Catalyst教程的第1章，共10章。

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

有关获取和使用VM的说明，请参阅下面的[“从使用教程虚拟机开始”](#cong-shi-yong-jiao-cheng-xu-ni-ji-kai-shi)。

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

# <span id="cong-shi-yong-jiao-cheng-xu-ni-ji-kai-shi">从使用教程虚拟机开始</span>

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
  
  如果您没有看到有效的IP地址或它没有响应ping（例如，您收到“请求超时”，“100％丢包”或“目标主机无法访问”）的错误消息，则可能与您可能需要解决的一些与网络相关的问题。有关其他信息和故障排除建议，请参阅下面的[“整理虚拟机网络相关问题”](#zheng-li-xu-ni-ji-wang-luo-xiang-guan-wen-ti)部分。
  
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
  
  例如，要安装使用Fluxbox（一个轻量级的WindowManager - 它非常适用于本教程，因为它大约是其他常见X Windows环境十分之一的大小），您可以：
  
  ```shell
  $ sudo aptitude update
  $ sudo aptitude install xorg fluxbox iceweasel
  ```
  
  然后使用以下命令从**VM控制台**启动X Windows ：
  
  ```shell
  $ startx
  ```
  
  请注意，如果要从SSH会话启动Fluxbox，可以使用`sudo dpkg-reconfigure x11-common`并从菜单中选择“anybody”。否则，您需要在实际的“VM控制台”上启动它。
  
  如果您偏好Gnome桌面环境，则可以执行以下操作：
  
  ```shell
  $ sudo aptitude update
  $ sudo aptitude install gnome iceweasel
  $
  $ # You can reboot or start with 'startx', we will just reboot here
  $ reboot
  ```
  
  对于KDE，只需将上面的包名“`gnome`”替换为“`kde`”即可。
  
  ```shell
  $ sudo aptitude install kde iceweasel
  ```
  
  注意，`iceweasel`基本上用于在Debian盒子上安装Firefox。您可以在X Windows下使用`firefox`命令或`iceweasel`命令（或使用菜单）启动它。您可以在<http://wiki.debian.org/Iceweasel>上获取有关Iceweasel的更多信息。
  
  此外，如果要运行X Windows（或可能需要额外内存的其他工具），则可能需要为虚拟机添加更多内存。有关如何执行此操作的说明，请参阅虚拟化软件的文档（通常非常简单）。

您可能会注意到Tutorial Virtual Machine使用[local::lib](https://metacpan.org/pod/local::lib)，因此Perl模块从`〜/perl5`（在本例中为`/home/catalyst/perl5`）运行，而不是“system Perl”的常用位置。我们建议您也考虑使用这个非常方便的模块。它可以在开发上极其简化在临时工作台和生产服务器上维护和测试不同组合或Perl模块的过程。（“relocatable Perl”功能也可用于从您的主目录[或您选择的任何其他目录]运行模块**和**Perl本身）。

**注**：请提供有关本教程的虚拟机方法如何为您工作的反馈。如果您有任何建议或意见，可以通过本页底部的电子邮件地址或通过<https://rt.cpan.org/Public/Dist/Display.html?Name=Catalyst-Manual>上的RT ticket联系作者。

## <span id="zheng-li-xu-ni-ji-wang-luo-xiang-guan-wen-ti">整理虚拟机网络相关问题</span>

通常，使用虚拟机来完成本教程比在其他环境中尝试*更*容易，尤其是如果您不熟悉Catalyst（或Perl或CPAN或......）。但是，您可能会遇到一些与网络相关的问题。好消息是，有很多关于这个问题的信息可以通过互联网上的搜索引擎获得。这里有一些背景信息可以帮助您入门。

在上一节的第5步中，我们假设“Bridged Mode”配置和DHCP将起作用（它适用于大多数人）。如果DHCP无法运行或在您所在的位置不可用，则大多数虚拟机“host”环境允许您在“guest”和“host”机器之间选择几种不同类型的网络之一。

```
1) Bridged
2) NAT
3) Local host only
```

教程虚拟机默认为“Bridged” - 这应该会导致VM像网络上的另一台设备一样，将获得与主机不同的DHCP IP地址。这种方法的优点是，您可以轻松地SSH和Web浏览到guest虚拟机。通常，如果您希望能够启动VM，然后使用主机中的SSH客户端和Web浏览器连接到虚拟机，则这是最佳选择。

在某些环境中，使用“NAT”（网络地址转换）模式可能会更好。使用此配置，guest VM与主机共享相同的IP地址。此方法的缺点是，如果您希望能够通过SSH或Web浏览到guest VM，则需要进行特殊配置。NAT选项应自动允许VM“出站连接”（例如，如果要通过Internet安装其他Debian软件包），但如果要获取来自其他计算机的“入站连接”，则需要进行特殊配置（包括“主机”）进入VM。某些虚拟机主机环境允许您配置“静态NAT”或“端口转发”以访问guest操作系统，其他人忽略此功能。

注：如果您安装X Windows并在实际VM上本地执行整个教程，而不是从主机上使用SSH和Web浏览器，则NAT模式可以正常工作。

“Local host only”模式允许guest VM和主机在网络中其他设备无法访问的“私有子网”上通话。只要您不需要从VM进入Internet（例如，安装其他Debian软件包），这就可以工作。

有关配置上述选项的帮助，请参阅虚拟机主机环境中的文档。以下是一些可能有用的链接：

- <http://vmfaq.com/entry/34/>
- <http://www.vmware.com/support/pubs/player_pubs.html>
- <http://www.virtualbox.org/manual/ch06.html>

# 本教程中使用的版本和惯例

本教程是使用以下资源构建的。请注意，您可能需要针对不同的环境和版本进行调整（请注意，版本号中的尾随零并不重要，可能会因某些查看技术而被删除;例如，Catalyst v5.80020可能显示为5.8002）：

- Debian 6 (Squeeze)
- Catalyst v5.90002
- Catalyst::Devel v1.34
- DBIx::Class v0.08195
- Catalyst::Model::DBIC::Schema v0.54
- Template Toolkit v2.22
- HTML::FormFu -- v0.09004

- **注**：您可以使用以下命令检查已安装的版本（请注意空格前的斜杠）：

  ```shell
  perl -M<_mod_name_>\ 999
  ```

  或者：

  ```shell
  perl -M<_mod_name_> -e 'print "$<_mod_name_>::VERSION\n"'
  ```

  例如：

  ```shell
  perl -MCatalyst::Devel\ 999
  ```

  或者：
  
  ```shell
  perl -MCatalyst::Devel -e 'print "$Catalyst::Devel::VERSION\n";'
  ```

- 本教程将以`http://localhost:3000`格式显示URL，但如果您从Tutorial Virtual Machine外部运行Web浏览器，则需要将URL中的`localhost`替换为IP地址（同样，您可以从ifconfig命令中获取eth0 IP地址）。例如，如果您的VM的IP地址为192.168.0.12，则需要使用`http://192.168.0.12:3000`的URL。请注意，开发服务器默认为端口3000（您可以在命令行上使用“-p”选项进行更改）。

  **请注意：**根据您使用的Web浏览器，您可能需要在各个点测试应用程序时点击Shift+Reload或Ctrl+Reload拉出一个新页面（请参阅<http://en.wikipedia.org/wiki/Wikipedia:Bypass_your_cache>以获得每个浏览器全面的选项列表信息）。
  
  此外，某些浏览器（**尤其是Internet Explorer**）可能需要使用开发服务器的`-k` **keepalive**选项。

# 数据库

本教程主要关注SQLite，因为它的安装和使用简单; 但是，将在附录中介绍支持MySQL和PostgreSQL所需的脚本修改。

**注：**使用Catalyst和DBIC等工具的一个优点是应用程序变得更加独立于数据库。因此，您会注意到只有`.sql`用于在数据库系统之间初始化数据库的文件发生变化：大多数代码通常保持不变。

您可以在此处跳转到本教程的下一章：[Catalyst Basics](02_CatalystBasics.md)
