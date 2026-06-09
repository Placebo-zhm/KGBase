# uv 使用指南

## 使用目标

`uv` 是一个面向 Python 的包管理、项目管理和 Python 版本管理工具。它适合用来统一处理虚拟环境、依赖安装、锁文件、脚本运行和命令行工具运行。

这篇笔记的目标不是完整翻译官方文档，而是沉淀个人日常 Python 开发中最常用的 `uv` 工作流。

适合记住的核心思路：

- 新项目优先使用 `uv init`。
- 项目依赖优先使用 `uv add`、`uv remove` 和 `uv sync`。
- 临时运行 Python 工具优先使用 `uvx`。
- 旧项目或兼容 pip 工作流时再使用 `uv pip`。
- Python 版本可以交给 `uv python` 管理。

## uv 适合解决什么问题

`uv` 可以替代或覆盖多个传统工具的常见场景：

```text
pip        -> uv pip install / uv add
venv       -> uv venv
pipx       -> uvx / uv tool
pyenv      -> uv python
poetry     -> uv init / uv add / uv sync / uv lock
pip-tools  -> uv lock / uv export / uv pip compile
```

适合使用 `uv` 的场景：

- 创建一个新的 Python 项目。
- 给项目添加、删除、同步依赖。
- 固定项目 Python 版本。
- 在不手动激活虚拟环境的情况下运行命令。
- 临时运行 `ruff`、`black`、`httpie` 等 Python 命令行工具。
- 为已有 `requirements.txt` 项目提供更快的安装方式。

不必一开始就使用 `uv` 的场景：

- 只是阅读别人的代码，还没有运行需求。
- 项目已经有稳定的团队级工具链，不方便马上切换。
- 需要完全复刻旧环境，且旧项目强依赖特定 `pip` 行为。

## 安装和更新

### Windows 安装

PowerShell 中执行：

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

安装后检查版本：

```powershell
uv --version
```

如果提示找不到 `uv`，先重新打开 PowerShell；仍然不行时检查安装目录是否已经加入 `PATH`。

### 使用包管理器安装

如果已经使用 WinGet：

```powershell
winget install --id=astral-sh.uv -e
```

如果已经使用 Scoop：

```powershell
scoop install main/uv
```

### 更新 uv

如果通过官方独立安装器安装，可以使用：

```powershell
uv self update
```

如果通过 WinGet、Scoop、pip 等方式安装，应使用对应包管理器更新。

## Python 版本管理

`uv` 可以安装和管理 Python 版本。这样新机器上不一定需要先手动安装 Python。

查看可用版本：

```powershell
uv python list
```

安装一个 Python 版本：

```powershell
uv python install 3.12
```

安装多个版本：

```powershell
uv python install 3.11 3.12 3.13
```

在当前项目固定 Python 版本：

```powershell
uv python pin 3.12
```

执行后通常会生成或更新 `.python-version`：

```text
.python-version
```

`.python-version` 用来记录当前目录希望使用的 Python 版本。多人或多设备同步项目时，它能帮助 `uv` 选择一致的解释器。

## 项目管理工作流

### 创建新项目

创建并进入项目：

```powershell
uv init demo
cd demo
```

常见项目文件：

```text
demo/
  .python-version
  README.md
  pyproject.toml
  uv.lock
```

文件作用：

- `pyproject.toml`：项目元数据和依赖声明。
- `uv.lock`：锁定依赖解析结果，保证环境可复现。
- `.python-version`：记录项目使用的 Python 版本。
- `.venv`：项目虚拟环境，通常不提交到 Git。

### 在项目中运行命令

不必手动激活虚拟环境，直接使用：

```powershell
uv run python --version
```

运行项目脚本：

```powershell
uv run python main.py
```

运行测试：

```powershell
uv run pytest
```

`uv run` 会在项目环境中执行命令。如果 `.venv` 不存在，`uv` 会按项目配置创建或更新环境。

### 同步已有项目

克隆或复制一个已有 `uv` 项目后，进入项目目录执行：

```powershell
uv sync
```

`uv sync` 会根据 `pyproject.toml` 和 `uv.lock` 创建或同步 `.venv`，适合在新机器、换环境或拉取别人更新后执行。

## 依赖管理

### 添加依赖

添加运行时依赖：

```powershell
uv add requests
```

添加多个依赖：

```powershell
uv add pandas numpy matplotlib
```

添加指定版本范围：

```powershell
uv add "fastapi>=0.110"
```

添加开发依赖：

```powershell
uv add --dev pytest ruff
```

添加依赖后，`uv` 会更新 `pyproject.toml` 和 `uv.lock`。

### 删除依赖

```powershell
uv remove requests
```

删除开发依赖：

```powershell
uv remove --dev pytest
```

### 锁定依赖

只更新锁文件，不安装环境：

```powershell
uv lock
```

一般日常开发中不需要频繁手动执行 `uv lock`，因为 `uv add`、`uv remove`、`uv sync` 会处理常见场景。需要明确刷新锁文件时再单独使用。

### 查看依赖树

```powershell
uv tree
```

这个命令适合排查某个包是被谁引入的，或者依赖版本冲突来自哪里。

## 运行脚本和命令

### 运行单个 Python 脚本

```powershell
uv run script.py
```

也可以显式指定：

```powershell
uv run python script.py
```

