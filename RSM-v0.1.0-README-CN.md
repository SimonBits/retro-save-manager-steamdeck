# Retro Save Manager (RSM) Steam Deck 版

**[English](RSM-v0.1.0-README.md) | 简体中文**

Retro Save Manager（RSM）是一套面向 Steam Deck
的轻量级游戏存档同步方案。

RSM 使用 [Syncthing](https://syncthing.net/) 在 Steam Deck
与其他设备之间同步游戏存档文件夹。

安装完成后，Syncthing 将作为 systemd
用户服务在后台自动运行。正常使用时，无需每次手动启动 Syncthing。

## 功能

-   随 Steam Deck 自动启动 Syncthing
-   使用 systemd 用户服务后台运行
-   通过 Syncthing Web UI 完成配置
-   支持局域网访问 Syncthing Web UI
-   RSM 使用独立的 Syncthing 配置
-   不修改已有 Syncthing 配置
-   卸载 RSM 后默认保留同步配置
-   重新安装后继续使用原 Syncthing Device ID
-   无需安装 Decky Plugin

## 支持平台

当前版本已验证：

-   Steam Deck
-   SteamOS
-   x86_64

内置 Syncthing 版本：

-   Syncthing v2.1.5

其他 Linux 掌机理论上可能可以运行，但当前版本尚未进行验证。

## 安装

解压 RSM 安装包。

在解压后的目录中打开终端，然后执行：

``` bash
chmod +x install.sh
./install.sh --cn
```

`--cn` 参数用于启用简体中文安装界面。如果不使用
`--cn`，安装程序默认使用英文输出：

``` bash
./install.sh
```

安装程序会在正式安装前执行一系列检查，包括：

-   CPU 架构
-   systemd 用户会话
-   必需的系统命令
-   是否已经安装 RSM
-   是否已有 Syncthing 正在运行
-   Syncthing 安装文件
-   TCP 8384 端口
-   TCP/UDP 22000 端口
-   UDP 21027 端口

安装完成后，RSM 将 Syncthing 安装到：

``` text
~/.local/share/retro-save-manager/
```

同时创建 systemd 用户服务：

``` text
~/.config/systemd/user/rsm-syncthing.service
```

Syncthing 将自动启用并启动。

## 首次配置

安装完成后，安装程序会显示 Syncthing Web UI 地址，例如：

``` text
http://192.168.1.100:8384
```

可以使用同一局域网中的电脑、手机或其他设备打开这个地址。

之后可以通过 Syncthing Web UI：

-   添加其他同步设备
-   创建共享文件夹
-   选择游戏存档目录
-   查看同步状态

## Web UI 与安全

RSM 默认将 Syncthing Web UI 设置为监听：

``` text
0.0.0.0:8384
```

这样可以直接使用局域网内的其他设备配置 Steam Deck 上的 Syncthing。

由于 Web UI 可以被局域网中的其他设备访问，**强烈建议在 Syncthing 中为
GUI 设置用户名和密码。**

不要主动将 TCP 8384 端口直接暴露到公网。

如果不希望为 Syncthing Web UI 设置用户名和密码，也可以在不需要同步时停止 RSM 服务：

```bash
systemctl --user stop rsm-syncthing.service
```

需要再次同步时，可以重新启动服务：

```bash
systemctl --user start rsm-syncthing.service
```
## 存档同步

RSM 提供的是**文件同步**功能。

RSM
**不会**在不同模拟器、模拟器核心、操作系统或不同游戏版本之间转换存档格式。

为了获得更稳定的同步体验，建议同步设备尽可能使用兼容的运行环境。

尤其需要注意：

-   模拟器或 RetroArch Core
-   ROM / 游戏文件名
-   存档文件名
-   存档扩展名
-   存档目录
-   模拟器使用的存档格式

例如，两台设备即使运行同一个 GBA 游戏，也可能因为使用不同 Core
或不同存档目录而无法直接共享存档。

这类兼容性差异无法由 RSM 自动解决。

## Save State

建议同步游戏自身生成的普通存档文件。

**不建议同步模拟器 Save State（即时存档 / 状态存档）。**

Save State 可能依赖具体的模拟器/Core
版本以及运行状态，因此通常没有普通游戏存档那么好的兼容性。

## Syncthing 冲突

如果多个设备在同步完成前分别修改了同一个存档文件，Syncthing 可能生成
conflict 文件。

为了减少冲突，建议：

1.  在设备 A 上结束游戏。
2.  正常退出游戏或模拟器。
3.  等待 Syncthing 完成同步。
4.  再到设备 B 上继续游戏。

尽量避免多台设备同时修改同一个正在同步的游戏存档。

## RSM 配置

RSM 使用独立的 Syncthing 配置目录：

``` text
~/.local/share/retro-save-manager/config/
```

它与 Steam Deck 上可能已经存在的普通 Syncthing 配置相互独立。

因此，安装 RSM 不需要修改或复用用户原有的 Syncthing 配置。

## 卸载

运行：

``` bash
chmod +x uninstall.sh
./uninstall.sh --cn
```

默认卸载程序会：

-   停止 RSM Syncthing 服务
-   禁用自动启动
-   删除 systemd 用户服务
-   删除 RSM 安装的 Syncthing 程序文件

默认情况下，卸载程序会保留 Syncthing 配置、Device
ID、设备设置和共享文件夹设置，以便重新安装 RSM 后继续使用原有配置。

保留的配置位于：

``` text
~/.local/share/retro-save-manager/config/
```

### 完全删除

默认情况下，卸载程序会保留 Syncthing 配置、Device
ID、设备设置和共享文件夹设置，以便重新安装 RSM 后继续使用原有配置。

如果需要彻底删除 RSM，包括 Syncthing 配置、Device ID、证书、设备设置、
共享文件夹设置以及同步数据库，请运行：

``` bash
chmod +x uninstall.sh
./uninstall.sh --purge --cn
```

**警告：此操作会永久删除 RSM 使用的 Syncthing 配置，无法撤销。**

## 重新安装

正常卸载 RSM 后，如果再次运行安装程序，Installer
会检测之前保留的配置并继续使用。

因此原有 Syncthing 身份和配置可以继续保留，包括 Syncthing Device ID。

## 网络端口

Syncthing 通常使用以下端口：

| 端口 | 协议 | 用途 |
| --- | --- | --- |
| 8384 | TCP | Web UI |
| 22000 | TCP | 同步通信 |
| 22000 | UDP | QUIC 同步通信 |
| 21027 | UDP | 局域网发现 |

RSM Installer 会在安装过程中检查这些端口。

如果 TCP 8384 已经被其他程序占用，安装将停止，因为 RSM 默认使用该端口提供 Syncthing Web UI。

其他 Syncthing 相关端口发生占用时，Installer 可能显示警告。

## 故障排查

查看 RSM 服务状态：

``` bash
systemctl --user status rsm-syncthing.service
```

重新启动服务：

``` bash
systemctl --user restart rsm-syncthing.service
```

查看日志：

``` bash
journalctl --user -u rsm-syncthing.service
```

检查 Web UI 是否正在监听：

``` bash
ss -lntp | grep 8384
```

## 关于 RSM

Retro Save Manager 由 **SimonBits** 制作。

YouTube：**@SimonBitsDev**

Bilibili：**大叔怀旧研究所**

RSM
的目标是在不同怀旧游戏设备之间提供更简单的存档同步方式，同时尽可能保持底层同步过程透明、易于理解。

## 开源协议

Retro Save Manager 使用 GNU General Public License v3.0（GPL-3.0）。

完整协议请查看：

``` text
LICENSE
```

## 第三方软件

RSM 安装包包含第三方开源软件 Syncthing。

Syncthing 是独立的开源项目，使用 Mozilla Public License 2.0（MPL-2.0）。

相关信息请查看：

``` text
THIRD_PARTY_NOTICES.md
payload/SYNCTHING-LICENSE.txt
payload/SYNCTHING-AUTHORS.txt
```

Syncthing 项目：

https://syncthing.net/

Syncthing 源代码：

https://github.com/syncthing/syncthing
