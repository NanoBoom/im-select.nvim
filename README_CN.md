# im-select.nvim

根据 NeoVim 的编辑模式自动切换输入法。

旧版 Vim 插件（如 [im-select](https://github.com/daipeihust/im-select)）在 Mac 上表现异常，因此用纯 Lua 为 NeoVim 重写了这个插件，效果非常好！

当前版本支持以下平台：

- macOS
- Windows 和 WSL
- Linux
  - Fcitx5
  - Fcitx（仅支持激活/停用切换）
  - IBus

欢迎贡献其他 Linux 输入法框架的支持！

## 1. 安装并检查二进制工具

`im-select.nvim` 使用二进制工具切换输入法，你需要：

1. 在对应系统上安装二进制工具。
2. 确保可执行文件在 NeoVim 可访问的路径中。

### 1.1 Windows / WSL

#### 安装

请安装 `im-select.exe` 并将其加入 `PATH`。

下载地址：[im-select](https://github.com/daipeihust/im-select)
（`x64` 平台请下载 `64-bit` 版本。）

#### 检查

在命令提示符中运行以下命令，验证 `im-select` 是否可被 Neovim 访问：

```bash
# 查找命令
$ where im-select.exe

# 获取当前输入法名称
$ im-select.exe

# 尝试切换到英文键盘
$ im-select.exe 1033
```

或直接在 NeoVim 中运行：

```bash
:!where im-select.exe

:!im-select.exe 1003
```

### 1.2 macOS

#### 安装

请安装 `macism`，这是目前唯一能在 macOS 上正确切换中日韩与英文输入法的 CLI 工具。

下载地址：[macism](https://github.com/laishulu/macism)

#### 检查

在 bash/zsh 中检查：

```bash
# 查找二进制文件
$ which macism

# 获取当前输入法名称
$ macism
com.apple.keylayout.ABC

# 尝试切换到英文键盘
$ macism com.apple.keylayout.ABC
```

在 NeoVim 中检查：

```bash
:!which macism
```

### 1.3 Linux

#### 安装

请安装并配置以下输入法之一：Fcitx / Fcitx5 / IBus

#### 检查

在 bash/zsh 中检查：

**> Fcitx**

```bash
# 查找
$ which fcitx-remote

# 激活输入法
$ fcitx-remote -o

# 停用输入法
$ fcitx-remote -c
```

**> Fcitx5**

```bash
# 查找
$ which fcitx5-remote

# 获取当前输入法名称
$ fcitx5-remote -n

# 尝试切换到英文键盘
$ fcitx5-remote keyboard-us
```

**> IBus**

```bash
# 查找
$ which ibus

# 获取当前输入法名称
$ ibus engine

# 尝试切换到英文键盘
$ ibus engine xkb:us::eng
```

在 NeoVim 中检查：

```bash
# 查找
:!which fcitx
:!which fcitx5
:!which ibus
```

## 2. 安装并配置插件

使用 Lazy.nvim 的最简配置：

```lua
{
    "keaising/im-select.nvim",
    config = function()
        require("im_select").setup({})
    end,
}
```

包含所有选项及默认值的完整配置：

```lua
{
    "keaising/im-select.nvim",
    config = function()
        require('im_select').setup({
            -- 在 `normal` 模式下切换到此输入法
            -- Windows/WSL 默认值："1033"（美式英语键盘）
            -- macOS 默认值："com.apple.keylayout.ABC"（US）
            -- Linux 默认值：
            --               Fcitx5: "keyboard-us"
            --               Fcitx:  "1"
            --               IBus:   "xkb:us::eng"
            -- 可用 `im-select` 或 `fcitx5-remote -n` 获取输入法名称
            default_im_select  = "com.apple.keylayout.ABC",

            -- 可以是二进制名称、完整路径，或包含额外参数的表，例如：
            -- 'im-select'、'/usr/local/bin/im-select'（无额外参数）
            -- 或 { "AIMSwitcher.exe", "--imm" }（需要额外参数）
            -- Windows/WSL 默认值："im-select.exe"
            -- macOS 默认值："macism"
            -- Linux 默认值："fcitx5-remote" 或 "fcitx-remote" 或 "ibus"
            default_command = "im-select.exe",

            -- 触发以下事件时恢复默认输入法
            -- "VimEnter" 和 "FocusGained" 已移除（会引起问题），如有需要可自行添加
            set_default_events = { "InsertLeave", "CmdlineLeave" },

            -- 找不到二进制工具时是否显示安装提示
            keep_quiet_on_no_binary = false,

            -- 是否异步执行 `default_command` 切换输入法
            async_switch_im = true,

            -- 指定进入 Insert 模式时使用的输入法。
            -- 设置后，每次进入 Insert 模式都会切换到此输入法。
            -- 默认为 nil，即进入 Insert 模式时不切换输入法。
            -- macOS 示例："com.sogou.inputmethod.sogou.pinyin"
            insert_im = nil,
        })
    end,
}
```
