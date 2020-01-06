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
GCN包含多層graph convolution，與Perceptron(認知器演算法)相似，但多了由spectral convolution激發 neighbourhood aggregation的步驟。
假設Elliptic Data Set的比特幣交易圖為G=(N,E)
* N：交易節點的集合
* E：BTC flow 的 edges集合
#### 3.2.1 GCN layer
第n層GCN的執行過程為 $H^{(n+1)} = \sigma(\hat{A}H^{(n)}W^{(n)})$
*  Input：adjacency矩陣 $A$ 和 embedding矩陣 $H^{(n)}$
*  Update：用weight矩陣 $W^{(n)}$ 更新 embedding矩陣 $H^{(n+1)}$
*  Output：embedding 矩陣 $H^{(n+1)}$
* $\hat{A}$是正規化的$A$，定義如下：
    * $\hat{A} = \tilde{D}^\frac{-1}{2}\tilde{A}\tilde{D}^\frac{-1}{2}$
    * $\tilde{A} = A + I$
    * $\tilde{D} = diag(\displaystyle\sum_{j}\tilde{A}_{ij})$
* $\sigma$是激勵函數，通常使用ReLU(Rectified Linear Unit)
* 初始embedding矩陣 $H^{(0)} = X$，來自節點特徵
* 共有L層的GCN最終輸出是softmax，由prediction probabilities組成的$H^{(L)}$

graph convolution層跟feed forward層相似，只差在$\hat{A}$，此矩陣是被graph Laplacian矩陣的spectral graph filtering激發，是Laplacian矩陣的線性函數結果；$\hat{A}$ 的乘法可以視為相鄰節點轉換嵌入的aggregation


#### 3.2.2 Two-Layer GCN & Skip-GCN
2-Layer GCN是常見的GCN，表示為 $H^{(2)} = softmax(\hat{A}\cdot ReLU(\hat{A}XW^{(0)})W^{(1)})$
若加入一個 **skip** 變量作為intermediate embedding $H^{(1)}$和輸入節點特徵 $X$ 的連接，能夠為模型帶來實際的幫助，被稱為Skip-GCN，記作 $H^{(2)} = softmax(\hat{A}\cdot ReLU(\hat{A}XW^{(0)})W^{(1)}+XW^{(1)})$
* $W^{(1)}$是作為connection的weight矩陣
* 當$W^{(0)}$和$W^{(1)}$皆為零，skip-GCN等同於Logistic Regression，因此skip-GCN的效力至少等同於Logistic Regression
