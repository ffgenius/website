---
title: windows 中使用 @antfu/ni
date: '2025-07-07T21:00:00.000+00:00'
lang: zh
duration: 1min
type: note
---

> `@antfu/ni` 是一款可以帮助你正确使用包管理工具的工具。\
> 但在 windows 中 ni 指令与系统内置的 `New Items` 指令冲突，导致无法正确使用。

**解决方法：**

```bash
# 检查系统是否有 ni 指令
Get-Alias -Name ni

# 打开 powershell 配置文件
code $PROFILE
```

```bash
# 检查别名是否存在
if (Test-Path Alias:ni) {
    Remove-Item -Path Alias:ni -Force
} else {}
```

remove alias 之后执行 `Get-Alias -Name ni` 终端会提示找不到 `ni` 指令。随后安装 `@antfu/ni` 即可正常使用。

```bash
npm i -g @antfu/ni
```
