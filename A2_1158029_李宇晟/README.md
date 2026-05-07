# 資料探勘作業 A2 - MIMIC3C Stratified Sampling 實驗

## 專案簡介
本專案使用 MIMIC3C 的 model-ready 資料，重做並延伸 W7_20260416 的分層抽樣實驗，重點檢驗在醫療不平衡分類任務中，Stratified 與 Random split 對 macro F1 的影響。

主要設計如下：
1. 二元不平衡資料設定為 9:1（y=1 約 10%）。
2. 三種資料量：n = 100、500、6000。
3. 七種模型：DecisionTree、KNN、LogisticReg、SVM、MLP、RandomForest、NaiveBayes。
4. 每組進行 20 個 seed，並比較 Stratified 與 Random 的 paired macro F1。

## 資料來源與欄位
1. 主要檔案：data/df_model_ready.csv（方案 A，使用 HW1 資料前處理方法）。
2. 目標欄位：target。
3. 防洩漏欄位（若存在會刪除）：ExpiredHospital、LOSdays、LOSgroupNum、AdmitDiagnosis。

## 專案結構
```text
A2_1158029_李宇晟/
├─ A2_1158029.ipynb      # 主實驗 notebook
├─ A2_1158029.md         # A2 報告文字版
├─ README.md             # 本文件
├─ DEBUG_LOG.md          # A2 除錯紀錄
├─ PROMPT_LOG.md         # A2 提示與操作紀錄
├─ requirements.txt
├─ data/
│  ├─ df_model_ready.csv
│  └─ mimic3c.csv
└─ output/
  ├─ mimic3c_results.csv
  ├─ significance_full.csv
  ├─ significance_table.md
  ├─ ceiling_effect_table.md
  ├─ std_ratio_table.md
  ├─ mimic3c_boxplot_n100.png
  ├─ mimic3c_boxplot_n500.png
  └─ mimic3c_boxplot_n6000.png
```

## 環境與安裝
1. 建立 Python 環境（conda 或 venv）。
2. 安裝套件：
```bash
pip install -r requirements.txt
```
若 requirements 不完整，可補安裝：
```bash
pip install ipykernel==7.2.0 matplotlib==3.7.5 seaborn==0.13.2 scipy==1.10.1 scikit-learn==1.3.2 ==2.0.3 numpy==1.26.4
```

## Notebook 流程摘要
1. 載入套件與輸出資料夾初始化。
2. 定義工具函式：
  - create_csv
  - make_imbalanced_subset
  - load_mimic3c
  - fit_and_score
  - run_one_dataset
  - paired_significance_table
  - ceiling_effect_table
  - std_ratio_table
  - plot_boxplot
3. 載入全資料集（預設 use_solution_a=True）。
4. 依 SIZE_GRID 迭代 n，先做 9:1 子抽樣，再進行 20 seeds 實驗。
5. 輸出 CSV、Markdown 表格與 boxplot 圖。

## 輸出檔案說明
1. output/mimic3c_results.csv
  - 每一列為一次 seed x strategy x model x n 的 macro F1 結果。
2. output/significance_full.csv
  - 每個 n x model 的 paired t-test（t_value、p_value、mean_diff、better）。
3. output/significance_table.md
  - p < 0.05 的子表（若無顯著則只剩表頭）。
4. output/ceiling_effect_table.md
  - n=500 下各模型平均 F1 與 stratified-random 差值標準差。
5. output/std_ratio_table.md
  - n=100 下 std(Stratified) / std(Random) 比值。
6. output/mimic3c_boxplot_n*.png
  - 三種資料量下模型與抽樣策略的 F1 分布箱型圖。

## 重現步驟
1. 開啟 A2_1158029.ipynb。
2. 依序自上而下執行所有 cell。
3. 檢查 output 內是否生成 8 個檔案（3 圖 + 5 表）。

## 驗證清單
1. Cell 執行完成且無中斷錯誤。
2. 螢幕輸出可看到 X shape、y=1 比例、特徵數量。
3. output/mimic3c_results.csv 筆數應為 840 列（3 n x 20 seeds x 2 strategy x 7 model）。
4. output/significance_full.csv 應為 21 列（3 n x 7 model）。
5. output/significance_table.md 若無顯著結果，顯示僅標題屬正常。

## 備註
1. 本 notebook 會先對各 seed 做 train/test split，再比較 Stratified 與 Random。
2. n=100 的 Random split 在個別 seed 可能出現極低少數類比例，導致波動偏大，這也是 std ratio 分析的重點。