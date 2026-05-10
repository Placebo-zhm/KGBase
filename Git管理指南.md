# Obsidian 知识库 Git 管理指南

## 管理目标

这个 Git 流程面向个人 Obsidian 知识库，重点是备份、同步、恢复和可追溯修改历史。

目标：

- 防止误删和误改。
- 在多设备之间同步笔记。
- 保留知识库结构调整记录。
- 遇到冲突时能定位和恢复。

不作为主要目标：

- 多人协作评审。
- 复杂分支模型。
- 自动化发布流程。

## 基本原则

- 重要修改前先查看状态。
- 每次整理后提交一次。
- 提交信息要能看懂修改目的。
- 同步前先拉取，提交后再推送。
- 不把敏感信息、缓存文件和临时文件提交到仓库。

## 当前环境注意事项

当前仓库可能出现 Windows 用户 SID 不一致导致的 Git 安全目录问题，错误类似：

```text
fatal: detected dubious ownership in repository
```

处理方式是在 PowerShell 中执行：

```powershell
git config --global --add safe.directory E:/note/learning/learning_note
```

执行后重新运行：

```powershell
git status
```

如果不再报 `dubious ownership`，说明该问题已经解决。

## 常用工作流

### 修改前检查

在开始大规模整理、移动目录或删除文件前，先执行：

```powershell
git status
```

用途：

- 确认当前有哪些未提交修改。
- 避免在已有未提交修改上继续大改。
- 判断是否需要先提交一个备份点。

### 日常提交

整理完一批笔记后执行：

```powershell
git status
git add .
git commit -m "note: 整理今日学习笔记"
```

如果配置了远程仓库，再推送：

```powershell
git push
```

### 多设备同步

在另一台设备开始写笔记前，先执行：

```powershell
git pull
```

写完并整理后执行：

```powershell
git status
git add .
git commit -m "note: 更新学习笔记"
git push
```

推荐顺序：

1. 开始前 `git pull`。
2. 修改笔记。
3. 整理附件和链接。
4. `git status` 检查。
5. `git add .`。
6. `git commit`。
7. `git push`。

## 提交信息规范

推荐格式：

```text
type: 简短说明
```

常用类型：

```text
note: 笔记内容新增或修改
structure: 目录、MOC、知识库结构调整
asset: 图片、PDF、附件调整
config: Obsidian 或 Git 配置调整
backup: 阶段性备份
```

示例：

```powershell
git commit -m "note: 整理 Git 常用命令"
git commit -m "structure: 新增 PARA 知识库目录说明"
git commit -m "asset: 移动散落图片到附件目录"
git commit -m "config: 更新 Obsidian 附件路径设置"
git commit -m "backup: 保存知识库整理前状态"
```

## 推荐提交频率

适合个人知识库的提交节奏：

- 每次完成一次明显整理后提交。
- 每天结束前至少提交一次。
- 大规模移动目录前先提交一次。
- 删除大量文件前先提交一次。
- 调整 Obsidian 插件和配置前先提交一次。

不要把几天甚至几周的改动堆到一个提交里，否则恢复时很难定位。

## `.gitignore` 建议

Obsidian 知识库通常建议追踪 Markdown 笔记、重要附件和必要配置，忽略缓存、工作区状态、系统文件和 IDE 文件。

建议内容：

```gitignore
# System files
.DS_Store
Thumbs.db
desktop.ini

# IDE files
.idea/
.vscode/

# Obsidian workspace state
.obsidian/workspace.json
.obsidian/workspace-mobile.json

# Obsidian cache or transient files
.obsidian/cache/
.obsidian/plugins/*/data.json

# Temporary files
*.tmp
*.bak
*.swp
~$*
```

### Obsidian 配置是否追踪

建议追踪：

```text
.obsidian/app.json
.obsidian/appearance.json
.obsidian/community-plugins.json
.obsidian/core-plugins.json
.obsidian/hotkeys.json
```

这些文件通常代表知识库的使用习惯和基础配置，适合在多设备间同步。

### 插件和主题是否追踪

推荐策略：

- `.obsidian/plugins`：可以追踪插件清单，但谨慎追踪插件运行数据。
- `.obsidian/themes`：如果主题文件较小且需要多设备一致，可以追踪。
- 插件的 `data.json`：通常包含设备状态、窗口状态或个人配置，先忽略更稳妥。

如果你非常依赖某个插件配置，可以单独决定是否追踪对应插件的配置文件。

## 附件管理

建议将 Obsidian 附件统一放入：

```text
99-Attachments/
```

图片、PDF 可以进入 Git，但要注意仓库体积。

建议：

- 普通图片可以提交。
- 大型 PDF、视频、压缩包谨慎提交。
- 单个文件超过 50 MB 时，优先考虑网盘、外部资料库或 Git LFS。

检查大文件可以使用：

```powershell
Get-ChildItem -Recurse -File | Sort-Object Length -Descending | Select-Object -First 20 FullName,Length
```

## 冲突处理

冲突常见于多设备同时修改同一篇笔记。

当 `git pull` 后出现冲突，先查看状态：

```powershell
git status
```

冲突文件中可能出现：

```text
<<<<<<< HEAD
本地内容
=======
远程内容
>>>>>>> 分支名或提交号
```

处理步骤：

1. 打开冲突文件。
2. 比较本地内容和远程内容。
3. 保留正确内容，删除冲突标记。
4. 保存文件。
5. 执行：

```powershell
git add .
git commit -m "note: 解决同步冲突"
```

如果不确定怎么处理，不要立刻删除任何一边内容，可以先复制到临时笔记中再整理。

## 恢复误删和误改

### 查看最近提交

```powershell
git log --oneline -n 10
```

### 查看某个文件的修改历史

```powershell
git log -- "文件路径.md"
```

### 恢复某个文件到最近一次提交状态

如果确认要放弃当前文件的未提交修改：

```powershell
git restore "文件路径.md"
```

### 从历史提交恢复某个文件

```powershell
git restore --source 提交ID -- "文件路径.md"
```

注意：恢复命令会改变工作区文件。执行前建议先 `git status`，确认当前修改是否已经备份。

## 大规模整理前的安全步骤

移动目录、重命名大量文件、清理附件前，推荐先创建备份提交：

```powershell
git status
git add .
git commit -m "backup: 大规模整理前备份"
```

整理完成后再提交：

```powershell
git status
git add .
git commit -m "structure: 调整知识库目录结构"
```

这样如果整理过程中链接、附件或目录出错，可以回到整理前状态。

## 推荐日常命令速查

```powershell
# 查看状态
git status

# 拉取远程更新
git pull

# 添加全部修改
git add .

# 提交
git commit -m "note: 更新学习笔记"

# 推送
git push

# 查看最近提交
git log --oneline -n 10
```

## 最小可执行流程

如果只想记住最少规则，按下面流程执行：

1. 写笔记前先执行 `git pull`。
2. 写完或整理完执行 `git status`。
3. 用 `git add .` 添加修改。
4. 用 `git commit -m "note: 简短说明"` 提交。
5. 用 `git push` 推送。
6. 遇到 `dubious ownership` 时执行 `git config --global --add safe.directory E:/note/learning/learning_note`。
