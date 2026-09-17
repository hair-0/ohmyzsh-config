# ohmyzsh-config

用 [chezmoi](https://www.chezmoi.io) 管理的 zsh / powerlevel10k 配置（私有仓库）。

## 内容

- `.zshrc` — 模板（`dot_zshrc.tmpl`）：conda 路径按机器渲染；主题 powerlevel10k、插件、别名、历史/补全设置、fzf、kimi-code PATH
- `.p10k.zsh` — powerlevel10k 配置（`p10k configure` 生成）

**不入库**：`.zsh_history` 等历史文件、`.ssh/`、第三方插件与主题本体（由安装脚本在新设备重新 clone）。

## 在新设备部署

前置：

- 本仓库私有，新设备需能访问 GitHub：`gh auth login`，或配置 SSH 密钥
- 终端装 Nerd Font（powerlevel10k 图标需要）

一条命令：

```sh
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply hair-0/ohmyzsh-config
```

`chezmoi apply` 会：写入 `.zshrc`、`.p10k.zsh`，并运行 `scripts/run_once_install-oh-my-zsh.sh`（仅首次：安装 oh-my-zsh、克隆三个插件和 powerlevel10k 主题）。

> 若 `get.chezmoi.io` 网络不通：从 [chezmoi Releases](https://github.com/twpayne/chezmoi/releases/latest) 下载 `linux_amd64` 包解压到 `~/.local/bin`，再执行 `chezmoi init --apply hair-0/ohmyzsh-config`。

## 装完后

- 机器特定/敏感设置放 `~/.zshrc.local`（`.zshrc` 末尾会自动 source，不入库）
- fzf 需单独安装（`~/.fzf.zsh` 由 fzf 安装器生成，不入库）
- conda：若新机路径不同，重跑 `conda init zsh` 刷新 `.zshrc` 中的 conda 块（该块已用 `{{ .chezmoi.homeDir }}` 模板化）
- 更新到最新：`chezmoi update`
- 修改后同步回仓库：`chezmoi add ~/.zshrc ~/.p10k.zsh && chezmoi cd && git add -A && git commit && git push`
