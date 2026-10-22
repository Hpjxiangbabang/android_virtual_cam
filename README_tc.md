# VCAM Next

[简体中文](./README.md) | [繁體中文](./README_tc.md) | [English](./README_en.md)

[![Stars](https://img.shields.io/github/stars/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/stargazers)
[![Forks](https://img.shields.io/github/forks/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/network/members)
[![Last commit](https://img.shields.io/github/last-commit/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/commits/master)
[![Release](https://img.shields.io/github/v/release/Hpjxiangbabang/android_virtual_cam?style=flat-square)](https://github.com/Hpjxiangbabang/android_virtual_cam/releases)
[![License](https://img.shields.io/github/license/Hpjxiangbabang/android_virtual_cam?style=flat-square)](./LICENSE)
[![Android](https://img.shields.io/badge/Android-10%2B-3DDC84?style=flat-square&logo=android&logoColor=white)](#環境要求)

[![下載 APK](https://img.shields.io/badge/下載-APK-2ea44f?style=for-the-badge)](https://github.com/Hpjxiangbabang/android_virtual_cam/releases)
[![回報問題](https://img.shields.io/badge/回報-Issues-1f6feb?style=for-the-badge)](https://github.com/Hpjxiangbabang/android_virtual_cam/issues)
[![上游倉庫](https://img.shields.io/badge/上游-w2016561536-6e7681?style=for-the-badge)](https://github.com/w2016561536/android_virtual_cam)

VCAM Next 是一個 LSPosed 模組，用來替換目標應用程式的相機輸入。應用程式開啟相機時，預覽和拍照都使用已選定的影片或圖片。

適用於需要可重複畫面的展示、聯調和拍攝流程。請勿用於任何違法用途，使用者自行承擔使用後果。

## 功能

**預覽**  
目標應用程式請求相機預覽時，輸出目前素材。

**拍照**  
拍攝結果為目前播放幀。

**素材庫**  
透過系統檔案選擇器新增影片或圖片。圖片會轉為循環影片。素材由模組提供，目標應用程式不需要儲存權限。

**按應用程式指定**  
可為單一應用程式指定素材。未指定的應用程式使用目前素材。

**播放控制**  
主畫面和懸浮控制列提供上一段、暫停、下一段，以及麥克風或影片音軌。切換在相機使用過程中生效。懸浮控制列收合時為一個可拖動圖示。

**畫面比例**  
素材尺寸與應用程式請求的解析度不一致時，預設按比例放入，空白處為黑邊，畫面不拉伸。關閉該選項後按素材原始尺寸輸出，此時兩者需要一致。

## 環境要求

| 項目 | 要求 |
| --- | --- |
| 權限 | 已 root |
| 框架 | [LSPosed](https://github.com/LSPosed/LSPosed)，或其他相容的 Xposed 環境 |
| 系統 | 建議 Android 10 及以上，最低 Android 5.0 |
| 作用範圍 | 只勾選需要替換相機的應用程式 |

預覽與拍照已在 Android 14 上驗證。

## 安裝

應用程式套件名稱：`io.virtualcam.android`

1. 從 [Releases](https://github.com/Hpjxiangbabang/android_virtual_cam/releases) 安裝 APK。發行包對應當前已發布版本；倉庫中尚未發布的改動需自行編譯。
2. 在 LSPosed 中啟用 VCAM Next，並勾選目標應用程式。
3. 授予儲存權限和懸浮窗權限。
4. 強制停止目標應用程式後，重新開啟其相機。

套件名稱變更後，系統會將其視為新應用程式。裝置上若仍有同名舊模組，或套件名稱為 `com.example.vcam` 的原版模組，請先在 LSPosed 中停用。兩個模組不能同時接管同一相機。

模組透過前景服務保持執行。服務結束後，重新開啟 VCAM Next。

## 使用

1. 新增影片或圖片。
2. 需要區分應用程式時，在「應用綁定」中指定素材。
3. 開啟目標應用程式的相機。
4. 使用過程中，透過懸浮控制列或主畫面切換素材、暫停，或更改聲音來源。

主畫面顯示目前素材。此處為空時，相機輸入不會被替換。

「設定與日誌」記錄目標應用程式請求的解析度。內容相同的記錄只更新時間。

## 設定

設定檔位於 `內部儲存/DCIM/Camera1/config.json`。

介面中的比例開關對應兩種輸出。設定檔還可指定另外兩種：

| `fit_mode` | 輸出 |
| --- | --- |
| `0` | 等比放入，保留黑邊 |
| `1` | 等比放大至鋪滿，裁去超出部分 |
| `2` | 拉伸至鋪滿 |
| `3` | 按素材原始尺寸輸出 |

從使用標記檔的舊版升級時，`disable.jpg`、`no_toast.jpg`、`no-silent.jpg`、`fit_inside.jpg` 的狀態會寫入 `config.json`。此後以設定檔為準。

## 從原始碼建置

需要 JDK 17 或更高版本。

```bash
./gradlew assembleDebug
```

安裝包位於 `app/build/outputs/apk/debug/`。

## 排查

| 現象 | 處理 |
| --- | --- |
| 仍是真實畫面，或黑屏 | 確認 LSPosed 已勾選該應用程式，並在強制停止後重新開啟；確認目前素材不為空；確認其他虛擬相機模組已停用 |
| 更換素材後畫面未變 | 等待數秒。若畫面仍不更新，結束目標應用程式後重新開啟 |
| 懸浮控制列未出現 | 授予「顯示在其他應用程式上層」。模組未接管該相機時不顯示控制列 |
| 前置畫面方向不符 | 部分應用程式會將前置預覽水平翻轉並旋轉 90°。按該應用程式的實際方向準備素材 |

## 回報

請透過 [Issues](https://github.com/Hpjxiangbabang/android_virtual_cam/issues) 提交。說明系統版本和目標應用程式，並附上「設定與日誌」中的記錄，以及 LSPosed 中本模組的日誌。

## 許可

本專案以 [MIT](./LICENSE) 許可發布。版權歸原作者 w2016561536（2021）及後續貢獻者所有。

VCAM Next 在 [android_virtual_cam](https://github.com/w2016561536/android_virtual_cam) 上繼續開發：素材在應用程式內管理，可按應用程式指定來源，播放過程中可切換，拍照使用目前幀，並在解析度不一致時保持畫面比例。

- 掛鉤方式參考 [CameraHook](https://github.com/wangwei1237/CameraHook)
- H.264 硬解碼參考 [Android-VideoToImages](https://github.com/zhantong/Android-VideoToImages)
