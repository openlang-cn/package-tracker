# 发布到 ClawHub

本项目的 Package Tracker 技能已按 [ClawHub](https://clawhub.com/) / [OpenClaw](https://openclaw.cc/tools/clawhub.html) 规范编写，可发布到 ClawHub 供他人安装使用。

## 发布前准备

1. **安装 ClawHub CLI**
   ```bash
   npm i -g clawhub
   # 或
   pnpm add -g clawhub
   ```

2. **登录**
   ```bash
   clawhub login
   ```
   按提示在浏览器中完成登录（GitHub 账户需至少一周以上历史才能发布）。

3. **修改 SKILL 中的主页地址**  
   编辑 `skills/package-tracker/SKILL.md`，将 frontmatter 里的 `homepage` 和 `metadata.openclaw.homepage` 中的 `YOUR_USERNAME` 改为你的 GitHub 用户名或实际仓库地址。

## 发布单个技能

在项目根目录执行：

```bash
clawhub publish skills/package-tracker --slug package-tracker --name "Package Tracker" --version 1.0.0 --tags latest --changelog "Initial release: 快递鸟即时查询"
```

参数说明：

| 参数 | 说明 |
|------|------|
| `--slug` | 技能唯一标识，安装时使用 `clawhub install package-tracker` |
| `--name` | 在 ClawHub 上显示的名称 |
| `--version` | 语义化版本号（如 1.0.0） |
| `--tags` | 标签，默认 `latest` |
| `--changelog` | 本版本的变更说明（可为空） |

## 后续更新

发布新版本时递增版本号并执行：

```bash
clawhub publish skills/package-tracker --slug package-tracker --name "Package Tracker" --version 1.0.1 --tags latest --changelog "修复 xxx"
```

或使用同步命令（会扫描本地 skills 并提示发布）：

```bash
clawhub sync --bump patch --tags latest
```

## 技能规范摘要（已落实）

- **SKILL.md**：包含 YAML frontmatter（`name`、`description`）及正文说明。
- **OpenClaw 元数据**（在 frontmatter 的 `metadata` 中）：
  - `requires.bins`：`python`
  - `emoji`：📦
  - `homepage`：仓库或文档链接

用户通过 `clawhub install package-tracker` 安装后，技能会出现在工作区的 `./skills` 目录，OpenClaw 会在下一个会话中加载。
