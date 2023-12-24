# MDS_TeamF

組員 </br>
M11108030 楊智文 </br>
M11108027 蔡欣蓉 </br>
M11108035 黃紹閔 </br>
R11250012 蔡知諺 </br>

### 期末專案主題：微笑單車未來一小時的站點示警及需求預測
#### 1. 專案介紹
背景：Youbike剩餘車數、車位數不穩定的情況下，需透過人力進行補車與拉車，有賴於對於下一時段剩餘車數、車位數之預測。
目標：解決台北市Youbike補拉車問題
資料集：
- ubike即時資料:
  https://mailntustedutw-my.sharepoint.com/:f:/g/personal/m11108027_ms_ntust_edu_tw/Ev5ggMiDcoBEqqgVjD24e8QB57LbSKJbVblVju9kfr_Y-A?e=PXPAeC

- 捷運站人流資料:
  https://mailntustedutw-my.sharepoint.com/:f:/g/personal/m11108027_ms_ntust_edu_tw/EvQmF58hzWFOinGjb76RhCABddFfznGd_Y2KCh-1kC49Mg?e=B9eKSc

- 天氣資訊:
  https://mailntustedutw-my.sharepoint.com/:f:/g/personal/m11108027_ms_ntust_edu_tw/EnO7Cy56AnhJq3Ah4T1Vea0BjuyoJbEJc5KiAu7VqzvFpA?e=4NInyV

研究流程: 藉由t-SNE、PCA等方式對資料進行降維，緊接著使用k-means對全台北市ubike站點分群，分別將其分為兩群與三群並比較其差異

#### 2. 進行全臺北市站點分群  

| 分群        | 2群 | 3群 |
| ----------- |:-------------:|:-----:|
| 降維        | t-SNE      | FPCA + t-SNE |
| 分群        | K-means      |  K-means |
| 分群評估指標 | Silhouette Coefficient|Silhouette Coefficient|

**2.1 分群後的地圖特徵**

2群：
- 紅色：捷運附近
- 藍色：住宅區

3群：
- 紅色：捷運沿線
- 橘色：距離捷運10分鐘距離
- 藍色：住宅

**2.2 分群後的時間特徵**  

2群：  
晚上：  
- 紅點：人們下班下課把車騎走，水位偏低
- 藍點：人們將車騎到住宅區，水位偏高
 
白天：  
- 紅點：人們上班上課將車騎到捷運站附近，銜接住宅與捷運站間的路程，水位上升
- 藍點：人們將車騎離開住宅區前往上班上課的地方，或是下一個交通工具的點歸還，因此這區域的水位偏低

3群：  
**將紅色那群的站點再多分一群橘色群的站點出來，而橘色站點推測為稍微被捷運影響的站點**  
晚上：  
- 紅點：因為較靠近捷運站，會一直有民眾歸還YouBike，水位在0.2~0.3間震盪
- 橘點：位於商業區或是距離捷運站較遠一點的地方，而民眾可能會把這邊的車直接騎回家，或是騎到其它搭乘大眾運輸工具的地方，因此水位會偏低
- 藍點：人們將車騎到住宅區，水位偏高

白天  
- 紅點：在早上上班上課時間，人們出捷運站後會把車借走，在白天有高度使用需求，因此水位偏低
- 橘點：商業區，民眾將車從其他地方騎到公司附近歸還，因此水位偏高
- 藍點：人們早上從住宅區騎出去上班上課的地點，因此白天的時候水位偏低

**2.3 分群特徵總比較**
| 特徵比較        | 2群 | 3群 |後續數據|
| ----------- |:-------------:|:-----:|:------:|
| 是否受捷運站影響| 紅:容易受影響/藍:不易受影響|紅:容易受影響/橘:部分受影響/藍:不易受影響|加入捷運人流|
| 水位        |紅:0.15 ~ 0.4/藍:0.2 ~ 06|紅:0.2 ~ 0.4/ 橘:0.15 ~ 0.55 /藍:0.2 ~ 0.6|--------|
| 平假日水位波動 |紅_振幅小、藍_振幅大|平日：振幅小/振幅大/振幅大</br> 週末/假日：振幅小/振幅中/振幅大|加入節日假日標籤|

**2.4 依照不同群的特性放置的不同變數**
| 放置變數 | 鄰近捷運站 | 5MA |t-24|t-23|t-22|t-3|t-2|t-1|是否為雨天|是否為節假日|捷運出-入t-24*7(上週同日同時段的淨人流)|捷運出-入t-24|捷運出-入5MA| 
| :-----------: |:-------------:|:-----:|:------:|:-----:|:-----:|:------:|:-----:|:-----:|:------:|:-----:|:-----:|:------:|:-----:|
| 2群 | 容易 |V|V|V|V|V|V|V|V|V|V|V|V|
| 2群 | 不容易 |V|V|V|V|V|V|V|V|V|-|-|-|
| 3群 | 容易 |V|V|V|V|V|V|V|V|V|V|V|V|
| 3群 | 部分/不容易 |V|V|V|V|V|V|V|V|V|-|-|-|


#### 3. 建置預測模型
模型挑選: 
- Linear Regression:預測直覺、快速，容易觀察自變數與應變數之間變化的關係
- Random Forest:可考量自變數與應變數間非線性的關係，集成式學習可以透過調整參數提高預測準確度
- ARIMA:協助對時序型資料進行預測  

訓練與測試集:
- 訓練集:2023/03/01 ~ 2023/05/23
- 測試集:2023/05/24 ~ 2023/05/30 (Predict by rolling window)

#### 4. 預測結果
| Tables        |Linear Regression |  Random Forest  |     ARIMA     |
| ------------- | ---------------- | --------------- | ------------- |
| Adjusted R2   | 0.4922 / 0.5284  | 0.5136 / 0.5378 | 0.4711/0.5529 |
| RMSE          | 0.1394 / 0.1639  | 0.1372 / 0.1617 | 0.1742/0.1770 |

由上表可知，無論在兩群或三群的預測上，Random Forest模型表現都是最佳，平均預測誤差為0.13/0.16，但可以看出三個模型的解釋力都不高，針對此情況有兩種可能:
1. 本預測模型因資料搜集緣故，無法考量影響所有ubike站點的參數，導致某些特定站點的預測準確率相對低。
2. 上述模型 Adjusted R2 與 RMSE 皆是全部站點的平均結果

#### 5. Dashboard ubike站點挑選
ubike站點挑選有以下幾步驟:
1. 刪除資料缺失部分(新設立站點 or 已廢除站點)
2. 挑選Random Forest模型 RMSE < 0.15的站點

#### 5.1 Dashboard ubike站點水位情況判斷標準
- 針對各站點重複預測五次，取得該站點下個時段平均預測水位與水位標準差
- 計算該站點下個時段水位信賴區間
- 依據其信賴區間大小，分別給予高水位(藍燈)、正常水位(綠燈)、低水位(紅燈)
  高水位:信賴區間上界 => 0.85
  低水位:信賴區間下界 <= 0.15
  正常水位:信賴區間上界 < 0.85 & 信賴區間下界 > 0.15

#### 5.2 Dashboard 成果呈現
