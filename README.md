# MDS_TeamF

組員 </br>
M11108030 楊智文 </br>
M11108027 蔡欣蓉 </br>
M11108035 黃紹閔 </br>
R11250012 蔡知諺 </br>

### 期末專案主題：微笑單車未來一小時的站點示警及需求預測
####專案介紹
背景：Youbike剩餘車數、車位數不穩定的情況下，需透過人力進行補車與拉車，有賴於對於下一時段剩餘車數、車位數之預測。
目標：解決台北市Youbike補拉車問題
資料集：
- ubike即時資料:
  https://mailntustedutw-my.sharepoint.com/:f:/g/personal/m11108027_ms_ntust_edu_tw/Ev5ggMiDcoBEqqgVjD24e8QB57LbSKJbVblVju9kfr_Y-A?e=PXPAeC

- 捷運站人流資料:
  https://mailntustedutw-  my.sharepoint.com/:f:/g/personal/m11108027_ms_ntust_edu_tw/EvQmF58hzWFOinGjb76RhCABddFfznGd_Y2KCh-1kC49Mg?e=B9eKSc

- 天氣資訊:
  https://mailntustedutw-my.sharepoint.com/:f:/g/personal/m11108027_ms_ntust_edu_tw/EnO7Cy56AnhJq3Ah4T1Vea0BjuyoJbEJc5KiAu7VqzvFpA?e=4NInyV

研究流程: 藉由t-SNE、PCA等方式對資料進行降維，緊接著使用k-means對全台北市ubike站點分群，分別將其分為兩群與三群並比較其差異

####建置預測模型
模型挑選: 
- Linear Regression:預測直覺、快速，容易觀察自變數與應變數之間變化的關係
- Random Forest:可考量自變數與應變數間非線性的關係，集成式學習可以透過調整參數提高預測準確度
- ARIMA:協助對時序型資料進行預測
訓練與測試集:
- 訓練集:2023/03/01 ~ 2023/05/23
- 測試集:2023/05/24 ~ 2023/05/30 (Predict by rolling window)

####預測結果
| Tables        |Linear Regression |  Random Forest  |     ARIMA     |
| ------------- | ---------------- | --------------- | ------------- |
| Adjusted R2   | 0.4922 / 0.5284  | 0.5136 / 0.5378 | 0.4711/0.5529 |
| RMSE          | 0.1394 / 0.1639  | 0.1372 / 0.1617 | 0.1742/0.1770 |

由上表可知，無論在兩群或三群的預測上，Random Forest模型表現都是最佳，平均預測誤差為0.13/0.16，但可以看出三個模型的解釋力都不高，針對此情況有兩種可能:
1. 本預測模型因資料搜集緣故，無法考量影響所有ubike站點的參數，導致某些特定站點的預測準確率相對低。
2. 上述模型 Adjusted R2 與 RMSE 皆是全部站點的平均結果

####Dashboard ubike站點挑選
ubike站點挑選有以下幾步驟:
1. 刪除資料缺失部分(新設立站點 or 已廢除站點)
2. 挑選Random Forest模型 RMSE < 0.15的站點

####Dashboard ubike站點水位情況判斷標準
- 針對各站點重複預測五次，取得該站點下個時段平均預測水位與水位標準差
- 計算該站點下個時段水位信賴區間
- 依據其信賴區間大小，分別給予高水位(藍燈)、正常水位(綠燈)、低水位(紅燈)
  高水位:信賴區間上界 => 0.85
  低水位:信賴區間下界 <= 0.15
  正常水位:信賴區間上界 < 0.85 & 信賴區間下界 > 0.15

####Dashboard 成果呈現
