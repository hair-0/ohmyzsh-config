# ohmyzsh-config

用 [chezmoi](https://www.chezmoi.io) 管理的 zsh / powerlevel10k 配置（公开仓库）。

## 内容

- `.zshrc` — 模板（`dot_zshrc.tmpl`）：conda 路径按机器渲染；主题 powerlevel10k、插件、别名、历史/补全设置、fzf、kimi-code PATH
- `.p10k.zsh` — powerlevel10k 配置（`p10k configure` 生成）

**不入库**：`.zsh_history` 等历史文件、`.ssh/`、第三方插件与主题本体（由安装脚本在新设备重新 clone）。

## 在新设备部署

前置：

- 新设备需能联网访问 GitHub（公开仓库无需登录；若网络受限，配置代理后执行）
- Nerd Font（图标字体）：`chezmoi apply` 会自动安装，详见下文 [Nerd Font 一节](#nerd-font图标字体)

一条命令：

```sh
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply hair-0/ohmyzsh-config
```

`chezmoi apply` 会：写入 `.zshrc`、`.p10k.zsh`，并运行安装脚本（均仅首次执行、幂等）：
- `scripts/run_once_install-oh-my-zsh.sh` — 安装 oh-my-zsh，克隆三个插件和 powerlevel10k 主题
- `scripts/run_once_install-nerd-font.sh` — 下载安装 MesloLGS NF 字体到 `~/.local/share/fonts`

> 若 `get.chezmoi.io` 网络不通：从 [chezmoi Releases](https://github.com/twpayne/chezmoi/releases/latest) 下载 `linux_amd64` 包解压到 `~/.local/bin`，再执行 `chezmoi init --apply hair-0/ohmyzsh-config`。

## Nerd Font（图标字体）

powerlevel10k 的图标（箭头、分支符号等）依赖 Nerd Font 字体，需先安装，然后在**终端模拟器里把字体设置为该字体**。

### 自动安装（推荐）

`chezmoi apply` 会自动安装 [MesloLGS NF](https://github.com/romkatv/powerlevel10k-media)（powerlevel10k 官方推荐，4 个文件约 10MB），装到 `~/.local/share/fonts` 并刷新字体缓存。已安装时会跳过。

### 手动安装（CLI）

```sh
# 方式一：getnf（交互式菜单，可任选字体，含 MesloLGS NF）
curl -fsSL https://raw.githubusercontent.com/getnf/getnf/main/install.sh | bash
getnf

# 方式二：官方 release zip 手动装（以 JetBrainsMono Nerd Font 为例，约几十 MB）
mkdir -p ~/.local/share/fonts && cd ~/.local/share/fonts
curl -fLO https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
unzip -o JetBrainsMono.zip && rm JetBrainsMono.zip
fc-cache -fv
```

### 装完后别忘

- **终端字体设置**：把终端模拟器的字体改为刚装的字体（如 MesloLGS NF），否则图标仍是方块。p10k 配置向导 `p10k configure` 会自动识别可用字体并提示选择。
- **WSL2 + Windows Terminal**：Windows Terminal 用的是 **Windows 侧**字体，需在 Windows 侧也安装。最简单的方式：把 WSL 里下载好的字体复制过去后重启 Windows Terminal，或在 Windows 上双击 TTF 安装：

  ```sh
  # 在 WSL 里执行（<你的Windows用户名> 替换为实际值）
  cp ~/.local/share/fonts/MesloLGS\ NF*.ttf /mnt/c/Users/<你的Windows用户名>/AppData/Local/Microsoft/Windows/Fonts/
  ```


## 装完后

- 机器特定/敏感设置放 `~/.zshrc.local`（`.zshrc` 末尾会自动 source，不入库）
- fzf 需单独安装（`~/.fzf.zsh` 由 fzf 安装器生成，不入库）
- conda：若新机路径不同，重跑 `conda init zsh` 刷新 `.zshrc` 中的 conda 块（该块已用 `{{ .chezmoi.homeDir }}` 模板化）
- 更新到最新：`chezmoi update`
- 修改后同步回仓库：`chezmoi add ~/.zshrc ~/.p10k.zsh && chezmoi cd && git add -A && git commit && git push`
