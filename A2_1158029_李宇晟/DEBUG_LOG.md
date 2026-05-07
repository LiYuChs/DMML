# DEBUG LOG

## 1) FileNotFoundError: data/df_model_ready.csv 不存在

### 錯誤訊息
```python
FileNotFoundError: [Errno 2] No such file or directory: 'data/df_model_ready.csv'
```

### 發生原因
load_mimic3c(use_solution_a=True) 會直接讀取 data/df_model_ready.csv。若尚未執行 A1 或檔案路徑不正確，會讀不到。

### 修正方式
1. 確認 data/df_model_ready.csv 存在。
2. 或改用 load_mimic3c(use_solution_a=False) 並檢查程式中 target 欄位來源。

---

## 2) KeyError: 'target' 欄位不存在

### 錯誤訊息
```python
KeyError: 'target'
```

### 發生原因
方案 A 預期資料含有 target 欄位。若提供的是原始 mimic3c.csv 或欄位名稱不同，會在 y = df['target'] 失敗。

### 修正方式
1. 使用正確的 model-ready 檔案。
2. 先印出 df.columns 檢查標籤欄位名稱。
3. 若是原始檔，改用方案 B 並指定正確標籤欄位。

---

## 3) ValueError: 資料量不足以進行指定抽樣

### 錯誤訊息
```python
ValueError: 資料量不足以進行指定大小與比例的抽樣
```

### 發生原因
make_imbalanced_subset() 會強制抽到 n_total 且少數類比例為 0.1。若母體資料少數類或多數類樣本不足，將拋出錯誤。

### 修正方式
1. 降低 n_total。
2. 調整 imbalance_ratio。
3. 先檢查 y==1 與 y==0 的可用樣本數。

---

## 4) PermissionError: CSV 正在被其他程式占用

### 錯誤訊息
```python
PermissionError: csv file is currently being used by another program. Please close the file and try again.
```

### 發生原因
create_csv() 會先嘗試刪除舊檔後再寫入，若檔案被 Excel 或編輯器占用，os.remove 會失敗。

### 修正方式
1. 關閉 output 內開啟中的 CSV。
2. 重新執行輸出 cell。

---

## 5) t-test 結果表看似空白

### 現象
output/significance_table.md 只有標題，沒有任何資料列。

### 發生原因
該表只保留 p < 0.05 的結果；若 21 組都不顯著，輸出就會只有欄位名稱。

### 修正方式
1. 先檢查 output/significance_full.csv 是否有完整 21 列。
2. 確認這是統計結果本身，而非程式錯誤。

---

## 6) SVM 警告或訓練時間較長

### 現象
SVC 在特徵數較高或資料較大時訓練時間較長，可能伴隨收斂相關提醒。

### 發生原因
每個 seed 都要重訓 7 個模型，且包含 RBF 核 SVM 與 MLP，總訓練成本高。

### 修正方式
1. 測試階段暫時將 SIZE_GRID 改為 [100]。
2. 減少 n_repeats（例如 5）。
3. 正式跑完整報告時再恢復原設定。

---

## 7) NaN 出現在 std ratio

### 錯誤/現象
當分母 std(Random) 幾乎為 0 時，ratio 可能非常大、非常小或不穩定。

### 發生原因
某些模型在特定 n 下分數變異極小，導致比例數值敏感。

### 修正方式
1. 先確認原始 f1 序列是否合理。
2. 必要時在報告中標註該比值為「接近 0 變異分母下的極端值」。

---

## 8) 輸出圖檔缺失

### 現象
output 內只看到 CSV，沒有 mimic3c_boxplot_n*.png。

### 發生原因
通常是最後一段繪圖迴圈未執行、執行中斷，或 matplotlib backend 造成 cell 失敗。

### 修正方式
1. 單獨重跑包含 plot_boxplot 的 cell。
2. 確認 OUTPUT_DIR 已建立且有寫入權限。
