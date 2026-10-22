# VCAM Next

[简体中文](./README.md) | [繁體中文](./README_tc.md) | [English](./README_en.md)

[![Stars](https://img.shields.io/github/stars/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/stargazers)
[![Forks](https://img.shields.io/github/forks/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/network/members)
[![Last commit](https://img.shields.io/github/last-commit/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/commits/master)
[![Release](https://img.shields.io/github/v/release/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/releases)
[![License](https://img.shields.io/github/license/Hpjxiangbabang/android_virtual_cam?style=flat-square)](./LICENSE)
[![Android](https://img.shields.io/badge/Android-10%2B-3DDC84?style=flat-square&logo=android&logoColor=white)](#requirements)

[![Download APK](https://img.shields.io/badge/Download-APK-2ea44f?style=for-the-badge)](https://github.com/Hpjxiangbabang/android_virtual_cam/releases)
[![Issues](https://img.shields.io/badge/Issues-report-1f6feb?style=for-the-badge)](https://github.com/Hpjxiangbabang/android_virtual_cam/issues)
[![Upstream](https://img.shields.io/badge/Upstream-w2016561536-6e7681?style=for-the-badge)](https://github.com/w2016561536/android_virtual_cam)

VCAM Next is an LSPosed module that replaces the camera input of a target app. Preview and still capture use the selected video or image.

It is intended for demonstrations, integration work, and capture flows that need a repeatable image. Do not use it for any illegal purpose. You are responsible for how you use it.

## Capabilities

**Preview**  
When a target app requests a camera preview, the module outputs the current material.

**Still capture**  
The captured image is the frame currently playing.

**Library**  
Add a video or an image with the system file picker. An image is converted to a looping video. The module supplies the media, so the target app does not need storage permission.

**Per-app assignment**  
Assign a material to one app. Apps without an assignment use the current material.

**Playback**  
The main screen and the floating control provide previous, pause, next, and a choice between the microphone and the video soundtrack. A change takes effect while the camera is in use. The floating control collapses to a draggable icon.

**Framing**  
When the material size differs from the resolution the app requested, the image is fitted inside that frame. The remaining area is black, and the picture is not stretched. With the option off, the material is sent at its original size and the two sizes need to match.

## Requirements

| Item | Requirement |
| --- | --- |
| Access | Root |
| Framework | [LSPosed](https://github.com/LSPosed/LSPosed), or another compatible Xposed environment |
| System | Android 10 or later recommended. Minimum is Android 5.0 |
| Scope | Enable only the apps whose camera should be replaced |

Preview and still capture have been verified on Android 14.

## Installation

Application id: `io.virtualcam.android`

1. Install the APK from [Releases](https://github.com/Hpjxiangbabang/android_virtual_cam/releases). A release build matches the published version. Changes that are not yet released have to be built from source.
2. Enable VCAM Next in LSPosed and select the target app.
3. Grant storage and overlay permissions.
4. Force-stop the target app, then open its camera again.

After the application id change, the system treats this build as a new app. If an older module with the same display name is still installed, or the original module `com.example.vcam` is still enabled, disable it in LSPosed first. Two modules cannot take over the same camera.

The module stays running as a foreground service. Open VCAM Next again after the service stops.

## Use

1. Add a video or an image.
2. To treat one app differently, assign its material under app binding.
3. Open the camera in the target app.
4. While it is in use, switch material, pause, or change the audio source from the floating control or the main screen.

The main screen shows the current material. While that field is empty, camera input is not replaced.

Settings and logs record the resolution requested by the target app. An identical entry updates its timestamp instead of adding another line.

## Configuration

The configuration file is `Internal storage/DCIM/Camera1/config.json`.

The framing switch on screen selects two of the output modes. The configuration file can also select the other two:

| `fit_mode` | Output |
| --- | --- |
| `0` | Fit inside the requested frame, with black bars |
| `1` | Scale up to cover the frame, cropping the overflow |
| `2` | Stretch to fill the frame |
| `3` | Send the material at its original size |

When upgrading from a build that used marker files, the state of `disable.jpg`, `no_toast.jpg`, `no-silent.jpg`, and `fit_inside.jpg` is written into `config.json`. The configuration file is authoritative after that.

## Build from source

JDK 17 or later is required.

```bash
./gradlew assembleDebug
```

The APK is written to `app/build/outputs/apk/debug/`.

## Troubleshooting

| Symptom | What to check |
| --- | --- |
| The live camera is still shown, or the preview is black | The target app is selected in LSPosed and has been force-stopped, the current material is not empty, and no other virtual-camera module is enabled |
| The picture does not change after a new material is selected | Wait a few seconds. If it still does not update, close the target app and open it again |
| The floating control does not appear | Grant “Display over other apps”. The control stays hidden when this module is not handling that camera |
| The front-camera image has the wrong orientation | Some apps flip the front preview horizontally and rotate it 90°. Prepare the material for the orientation that app actually shows |

## Issues

Report problems in [Issues](https://github.com/Hpjxiangbabang/android_virtual_cam/issues). Include the system version, the target app, the entries under Settings and logs, and this module’s log from LSPosed.

## License

Released under the [MIT](./LICENSE) license. Copyright remains with the original author, w2016561536 (2021), and later contributors.

VCAM Next continues [android_virtual_cam](https://github.com/w2016561536/android_virtual_cam). Materials are managed in the app, can be assigned per application, and can be switched during playback. Still capture uses the current frame, and a size mismatch keeps the picture in proportion.

- Hook approach based on [CameraHook](https://github.com/wangwei1237/CameraHook)
- H.264 hardware decoding based on [Android-VideoToImages](https://github.com/zhantong/Android-VideoToImages)
