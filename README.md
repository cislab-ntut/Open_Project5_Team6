# 題目 Anti-Money Laundering in Bitcoin：with Graph Convolutional Network for Financial Forensics
## 1. Toward Financial Inclusion 
“貧窮的代價是昂貴的，” 這是普惠金融倡導者的共同信條。
反洗錢法規（AML），導致較為貧困的人民（低收入戶、移民和難民）它們無法輕易進入金融體系，但因為非法行業的龐大，始得無法取消AML。為解決洗錢的問題，必須減少對數據分析的複雜挑戰，因此提出「使用正確的工具和開放數據，是否可以幫助調和安全的需求與金融普惠的原因嗎？」

### 1.1	AML in acryptocurrency world
因為比特幣網絡所引入的加密貨幣，使貨幣轉移類創業公司專注使用比特幣和其他加密貨幣作爲“軌道” ，以實現低成本、點對點的跨境資金轉移。但相對的很多犯罪分子利用比特幣的假名特性隱藏在人們的視線中，然後進行勒索軟件攻擊，並經營暗網市場，以交換非法商品和服務。因此美國金融犯罪執法網絡發佈《銀行保密法》（BSA）要求貨幣服務業務（MSB）生成衡量洗錢、恐怖主義金融和其他金融犯罪的風險評估。這些評估基於客戶構成、服務地區和提供的金融產品或服務。因此在貨幣服務業務（MSB）不僅必須報告可疑帳戶，而且必須對它們進行凍結或關閉帳戶。  
在傳統金融零散的數據生態系統中，這方面的合規性通常是通過 MSB 之間的通話來執行的。但在比特幣的開放系統中，整個圖形交易網絡數據是公開的，這對於調查人員抓取使用假名的犯罪分子是關鍵優勢。

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
![image](https://github.com/cislab-yzu/Project6-5_Open/blob/master/%E8%AA%AA%E6%98%8E%E5%9C%96%E7%89%87/3_2_1.PNG)
![image](https://github.com/cislab-yzu/Project6-5_Open/blob/master/%E8%AA%AA%E6%98%8E%E5%9C%96%E7%89%87/3_2_2.PNG)

