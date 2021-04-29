---
title: "Basic Elixir Development Environment"
date: 2021-04-28T11:37:29-04:00
showToc: true
---
Getting started programming with Elixir is straightforward. In this post we'll be describing the main milestones you need to have a productive environment and linking out to various resources to get setup.

### Ingredients 
* Elixir 1.10+ `elixir --version`
* PostgreSQL 10+ `psql --version`
* Node.js 14+ `node --version`

## Assumptions
If you are new to programming entirely, you may find this article brief so if you need additional help, here's a couple of basic guides that can help:

 - [How to install Git on any OS by GitHub](https://github.com/git-guides/install-git)
 - [Node.js Downloads](https://nodejs.org/en/download/)


## Elixir
You can learn how to install Elixir on your operating system at [https://elixir-lang.org/install.html](https://elixir-lang.org/install.html), following that guide should get you setup with Erlang & Elixir. You can verify they are installed by doing `elixir --version` on the command line.

### Local Hex Install
Erlang & Elixir use a powerful package management system called [Hex](https://hex.pm/), you can install it locally by running the `local.hex` command in Mix. Mix is Elixir's build tool, and comes standard with Elixir. 
```shell
$ mix local.hex
```

### Local Phoenix Install
You can install a local Phoenix installer that will help you bootstrap new projects that you want to make. 
```shell
$ mix archive.install hex phx_new
```


## PostgreSQL
PostgreSQL is a powerful SQL database engine, it's optional overall for apps however many Elixir shops end up using it for it's speed & stability. It's also well integrated with the database wrapper Ecto, which is standard in most Phoenix installs.

You can learn about installing PostgreSQL from their official website at [https://www.postgresql.org/download/](https://www.postgresql.org/download/). 

### Verifying Setup
You'll need to be able to access your local database on the command line or via whatever tool you prefer. 

```shell
# Running psql should open up a database shell
$ psql 
psql (13.1)
Type "help" for help.

your_username=#
```


## Visual Studio Code
Visual Studio Code, otherwise known as VSCode is a powerful and lightweight editor from Microsoft. You can use it to efficiently work with most any programming language or text-based file.

You can download & install Visual Studio Code from their website [https://code.visualstudio.com/Download](https://code.visualstudio.com/Download).

### Recommended Extensions
By default VSCode is already pretty powerful, however you can turbocharge it by installing some of these recommended extensions. It's worth browsing their extension marketplace as almost every single helpful tool is published there.

- [ElixirLS: Elixir support and debugger](https://marketplace.visualstudio.com/items?itemName=JakeBecker.elixir-ls)

    ElixirLS installs a local language server & debugger for Elixir applications. Installing this extension will give you auto completion, warnings, debugging, go-to-definition, and more for any type of Elixir program. 
