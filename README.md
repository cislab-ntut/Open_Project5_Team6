# 題目 Anti-Money Laundering in Bitcoin：with Graph Convolutional Network for Financial Forensics
## 3.Task and Methods
防洗錢分析預測需要在大量且不斷增長的交易資料集內找到少數非法交易，以防治犯罪的角度而言，期望在不增加False Negative的條件下，減少False Positive的機率；以Elliptic 數據集來說，要對該數據做篩選交易，以評估與給定的往來於加密貨幣錢包的交易關聯風險，對unlabelled的比特幣交易進行合法性分類。

### 3.1 Benchmark Method
最常見用於二元分類的監督式學習有Logistic Regression、Random Forest、MLP(Multilayer Perceptron)，前兩項較常用於AML，但是這些方法並沒有graph information。
Elliptic Data Set中特徵分成兩種：
* Local Feature（94個）：時間步長、入度和出度、交易費、輸出量以及衍生統計特徵（如相關聯交易的平均數量、平均接受/使用的BTC）
* 聚合 Feature（72個）：通過從中心節點向後/向前一跳聚合交易信息來獲得（鄰居相關原生特徵的最大、最小、標準差和相關係數）

聚合特徵的加入可以改善效能；但是將purely feature-based method拓展至immediate neighbourhood的實現有一定挑戰性，促使Graph Convolutional Networks的應用。
### 3.2 GCN
![image](https://github.com/cislab-yzu/Project6-5_Open/blob/master/%E8%AA%AA%E6%98%8E%E5%9C%96%E7%89%87/3_2_1.PNG)
![image](https://github.com/cislab-yzu/Project6-5_Open/blob/master/%E8%AA%AA%E6%98%8E%E5%9C%96%E7%89%87/3_2_2.PNG)

