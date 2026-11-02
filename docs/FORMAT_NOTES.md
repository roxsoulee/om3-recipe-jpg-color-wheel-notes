# Recipe JPG 12 色節點格式筆記

文件授權：[CC BY 4.0](../LICENSE)。授權範圍與署名方式見 [README](../README.md#文件授權)。

這是已知 layout 的研究記錄，不是官方格式規格或任意 JPG 的安全寫入指南。
本文的「已觀察」指既有唯讀樣本檢查；沒有因這次整理文件重新執行 parser、writer、Workspace 或測試。

## 1. 位址基準

設目標 Exif APP1 segment 的 `FF E1` marker 在檔案位置 `S`：

```text
APP1 marker       S
Segment length    S + 2
Exif payload      S + 4   (the E in Exif\0\0)
```

本文的 payload offset 從 `Exif\0\0` 的第一個 byte 起算，不是 TIFF header、MakerNote header 或整個 JPG 的固定 file offset。

已觀察到兩個不同 layout：

| 樣本家族 | node 0 相對 payload | node 0 相對 marker | 儲存方式 |
| --- | ---: | ---: | --- |
| 24 張逐節點相機樣本 | 11988 | 11992 | 12 個連續 little-endian signed int16 |
| 一個已知 Color base template 家族 | 4096 | 4100 | 12 個連續 little-endian signed int16 |

在各自 layout 中，節點 `i` 位於 `node_0 + 2*i`，`i = 0..11`。
12 個值共 24 bytes；例如第二個家族使用 payload 區間 `[4096, 4120)`。

`marker+11992` 與 `payload+11988` 是同一位置的不同表示法；
但 `payload+4096` 是另一個 layout，不能只用加減 4 bytes 把兩個家族混為一談。

舊筆記曾與實際模板寫入程式使用不同基準，因此不能只看一份舊 offset 表就認定輸入符合該 layout。
Exif APP1 存在、位址仍在 segment 範圍內，都不等於已識別正確 recipe 結構。

## 2. 節點順序

以下是研究專案的標籤與順序，不宣稱是各語系官方 UI 的逐字名稱。
從專案色輪頂端開始順時針排列：

| Index | Label | 中文辨識名稱 | 相機樣本 payload offset | 模板家族 payload offset |
| ---: | --- | --- | ---: | ---: |
| 0 | Yellow | 黃 | 11988 | 4096 |
| 1 | Orange | 橙 | 11990 | 4098 |
| 2 | Red | 紅 | 11992 | 4100 |
| 3 | Pinkish | 粉紅 | 11994 | 4102 |
| 4 | Magenta | 洋紅 | 11996 | 4104 |
| 5 | Purple | 紫 | 11998 | 4106 |
| 6 | Blue | 藍 | 12000 | 4108 |
| 7 | Aqua | 水藍 | 12002 | 4110 |
| 8 | Cyan | 青 | 12004 | 4112 |
| 9 | Mint | 薄荷綠 | 12006 | 4114 |
| 10 | Green | 綠 | 12008 | 4116 |
| 11 | Lime | 黃綠 | 12010 | 4118 |

index 10 是 Green，11 是 Lime；這套索引不是 Mono filter 索引。
歷史文件記錄，名稱對照曾參照 recipe 表格與 TheCranes 相關資料；原始參考頁尚未找回。
沒有將外部圖表、配方表格或圖片複製到本包。

專案另有 hue center 的預覽近似模型，但它不是 JPG 中儲存的額外角度欄位，
也不證明相機成像使用固定寬度的 HSV 區間；本文件不公開或校正預覽演算法。

## 3. 編碼與範圍

已觀察的固定 layout 使用每節點兩個 bytes 的 signed int16，little-endian。
專案接受整數 `-5..+5`，含端點。

| 值 | Little-endian bytes |
| ---: | --- |
| -5 | `FB FF` |
| -1 | `FF FF` |
| 0 | `00 00` |
| +1 | `01 00` |
| +5 | `05 00` |

這是編碼說明，不是新製作的相機樣本。
節點值不是曝光 EV、色相角度或已證明線性的飽和度百分比。
完整 12 節點乘以 11 個合法值的獨立驗證矩陣尚未取得。

歷史 import 摘要另有 big-endian 案例；不能據此假設所有 JPG 都是 LE，
也不能把當前 parser 嘗試兩種位元組序的行為當成所有真機檔案皆可解析的證明。

## 4. 定位方法

已找回的 F5.8 差分工具靜態定義了以下研究流程，並與現存 24 張樣本的命名方式一致：

1. 以 `wheel_XX_p1.jpg`／`wheel_XX_m1.jpg` 配成同一節點的 +1／-1 樣本。
2. 比較各對檔案的 byte 差異，記錄每個位置涉及哪些節點及 byte 值差。
3. 依是否位於 APP1、涉及節點數及鄰近資料等啟發式條件排序候選。
4. 將候選與已知設定、位址基準及獨立 Workspace 讀值對照。

前 3 項可從工具碼確認；第 4 項的完整逐檔原始結果未附齊。
工具報告的 `pos` 是 file offset，不是 APP1-relative offset；候選排名不等於最終欄位語意。
原始候選 CSV／差分報告尚未找回，本次沒有重建或補造。

相機拍攝可能同時改變時間或其他 metadata，不能要求整份 JPG 只差兩個 bytes，
也不能僅靠檔名推定全部設定已受控。

## 5. 結構搜尋與誤判限制

既有 parser 的靜態策略還包括：在 Exif APP1 內分別嘗試 LE／BE，
尋找相鄰範圍值 `-5, +5` 及其後 12 個合法 int16；若鄰近包含 int16 序列 `[8,2,0,3]`，提高候選權重。
實際判型還結合其他 recipe 結構，不能把這段搜尋孤立成通用解析器。

這些是啟發式，不是官方唯一 signature。相同序列、多候選、全零、padding、
metadata 被移除或其他 profile 殘留資料，都可能需要額外判別。
本包沒有提供 parser、writer 或可執行測試，不承諾任意輸入都能正確辨識。

對已知 layout 的有限觀察，不應擴大為所有相機、韌體、Workspace 版本或任意 JPG 的相容性保證。
