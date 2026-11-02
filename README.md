# OM-3 Recipe JPG Color Wheel Notes

OM SYSTEM OM-3 Recipe JPG 的 12 色節點定位與讀取研究筆記。

這份文件整理已觀察的位元組配置、節點順序及歷史驗證記錄，不是整套 Recipe Studio 原始碼，也不是官方格式規格。

## 文件

- [格式筆記](docs/FORMAT_NOTES.md)：位址基準、兩種已觀察 layout、編碼、12 節點順序與定位方法。
- [證據與限制](docs/EVIDENCE_AND_LIMITATIONS.md)：直接觀察、歷史摘要、來源及尚未閉合的證據。
- [變更摘要](CHANGELOG.md)。

## 已知範圍

- 已觀察一組相機樣本的色輪資料位於 Exif APP1 payload 相對位址 `11988`。
- 一個已知模板家族使用 payload 相對位址 `4096`。
- 兩者都記錄 12 個連續 signed int16 值，已觀察的固定 layout 使用 little-endian；專案接受整數 `-5..+5`。
- 歷史交接文件逐項記錄 12 節點為 Workspace readable，另保留兩組完整色輪值與 Workspace 截圖一致的摘要。
- 原始截圖與各驗證輸入的 hash 尚未完整對應，不宣稱所有節點、值域或軟體版本均已獨立驗證。

**固定 offset 不是所有 OM-3 JPG 的通用位址。** 不應直接用它修改任意照片。

歷史相機韌體、OM Workspace 與作業系統版本均為 `UNKNOWN`；本次文件整理日期不代表當時測試環境。

## 不包含

本包不含照片、base templates、截圖、icon、校正資料、presets、應用程式原始碼、EXE、writer 或 parser。
沒有可執行程式，也沒有附上可獨立重現歷史實驗的原始資料包。

Mono、WB、Tone、OES、C1-C5 Wi-Fi writer、韌體與完整成像演算法不在此研究公開範圍。
本包不提供相機連線或寫入操作。

## 來源與定位

內容依據私人保存的樣本觀察、既有實作與歷史交接紀錄重新撰寫；沒有直接散布完整工程或原始交接文件。
照片來源聲明、程式定義與歷史 Workspace 驗證主張分開列示，詳見證據文件。

這是獨立研究，與 OM Digital Solutions、OM SYSTEM、Olympus 或 OM Workspace 無隸屬或官方背書關係。
所提產品名稱僅用於辨識研究對象。

## 文件授權

本研究文件採用 [Creative Commons Attribution 4.0 International（CC BY 4.0）](https://creativecommons.org/licenses/by/4.0/)；完整條款見 [LICENSE](LICENSE)。

授權範圍是本包的 `README.md`、`CHANGELOG.md`、`docs/FORMAT_NOTES.md` 與 `docs/EVIDENCE_AND_LIMITATIONS.md` 中作者有權授權的文件表達。
這不授權完整 Recipe Studio 程式，也不授權未附的照片、templates、icon、校正資料、presets 或第三方原件。
產品商標不在授權範圍內；不對技術事實或公有領域內容增設限制，也不因他人參照本筆記自行實作而自動將其程式套用本文件授權。

署名請使用專案名稱 **OM-3 Recipe JPG Color Wheel Notes**，附上取得文件的來源連結、CC BY 4.0 連結，並指出是否修改。
這是署名方式說明，不添加 CC BY 4.0 以外的使用限制；完整權利義務以 LICENSE 為準。
文件依現況提供，不保證內容完整、任意 JPG 相容或適合特定用途，詳見授權條款的免責規定。

文件授權已確認；實際 GitHub 上傳尚未執行。

## English Summary

Documentation-only research notes on the 12 Color Profile nodes in known OM-3 Recipe JPG layouts.
They distinguish byte observations from historical Workspace verification claims and disclose missing original evidence.
Offsets are layout-specific, not a universal JPEG specification. Historical firmware, Workspace and OS versions remain `UNKNOWN`.
No application code, photos, templates, icons, calibration data or executable is distributed.
The authored research documentation is licensed under [CC BY 4.0](LICENSE); this does not license the full application or omitted third-party material.
Credit the project as **OM-3 Recipe JPG Color Wheel Notes**, retain the source and license links, and indicate changes. GitHub publication has not yet been performed.
