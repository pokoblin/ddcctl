# ddcctl:在 OSX 命令行下控制显示器的 DDC 工具 #
*其他语言版本:[English](README.md)*

在 OSX 终端里调整外接显示器的内置控制项:

* 亮度(brightness)
* 对比度(contrast)

以及*可能*支持(取决于你的显示器固件是否实现良好):

* 输入源(input source)
* 内置扬声器音量
* RGB 色彩
* 色彩预设
* 复位(reset)

# Apple Silicon(M1/M2/M3/M4) #
在 Apple Silicon 机型上,ddcctl 早期一直使用的旧版 IOFramebuffer I2C 接口已不再由 GPU 驱动
暴露,因此旧版本根本无法进行 DDC/CI 通信。现在 ddcctl 会在编译时检测架构:在 `arm64` 上,
它改为通过私有的 `IOAVService` API(经由 `DCPAVServiceProxy`)来驱动显示器——这与
[m1ddc](https://github.com/waydabber/m1ddc) 和 MonitorControl 所采用的机制相同。Intel 机型
则继续使用原来的 IOFramebuffer 路径。

Apple Silicon 上的注意事项:
* 显示器必须通过 USB-C/DisplayPort Alt Mode 或 Thunderbolt 连接。M1 / 入门级 M2 机型上的
  **内置 HDMI 口**不承载 DDC 信号,因此不受支持。
* 读取 VCP 值(`-X ?`)在固件会响应 DDC/CI 读取的显示器上才有效。

# 项目状态 #
这是一个 GPLv3 开源仓库,你可以在该许可证允许的范围内使用它。

它并不是一个社区性质的"自由软件"项目——它明确地只是我个人的工具,目前处于(令人又爱又怕的)
_"维护模式"_。

# 反馈问题 #
直说吧:如果你的 macOS、Macintosh(不是黑苹果)以及受 DDC 控制的显示器出现问题,那么就得靠
*_你自己_*去排查,并(可选地)在修复后提交 PR(见文末章节)与大家分享。

我目前没有时间去分诊(triage)新的 Issue、也没有精力去处理那些被报告上来的非 bug 问题,
更无意在这里维护一个技术支持或讨论论坛。

# 安装 #

## 方式一:通过 Homebrew 安装 ##
打开终端窗口,运行 `$ brew install ddcctl`。

## 方式二:下载二进制文件 ##
前往 [Releases](https://github.com/kfix/ddcctl/releases),从
[最新发布版](https://github.com/kfix/ddcctl/releases/latest)下载
[`ddcctl_binaries.zip`](https://github.com/kfix/ddcctl/releases/latest/download/ddcctl_binaries.zip)
压缩包。

## 方式三:从源码构建 ##
* 安装 Xcode
* 运行 `make`

# 用法 #
运行 `ddcctl -h` 查看可用选项。
[ddcctl.sh](/scripts/ddcctl.sh) 是我用来控制插在 Mac Mini 上的两台 PC 显示器的脚本。
你可以把 Alfred、ControlPlane 或 Karabiner 指向它,以便快速切换预设。

# 输入源 #
设置输入源时,请参照下表确定应使用的数值。
例如,把第一台显示器切换到 HDMI:`ddcctl -d 1 -i 17`。

| 输入源 | 数值        |
| ------------- |-------------|
| VGA-1 | 1 |
| VGA-2 | 2 |
| DVI-1 | 3 |
| DVI-2 | 4 |
| 复合视频 1 (Composite video 1) | 5 |
| 复合视频 2 (Composite video 2) | 6 |
| S-Video-1 | 7 |
| S-Video-2 | 8 |
| 电视调谐器 1 (Tuner-1) | 9 |
| 电视调谐器 2 (Tuner-2) | 10 |
| 电视调谐器 3 (Tuner-3) | 11 |
| 分量视频 (YPrPb/YCrCb) 1 | 12 |
| 分量视频 (YPrPb/YCrCb) 2 | 13 |
| 分量视频 (YPrPb/YCrCb) 3 | 14 |
| DisplayPort-1 | 15 |
| DisplayPort-2 | 16 |
| HDMI-1 | 17 |
| HDMI-2 | 18 |
| USB-C | 27 |

# 致谢 #
`ddcctl.m` 源自 TonyMac-x86 论坛上的一个[帖子](https://www.tonymacx86.com/threads/controlling-your-monitor-with-osx-ddc-panel.90077/page-6#post-795208)。

`DDC.c` 最初来自 [jontaylor/DDC-CI-Tools-for-OS-X](https://github.com/jontaylor/DDC-CI-Tools-for-OS-X),
后来被论坛上的其他人重构过。

Apple Silicon(`arm64`)的 `IOAVService` 代码路径改编自
[waydabber/m1ddc](https://github.com/waydabber/m1ddc)(MIT 许可)。

也有一些 fork 回移(backport)了补丁,这挺*好*的 :ok_hand:。

# 贡献 PR #

修复 bug 与非修复类/新功能的 PR 遵循相同的大致准则:
* 清楚说明该改动对(假定的)大多数用户 / 开发者的普遍价值
  * 如果还能给出该改动不会削弱大多数用户可用性的正面证明,那就更好了
* 易于测试
  * 如果你有测试步骤,请附上*你的*测试流程!
  * 请记住,我的验证始终是手动的——我没有搭建接入一堆真实 Mac 和显示器的 CI 系统

至于新功能的额外准则,请理解 `ddcctl` 目前所做的就是我在自己全 Apple 设备上_所需要_的功能。

有一份来自已报告 Issue 的(普遍受欢迎的)功能[待办清单](https://github.com/kfix/ddcctl/projects/1)。
欢迎提交 PR 来实现这些功能!

我不太有兴趣添加那些我既无能力、也无意愿在自己硬件上支持的功能。

很遗憾,我无法预估审阅所需的时间——但 PR 越简单/越干净,它被审阅并合并的速度就越快。
