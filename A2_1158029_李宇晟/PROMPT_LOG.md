# PROMPT LOG

## 01. 建立 A2 實驗框架

### Q
請幫我把 W7 的 stratified vs random 比較流程移植到 MIMIC3C，並一次跑 7 個模型。

### A（執行重點）
1. 建立 run_one_dataset()，支援 7 模型與 20 seeds。
2. 每個 seed 依 strategy 切 train/test。
3. 以 macro F1 作為統一指標。

---

## 02. 強制不平衡比例抽樣

### Q
我要固定資料集比例為 9:1，再比較 Stratified 和 Random。

### A（執行重點）
1. 新增 make_imbalanced_subset()。
2. 先抽到 n_total，再讓 y=1 比例固定為 0.1。
3. 若樣本不足則主動拋錯，避免靜默失敗。

---

## 03. 載入資料方案切換

### Q
希望可以切換使用 HW1 的 model-ready 檔或原始 mimic3c.csv。

### A（執行重點）
1. 新增 load_mimic3c(use_solution_a=True)。
2. 方案 A 直接使用 data/df_model_ready.csv。
3. 方案 B 讀 mimic3c.csv 並做簡化編碼流程。
4. 補上標籤洩漏欄位防禦性移除。

---

## 04. 匯出結果與統計表

### Q
請把結果整理成 CSV，顯著性另外輸出 markdown 表格。

### A（執行重點）
1. create_csv() 統一輸出格式（utf-8-sig）。
2. 匯出 output/mimic3c_results.csv。
3. 匯出 output/significance_full.csv。
4. 匯出 output/significance_table.md（只保留 p < 0.05）。

---

## 05. 補齊 W7 的兩個延伸分析

### Q
我需要 ceiling effect 和 std ratio 的對照表。

### A（執行重點）
1. ceiling_effect_table(n_focus=500)。
2. std_ratio_table(n_focus=100)。
3. 兩張表都輸出成 markdown 檔，供報告直接引用。

---

## 06. 產生可視化圖

### Q
每個 n 都要有 stratified/random 的 boxplot 圖。

### A（執行重點）
1. 新增 plot_boxplot(df_results, n)。
2. 輸出三張圖：
   - output/mimic3c_boxplot_n100.png
   - output/mimic3c_boxplot_n500.png
   - output/mimic3c_boxplot_n6000.png

---

## 07. 顯著表為空的解讀

### Q
為什麼 significance_table.md 只有標題？

### A（執行重點）
1. 先確認 significance_full.csv 有完整 21 組結果。
2. 說明 significance_table.md 是「只顯示顯著組」的過濾後檔案。
3. 無顯著時只剩標題是預期行為。

---

## 08. 報告數值校正

### Q
請依 output 檔案檢視 A2 報告中數據敘述邏輯是否衝突。

### A（執行重點）
1. 以 significance_full.csv、ceiling_effect_table.md、std_ratio_table.md 作為唯一數據來源。
2. 修正 mean_diff 正負方向、最高 F1 模型、diff_std 和 ratio 解讀。
3. 同步修正結論段文字，避免與表格矛盾。

---

## 09. 文件重整請求

### Q
請參考 A1 folder，再次檢視格式是否正確。

### A（執行重點）
1. README 改為 A2 實驗導向，不再沿用 A1 前處理敘述。