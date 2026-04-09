# openclaw-skill-cli-builder

## 简介

Python CLI 工具脚手架生成 Skill。根据描述自动生成完整的 CLI 项目结构，支持 Click / argparse / Typer。

## 触发场景

- "写一个 CLI"、"build CLI"
- "命令行工具"、"command line tool"
- "帮我生成脚手架"、"scaffold"
- "用 Click/Typer/argparse"
- "Python 脚本转 CLI"

## 核心流程

### 第一步：确认 CLI 框架

| 框架 | 适用场景 | 特点 |
|------|----------|------|
| **Click** | 中等复杂度、需要自动生成帮助 | 装饰器风格、功能丰富 |
| **Typer** | 现代 Python 3.6+，喜欢类型提示 | 基于 Click，更简洁 |
| **argparse** | 简单脚本、不想加依赖 | 标准库 |

### 第二步：生成项目结构

**基础结构：**

```
project_name/
├── project_name/
│   ├── __init__.py
│   ├── main.py          # CLI 入口
│   ├── commands/
│   │   ├── __init__.py
│   │   ├── cmd1.py
│   │   └── cmd2.py
│   └── utils.py
├── tests/
│   ├── __init__.py
│   └── test_main.py
├── pyproject.toml
├── README.md
└── .gitignore
```

### 第三步：生成完整代码

**Click 示例：**

```python
# main.py
import click
from commands import cmd1, cmd2

@click.group()
@click.version_option()
def cli():
    """项目描述"""
    pass

cli.add_command(cmd1.run)
cli.add_command(cmd2.run)

if __name__ == '__main__':
    cli()
```

```python
# commands/cmd1.py
import click

@click.command()
@click.option('--name', '-n', default='World', help='Name to greet')
@click.option('--verbose', '-v', is_flag=True, help='Verbose mode')
def run(name, verbose):
    """命令1描述"""
    if verbose:
        click.echo(f"Running command with name={name}")
    click.echo(f"Hello, {name}!")
```

**Typer 示例：**

```python
# main.py
import typer
from typing import Optional

app = typer.Typer(help="项目描述")

@app.command()
def greet(name: str = typer.Option("World", "--name", "-n", help="Name to greet"),
          verbose: bool = typer.Option(False, "--verbose", "-v")):
    """命令描述"""
    if verbose:
        typer.echo(f"Running...")
    typer.echo(f"Hello, {name}!")

if __name__ == "__main__":
    app()
```

### 第四步：pyproject.toml

```toml
[project]
name = "project-name"
version = "0.1.0"
description = "项目描述"
requires-python = ">=3.8"
dependencies = [
    "click>=8.0",
]

[project.scripts]
project-name = "project_name.main:cli"
```

### 第五步：输出格式

```
## CLI 项目：<名称>

### 框架：Click / Typer / argparse

### 文件结构
```
<目录树>
```

### pyproject.toml
```toml
<内容>
```

### main.py
```python
<代码>
```

### 安装与使用
```bash
pip install -e .
<cli-name> --help
```
```

## 最佳实践

1. **子命令用 `@click.group()`**：一个 CLI 多个命令，结构清晰
2. **统一错误处理**：失败时 `click.echo(..., err=True)` + `raise SystemExit(1)`
3. **进度条**：长时间操作用 `click.progressbar()`
4. **彩色输出**：`click.secho("text", fg="green")`
5. **配置文件**：支持 `--config` 加载 YAML/JSON 配置
6. **版本号**：写在 `__init__.py` 里，避免重复

## 注意事项

- 如果不确定框架，默认选 Click（兼容性最好）
- Windows 兼容：避免依赖 Unix 特有功能
- 依赖最小化：能用标准库就别加依赖