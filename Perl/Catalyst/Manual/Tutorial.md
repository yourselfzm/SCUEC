# 名称
Catalyst::Manual::Tutorial - Catalyst Tutorial: Overview

# 描述

Catalyst框架是一个灵活而全面的环境，用于快速构建高功能的Web应用程序。本教程旨在快速介绍其基础知识及其最常用的功能，同时关注实际的最佳实践。

我们建议您在网上阅读此介绍。请访问<https://metacpan.org/module/Catalyst::Manual::Tutorial>，确保您正在阅读本教程的最新版本。或者，您可以使用诸如[Pod::Webserver](https://metacpan.org/pod/Pod::Webserver)，[Pod::POM::Web](https://metacpan.org/pod/Pod::POM::Web)，[Pod::Browser](https://metacpan.org/pod/Pod::Browser)（基于Catalyst）或[CPAN::Mini::Webserver](https://metacpan.org/pod/CPAN::Mini::Webserver)之类的CPAN模块来读取本教程的本地副本。

本教程分为以下几个部分：

1. [Introduction](Tutorial/01_Intro.md)
2. [Catalyst Basics](Tutorial/02_CatalystBasics.md)
3. [More Catalyst Basics](Tutorial/03_MoreCatalystBasics.md)
4. [Basic CRUD](Tutorial/04_BasicCRUD.md)
5. [Authentication](Tutorial/05_Authentication.md)
6. [Authorization](Tutorial/06_Authorization.md)
7. [Debugging](Tutorial/07_Debugging.md)
8. [Testing](Tutorial/08_Testing.md)
9. [Advanced CRUD](Tutorial/09_AdvancedCRUD.md)
10. [Appendices](Tutorial/10_Appendices.md)

有关教程每章的最终代码tarball，请访问<http://dev.catalyst.perl.org/repos/Catalyst/trunk/examples/Tutorial/>。

# 详细目录

## [Chapter 1: Introduction](Tutorial/01_Intro.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* VERSIONS AND CONVENTIONS USED IN THIS TUTORIAL
* CATALYST INSTALLATION
* DATABASES
* WHERE TO GET WORKING CODE

## [Chapter 2: Catalyst Basics](Tutorial/02_CatalystBasics.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* CREATE A CATALYST PROJECT
* HELLO WORLD

  * The Simplest Way
  * Hello, World! Using a View and a Template

* CREATE A SIMPLE CONTROLLER AND AN ACTION

## [Chapter 3: More Catalyst Basics](Tutorial/03_MoreCatalystBasics.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* CREATE A NEW APPLICATION
* EDIT THE LIST OF CATALYST PLUGINS
* CREATE A CATALYST CONTROLLER
* CATALYST VIEWS

  * Create a Catalyst View
  * Create a TT Template Page
  * Test Run The Application

* CREATE A SQLITE DATABASE
* DATABASE ACCESS WITH DBIx::Class

  * Create a Dynamic DBIC Model

* ENABLE THE MODEL IN THE CONTROLLER

  * Test Run The Application

* CREATE A WRAPPER FOR THE VIEW

  * Configure TT.pm For The Wrapper
  * Create the Wrapper Template File and Stylesheet
  * Test Run The Application

* A STATIC DATABASE MODEL WITH DBIx::Class

  * Create Static DBIC Schema Files
  * Updating the Generated DBIC Schema Files
  * Run The Application

* UPDATING THE VIEW
* RUNNING THE APPLICATION FROM THE COMMAND LINE
* OPTIONAL INFORMATION

  * Using RenderView for the Default View
  * Using The Default Template Name
  * Return To A Manually-Specified Template

## [Chapter 4: Basic CRUD](Tutorial/04_BasicCRUD.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* FORMLESS SUBMISSION

  * Include a Create Action in the Books Controller
  * Include a Template for the url_create Action:
  * Try the url_create Feature

* CONVERT TO A CHAINED ACTION

  * Try the Chained Action
  * Refactor to Use a "Base" Method to Start the Chains

* MANUALLY BUILDING A CREATE FORM

  * Add a Method to Display the Form
  * Add a Template for the Form
  * Add Method to Process Form Values and Update Database
  * Test Out the Form

* A SIMPLE DELETE FEATURE

  * Include a Delete Link in the List
  * Add a Common Method to Retrieve a Book for the Chain
  * Add a Delete Action to the Controller
  * Try the Delete Feature
  * Fixing a Dangerous URL
  * Try the Delete and Redirect Logic
  * Using uri_for to Pass Query Parameters
  * Try the Delete and Redirect With Query Param Logic

* EXPLORING THE POWER OF DBIC

  * Add Datetime Columns to Our Existing Books Table
  * Update DBIC to Automatically Handle the Datetime Columns
  * Create a ResultSet Class
  * Chaining ResultSets
  * Adding Methods to Result Classes

## [Chapter 5: Authentication](Tutorial/05_Authentication.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* BASIC AUTHENTICATION

  * Add Users and Roles to the Database
  * Add User and Role Information to DBIC Schema
  * Sanity-Check Reload of Development Server
  * Include Authentication and Session Plugins
  * Configure Authentication
  * Add Login and Logout Controllers
  * Add a Login Form TT Template Page
  * Add Valid User Check
  * Displaying Content Only to Authenticated Users
  * Try Out Authentication

* USING PASSWORD HASHES

  * Get a SHA-1 Hash for the Password
  * Switch to SHA-1 Password Hashes in the Database
  * Enable SHA-1 Hash Passwords in Catalyst::Plugin::Authentication::Store::DBIC
  * Try Out the Hashed Passwords

* USING THE SESSION FOR FLASH

  * Try Out Flash
  * Switch To Flash-To-Stash

## [Chapter 6: Authorization](Tutorial/06_Authorization.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* BASIC AUTHORIZATION

  * Update Plugins to Include Support for Authorization
  * Add Config Information for Authorization
  * Add Role-Specific Logic to the ``Book List'' Template
  * Limit Books::add to admin Users
  * Try Out Authentication And Authorization

* ENABLE MODEL-BASED AUTHORIZATION

## [Chapter 7: Debugging](Tutorial/07_Debugging.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* LOG STATEMENTS
* RUNNING CATALYST UNDER THE PERL DEBUGGER
* DEBUGGING MODULES FROM CPAN
* TT DEBUGGING

## [Chapter 8: Testing](Tutorial/08_Testing.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* RUNNING THE "CANNED" CATALYST TESTS
* RUNNING A SINGLE TEST
* ADDING YOUR OWN TEST SCRIPT
* SUPPORTING BOTH PRODUCTION AND TEST DATABASES

## [Chapter 9: Advanced CRUD](Tutorial/09_AdvancedCRUD.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* ADVANCED CRUD OPTIONS

## [Chapter 10: Appendices](Tutorial/10_Appendices.md)

注意：单击上一行中的标题可跳转到实际章节。以下是本章的“目录”。

* APPENDIX 1: CUT AND PASTE FOR POD-BASED EXAMPLES

  * "Un-indenting" with Vi/Vim
  * "Un-indenting" with Emacs

* APPENDIX 2: USING MYSQL AND POSTGRESQL

  * MySQL
  * PostgreSQL

* APPENDIX 3: IMPROVED HASHING SCRIPT

# 作者

Kennedy Clark, `hkclark@gmail.com`
