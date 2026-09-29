# 實驗結果分析

本資料夾保存資料前處理、模型預測比較與評估結果圖片。本文件提供圖片索引與目前可以由實驗輸出支持的分析；完整程式、資料切分與訓練參數請參考 repository 根目錄的 notebooks。

> **資料說明**：原始 GNSS 資料、處理後 CSV、模型 checkpoint 與 `lightning_logs/` 不在公開 repository 中。圖片雖然已去除原始資料檔，但仍可能呈現資料的時間範圍與位移特徵，發布前請確認使用權限。

## 評估指標

- **MAE（Mean Absolute Error）**：預測值與實際值之間的平均絕對誤差，越小越好。
- **RMSE（Root Mean Squared Error）**：誤差平方平均後開根號，對較大的誤差更敏感，越小越好。

不同 notebook 的資料切分、輸出尺度與實驗設定可能不同，因此只有在相同資料集、相同切分與相同尺度下，才能直接比較模型分數。

## 資料前處理結果

以下圖片比較離群值處理前後的資料分布。移除離群值後，主要資料的趨勢通常更容易辨識，但這個步驟也可能移除真實的突發位移事件；實際是否合理需要搭配資料來源與現場事件確認。

| 地區與測站 | 原始資料 | 移除離群值後 |
| --- | --- | --- |
| 高雄 `g2` | [原始圖](Kaohsiung_g2_original.png) | [處理後](Kaohsiung_g2_remove.png) |
| 南投 `cn01` | [原始圖](Nantou_cn01_original.png) | [處理後](Nantou_cn01_remove.png) |
| 台北 `h23-g1` | [原始圖](Taipei_h23-g1_original.png) | [處理後](Taipei_h23-g1_remove.png) |

## 模型預測比較

以下圖片將實際位移與模型預測放在同一張圖中。判讀重點是趨勢方向、轉折時間、尖峰幅度，以及預測是否出現系統性偏移。

### 高雄：`g2`

| 模型 | 圖片 |
| --- | --- |
| GRU | [查看結果](GRU_Kaohsiung_movements_compare.png) |
| XGBoost | [查看結果](XGBoost_Kaohsiung_movements_compare.png) |
| TFT | [查看結果](TFT_Kaohsiung_movements_compare.png) |

高雄結果可用來觀察模型對整體位移趨勢與局部波動的掌握程度。若預測線在尖峰處明顯平滑，代表模型可能較擅長學習一般趨勢，卻低估短時間的快速變化。

### 南投：`cn01`

| 模型 | 圖片 |
| --- | --- |
| GRU | [查看結果](GRU_Nantou_movements_compare.png) |
| XGBoost | [查看結果](XGBoost_Nantou_movements_compare.png) |
| TFT | [查看結果](TFT_Nantou_movements_compare.png) |

南投結果可用來比較不同模型面對另一個測站時間序列時的穩定性。應特別注意模型在轉折區間的延遲，以及預測曲線是否過度貼近訓練資料的平滑趨勢。

### 台北：`h23-g1`

| 模型 | 圖片 |
| --- | --- |
| GRU | [查看結果](GRU_Taipei_movements_compare.png) |
| XGBoost | [查看結果](XGBoost_Taipei_movements_compare.png) |
| TFT | [查看結果](TFT_Taipei_movements_compare.png) |

台北結果可用來檢查模型在不同方向位移上的差異。若某一方向的實際值變化幅度較大，該方向的 MAE 或 RMSE 不宜直接與其他方向比較，應先確認是否已反轉換尺度或進行標準化。

## 各方向評估圖

這些圖片分別呈現 `EMove`、`NMove` 與 `HMove` 的評估結果。它們適合用來比較各方向的誤差分布與模型差異；若要報告正式結論，建議同時在 notebook 輸出數值表格。

### 高雄：`g2`

- `EMove`：[評估結果](Evaluation_Result_for_EMove_Kaohsiung-g2.png)
- `NMove`：[評估結果](Evaluation_Result_for_NMove_Kaohsiung-g2.png)
- `HMove`：[評估結果](Evaluation_Result_for_HMove_Kaohsiung-g2.png)

### 南投：`cn01`

- `EMove`：[評估結果](Evaluation_Result_for_EMove_Nantou-cn01.png)
- `NMove`：[評估結果](Evaluation_Result_for_NMove_Nantou-cn01.png)
- `HMove`：[評估結果](Evaluation_Result_for_HMove_Nantou-cn01.png)

### 台北：`h23-g1`

- `EMove`：[評估結果](Evaluation_Result_for_EMove_Taipei-g1.png)
- `NMove`：[評估結果](Evaluation_Result_for_NMove_Taipei-g1.png)
- `HMove`：[評估結果](Evaluation_Result_for_HMove_Taipei-g1.png)

## Notebook 中的數值結果

目前 notebook 的可見輸出包含以下 TFT 測試結果：

| 目標 | MAE | RMSE |
| --- | ---: | ---: |
| `EMove` | 0.0126 | 0.0215 |
| `NMove` | 0.0063 | 0.0112 |
| `HMove` | 0.0311 | 0.0543 |

這組數值來自 `TemperalFusionTransformer.ipynb` 的測試輸出。由於公開 repo 不含原始資料與完整訓練紀錄，不能只依這組數值宣稱 TFT 在所有地區或所有模型設定下都最佳。報告中應補充資料切分、預測尺度、亂數種子與套件版本。

## 結論與限制

1. 圖片能清楚呈現不同模型是否抓到整體趨勢，但只看曲線不等於完成模型比較。
2. MAE 與 RMSE 應在相同資料切分與尺度下比較；若資料經過 `MinMaxScaler`，需說明分數是否仍在縮放後尺度。
3. 離群值處理可能改善一般趨勢的預測，也可能掩蓋真正重要的突發事件，不能只依模型分數判定前處理一定正確。
4. 目前結果是研究實驗紀錄，不代表可直接用於實際坡地預警；正式使用前仍需要更多測站、時間區段與事件案例驗證。