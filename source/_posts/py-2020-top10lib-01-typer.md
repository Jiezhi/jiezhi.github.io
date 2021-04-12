title: 2020年度 Python 库 Top10库之1-Typer
author: Jiezhi.G
email: Jiezhi.G@gmail.com
categories:
  - default
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2021-01-24 12:58:31
url:
tags:
photos:
---
Typer 是一个人见人爱，花见花开的用于构建 CLI 程序的库，基于 Python 3.6+的类型提示（type hints）。

<!--more-->
Typer 官方文档：https://typer.tiangolo.com/
源码地址： https://github.com/tiangolo/typer

# 特性
* 交互式编写：易于使用和学习，减少 debug 和阅读文档时间。

* 容易使用：支持所有的 shell 自动提示和自动补全。

* 简短： 避免重复，从参数声明中获取信息

* 开箱即用： 2行代码即可使用，一行 import，一行函数调用

* 无惧复杂项目： 项目再复杂，Typer照样查

# 依赖
Python 3.6+

# 安装
`pip install typer`

# 举例
## 最简单的实现, **main.py**:
```python
import typer


def main(name: str):
    typer.echo(f"Hello {name}")


if __name__ == "__main__":
    typer.run(main)
```

## 运行
```bash
💬 运行程序
python main.py

💬 友好地提示你缺少 NAME 参数
Usage: main.py [OPTIONS] NAME
Try "main.py --help" for help.

Error: Missing argument 'NAME'.

💬  自带 --help
python main.py --help

Usage: main.py [OPTIONS] NAME

Arguments:
  NAME  [required]

Options:
  --install-completion  Install completion for the current shell.
  --show-completion     Show completion for the current shell, to copy it or customize the installation.
  --help                Show this message and exit.

💬 当你创建一个python包，安装时带上--install-completion参数，则获得了自动补全的功能
💬 现在传入 NAME 参数运行
python main.py Camila

Hello Camila

💬 成功运行！ 🎉
```

# 代码升级
修改 `main.py` 代码，新增 `typer.Typer()` app 实例并创建两个带参数的子命令：

```python
import typer

app = typer.Typer()


@app.command()
def hello(name: str):
    typer.echo(f"Hello {name}")


@app.command()
def goodbye(name: str, formal: bool = False):
    if formal:
        typer.echo(f"Goodbye Ms. {name}. Have a good day.")
    else:
        typer.echo(f"Bye {name}!")


if __name__ == "__main__":
    app()
```

代码中：
* 显示创建一个`typer.Typer`实例
  * 之前代码中`typer.run`其实隐式地创建了改实例
* 通过 `@app.command()` 创建了两个子命令
* 调用 `app()`

```bash
💬 查看 --help 内容
python main.py --help

Usage: main.py [OPTIONS] COMMAND [ARGS]...

Options:
  --install-completion  Install completion for the current shell.
  --show-completion     Show completion for the current shell, to copy it or customize the installation.
  --help                Show this message and exit.

Commands:
  goodbye
  hello

💬  包含两个子命令（即两个函数）: goodbye and hello

💬 查看子命令 hello 的帮助

python main.py hello --help

Usage: main.py hello [OPTIONS] NAME

Arguments:
  NAME  [required]

Options:
  --help  Show this message and exit.

💬 然后再看下 goodbye 的

python main.py goodbye --help

Usage: main.py goodbye [OPTIONS] NAME

Arguments:
  NAME  [required]

Options:
  --formal / --no-formal  [default: False]
  --help                  Show this message and exit.

💬 为 bool 类型的参数创建了 --formal and --no-formal 选项 🎉

💬 执行下 hello 命令

python main.py hello Camila

Hello Camila

💬 再执行下 goodbye 命令

python main.py goodbye Camila

Bye Camila!

💬 再加上 --formal

python main.py goodbye --formal Camila

Goodbye Ms. Camila. Have a good day.

```

## 总结
只需声明一次函数入参类型，即可以创建一个带自动补齐命令功能的程序。

更多内容，详见官网教程：https://typer.tiangolo.com/tutorial/