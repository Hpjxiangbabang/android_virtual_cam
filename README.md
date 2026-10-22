# VCAM Next

[简体中文](./README.md) | [繁體中文](./README_tc.md) | [English](./README_en.md)

[![Stars](https://img.shields.io/github/stars/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/stargazers)
[![Forks](https://img.shields.io/github/forks/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/network/members)
[![Last commit](https://img.shields.io/github/last-commit/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/commits/master)
[![Release](https://img.shields.io/github/v/release/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/releases)
[![License](https://img.shields.io/github/license/Hpjxiangbabang/android_virtual_cam?style=flat-square)](./LICENSE)
[![Android](https://img.shields.io/badge/Android-10%2B-3DDC84?style=flat-square&logo=android&logoColor=white)](#环境要求)

[![下载 APK](https://img.shields.io/badge/下载-APK-2ea44f?style=for-the-badge)](https://github.com/Hpjxiangbabang/android_virtual_cam/releases)
[![反馈问题](https://img.shields.io/badge/反馈-Issues-1f6feb?style=for-the-badge)](https://github.com/Hpjxiangbabang/android_virtual_cam/issues)
[![上游仓库](https://img.shields.io/badge/上游-w2016561536-6e7681?style=for-the-badge)](https://github.com/w2016561536/android_virtual_cam)

VCAM Next 是一个 LSPosed 模块，用于替换目标应用的相机输入。应用打开相机时，预览和拍照都使用已选定的视频或图片。

适用于需要可重复画面的演示、联调和拍摄流程。请勿用于任何违法用途，使用者自行承担使用后果。

## 功能

**预览**  
目标应用请求相机预览时，输出当前素材。

**拍照**  
拍摄结果为当前播放帧。

**素材库**  
通过系统文件选择器添加视频或图片。图片会转为循环视频。素材由模块提供，目标应用不需要存储权限。

**按应用指定**  
可为单个应用指定素材。未指定的应用使用当前素材。

**播放控制**  
主界面和悬浮控制条提供上一段、暂停、下一段，以及麦克风或视频音轨。切换在相机使用过程中生效。悬浮控制条收起时为一个可拖动图标。

**画面比例**  
素材尺寸与应用请求的分辨率不一致时，默认按比例放入，空白处为黑边，画面不拉伸。关闭该选项后按素材原始尺寸输出，此时两者需要一致。

## 环境要求

| 项目 | 要求 |
| --- | --- |
| 权限 | 已 root |
| 框架 | [LSPosed](https://github.com/LSPosed/LSPosed)，或其他兼容的 Xposed 环境 |
| 系统 | 建议 Android 10 及以上，最低 Android 5.0 |
| 作用范围 | 只勾选需要替换相机的应用 |

预览与拍照已在 Android 14 上验证。

## 安装

应用包名：`io.virtualcam.android`

1. 从 [Releases](https://github.com/Hpjxiangbabang/android_virtual_cam/releases) 安装 APK。发行包对应当前已发布版本；仓库中尚未发布的改动需自行编译。
2. 在 LSPosed 中启用 VCAM Next，并勾选目标应用。
3. 授予存储权限和悬浮窗权限。
4. 强制停止目标应用后，重新打开其相机。

包名变更后，系统将其视为新应用。设备上若仍有同名旧模块，或包名为 `com.example.vcam` 的原版模块，请先在 LSPosed 中停用。两个模块不能同时接管同一相机。

模块通过前台服务保持运行。服务结束后，重新打开 VCAM Next。

## 使用

1. 添加视频或图片。
2. 需要区分应用时，在「应用绑定」中指定素材。
3. 打开目标应用的相机。
4. 使用过程中，通过悬浮控制条或主界面切换素材、暂停，或更改声音来源。

主界面显示当前素材。此处为空时，相机输入不会被替换。

「设置与日志」记录目标应用请求的分辨率。内容相同的记录只更新时间。

## 配置

配置文件位于 `内部存储/DCIM/Camera1/config.json`。

界面中的比例开关对应两种输出。配置文件还可指定另外两种：

| `fit_mode` | 输出 |
| --- | --- |
| `0` | 等比放入，保留黑边 |
| `1` | 等比放大至铺满，裁去超出部分 |
| `2` | 拉伸至铺满 |
| `3` | 按素材原始尺寸输出 |

从使用标记文件的旧版升级时，`disable.jpg`、`no_toast.jpg`、`no-silent.jpg`、`fit_inside.jpg` 的状态会写入 `config.json`。此后以配置文件为准。

## 从源码构建

需要 JDK 17 或更高版本。

```bash
./gradlew assembleDebug
```

安装包位于 `app/build/outputs/apk/debug/`。

## 排查

| 现象 | 处理 |
| --- | --- |
| 仍是真实画面，或黑屏 | 确认 LSPosed 已勾选该应用，并在强制停止后重新打开；确认当前素材不为空；确认其他虚拟相机模块已停用 |
| 更换素材后画面未变 | 等待数秒。若画面仍不更新，结束目标应用后重新打开 |
| 悬浮控制条未出现 | 授予「显示在其他应用上层」。模块未接管该相机时不显示控制条 |
| 前置画面方向不符 | 部分应用会将前置预览水平翻转并旋转 90°。按该应用的实际方向准备素材 |

## 反馈

请通过 [Issues](https://github.com/Hpjxiangbabang/android_virtual_cam/issues) 提交。说明系统版本和目标应用，并附上「设置与日志」中的记录，以及 LSPosed 中本模块的日志。

## 许可

本项目以 [MIT](./LICENSE) 许可发布。版权归原作者 w2016561536（2021）及后续贡献者所有。

VCAM Next 在 [android_virtual_cam](https://github.com/w2016561536/android_virtual_cam) 上继续开发：素材在应用内管理，可按应用指定来源，播放过程中可切换，拍照使用当前帧，并在分辨率不一致时保持画面比例。

- 挂钩方式参考 [CameraHook](https://github.com/wangwei1237/CameraHook)
- H.264 硬解码参考 [Android-VideoToImages](https://github.com/zhantong/Android-VideoToImages)
