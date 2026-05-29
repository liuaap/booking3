# 醫師上班時段模型實作規則

## 核心模型

1. 時段卡來源永遠是診所營業時間。
   - 固定模式使用診所固定營業時間。
   - 臨時模式使用該日期的有效營業時間。

2. 醫師在每張時段卡內設定自己的上班時段。
   - 儲存欄位為 `workRanges`。
   - `workRanges` 是醫師上班時間表的主要資料來源。
   - `workRanges` 不應由預約時格反推為唯一來源。

3. 每張時段卡必須有上班狀態。
   - 欄位位置是時段卡內的 `slot-segment-kind`。
   - 狀態值目前為：
     - `openBooking`: 開放預約
     - `phoneBooking`: 電話預約
     - `offDuty`: 不上班
   - 狀態應儲存在對應 `workRanges[].status`。

4. 時段卡不得因沒有時格而消失。
   - 沒有線上預約時格，只代表該上班時段沒有開放線上預約。
   - 不代表醫師不上班。
   - 畫面應保留時段卡並顯示空狀態說明。

5. 格距只是時格篩選器。
   - 例如「每 30 分鐘」只用來篩出可顯示、可選取的線上預約時格。
   - 格距不得決定醫師是否上班。
   - 格距不得決定時段卡是否存在。

6. `enabledTimes` 只代表線上預約時格。
   - 只有 `workRanges[].status === "openBooking"` 的時段才可產生 `enabledTimes`。
   - `phoneBooking` 與 `offDuty` 不應新增線上預約時格。

7. 已被使用的時格必須無條件顯示。
   - 不受格距篩選影響。
   - 不受目前上班狀態影響。
   - 用途是提醒醫師此時間已有預約。

## 資料責任

- `workRanges`: 醫師上班時段與狀態。
- `enabledTimes`: 線上開放預約的時格。
- `segmentSlotFilters`: 每張卡的時格篩選間隔。
- `segmentSplits`: 同一營業時間卡內複製出的分割編輯區。
- `editableRangeOverrides`: 編輯中每張卡的起訖時間。
- `segmentStatusModes`: 編輯中每張卡的上班狀態，儲存時寫入 `workRanges[].status`。

## 上班時間表顯示

- `openBooking` 需要顯示在醫師上班時間表。
- `phoneBooking` 需要顯示在醫師上班時間表，但不產生線上預約時格。
- `offDuty` 需要保存狀態，但不顯示為醫師上班時間。

## 實作注意

- 讀取排班時應優先使用 `workRanges` 還原時段卡。
- 舊資料若沒有 `workRanges`，才可用 `enabledTimes` 或 `displayStartTime/displayEndTime` 做 fallback。
- 儲存前需要檢查 `workRanges` 是否互相重疊。
- 分割取消後若分割數歸零，應刪除對應 `segmentSplits` key，避免留下髒狀態。