### 给脚本添加依赖

如果是单文件脚本，可以把依赖写入脚本的内联元数据：

```powershell
uv add --script script.py requests
```

然后运行：

```powershell
uv run script.py
```

这种方式适合临时脚本、数据处理脚本和可独立分享的小工具。

### 指定 Python 版本运行

```powershell
uv run --python 3.12 python --version
```

如果本机没有对应版本，`uv` 可以按配置自动下载可用的 Python 版本。

## 作为 pip / pipx 替代品使用

### 使用 uvx 临时运行工具

`uvx` 是 `uv tool run` 的简写，适合临时运行命令行工具，不污染当前项目依赖。

```powershell
uvx ruff check .
```

```powershell
uvx black .
```

适合场景：

- 临时格式化或检查代码。
- 试用一个命令行工具。
- 不想全局安装工具。

### 全局安装命令行工具

如果某个工具经常用，可以安装到用户级工具目录：

```powershell
uv tool install ruff
```

查看已安装工具：

```powershell
uv tool list
```

卸载工具：

```powershell
uv tool uninstall ruff
```

如果安装后命令不可用，可以执行：

```powershell
uv tool update-shell
```

然后重新打开 PowerShell。

### 使用 pip 兼容接口

已有项目如果还在使用 `requirements.txt`，可以先用 `uv pip` 提升安装速度，而不立刻迁移到 `uv` 项目。

创建虚拟环境：

```powershell
uv venv
```

安装包：

```powershell
uv pip install requests
```

从 `requirements.txt` 安装：

```powershell
uv pip install -r requirements.txt
```

按锁定文件同步环境：

```powershell
uv pip sync requirements.txt
```

注意：新项目更推荐 `uv init`、`uv add` 和 `uv sync`，`uv pip` 更适合兼容旧工作流。

## 常见命令速查

```powershell
# 查看 uv 版本
uv --version

# 更新 uv
uv self update

# 查看可用 Python 版本
uv python list

# 安装 Python
uv python install 3.12

# 固定当前项目 Python 版本
uv python pin 3.12

# 创建新项目
uv init demo

# 添加依赖
uv add requests

# 添加开发依赖
uv add --dev pytest ruff

# 删除依赖
uv remove requests

# 同步项目环境
uv sync

# 更新锁文件
uv lock

# 在项目环境中运行命令
uv run python main.py

# 临时运行 Python 工具
uvx ruff check .

# 全局安装 Python 工具
uv tool install ruff

# 创建虚拟环境
uv venv

# 使用 pip 兼容接口安装包
uv pip install requests

# 使用 requirements.txt 同步环境
uv pip sync requirements.txt
```

## 推荐日常流程

### 新项目

```powershell
uv init demo
cd demo
uv python pin 3.12
uv add requests
uv add --dev pytest ruff
uv run python main.py
```

推荐提交到 Git 的文件：

```text
pyproject.toml
uv.lock
.python-version
README.md
```

通常不要提交：

```text
.venv/
```

### 已有 uv 项目

```powershell
git pull
uv sync
uv run pytest
```

如果新增依赖：

```powershell
uv add 包名
uv run pytest
git add pyproject.toml uv.lock
git commit -m "deps: 添加项目依赖"
```

### 临时运行工具

```powershell
uvx ruff check .
```

如果之后经常使用，再改为：

```powershell
uv tool install ruff
```

## 常见问题

### 要不要激活虚拟环境

使用 `uv run` 时通常不需要手动激活 `.venv`。例如：

```powershell
uv run python main.py
```

如果需要进入交互式环境或让 IDE 识别解释器，可以选择 `.venv` 中的 Python 解释器。

### uv.lock 要不要提交

应用项目通常建议提交 `uv.lock`，这样多设备和多人环境更容易复现。

库项目是否提交锁文件取决于团队习惯。个人项目中，为了可复现和省心，通常也可以提交。

### pyproject.toml 和 uv.lock 有什么区别

`pyproject.toml` 描述“我需要什么依赖”，例如 `requests>=2`。

`uv.lock` 记录“最终解析出了哪些确定版本”，例如 `requests==2.x.x` 以及它的间接依赖。

日常修改依赖时应通过 `uv add` 和 `uv remove`，不要手动乱改 `uv.lock`。

### .venv 要不要提交

不要提交 `.venv`。它是本机生成的虚拟环境，体积大、路径和平台相关，应该通过 `uv sync` 重新生成。

### 什么时候用 uv pip

优先级可以这样判断：

```text
新项目       -> uv init / uv add / uv sync
已有 uv 项目 -> uv sync / uv run
临时工具     -> uvx
旧 pip 项目  -> uv pip install / uv pip sync
```

## 最小可执行流程

如果只想记住最少命令：

1. 安装后检查：`uv --version`。
2. 新项目：`uv init 项目名`。
3. 固定 Python：`uv python pin 3.12`。
4. 添加依赖：`uv add 包名`。
5. 运行命令：`uv run python main.py`。
6. 同步环境：`uv sync`。
7. 临时工具：`uvx 工具名`。

官方文档入口：

- https://docs.astral.sh/uv/
- https://docs.astral.sh/uv/getting-started/installation/
- https://docs.astral.sh/uv/getting-started/features/
- https://docs.astral.sh/uv/reference/cli/
