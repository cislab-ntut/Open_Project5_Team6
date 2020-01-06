# 題目 Anti-Money Laundering in Bitcoin：with Graph Convolutional Network for Financial Forensics
## 1. Toward Financial Inclusion 
“貧窮的代價是昂貴的，” 這是普惠金融倡導者的共同信條。
反洗錢法規（AML），導致較為貧困的人民（低收入戶、移民和難民）它們無法輕易進入金融體系，但因為非法行業的龐大，始得無法取消AML。為解決洗錢的問題，必須減少對數據分析的複雜挑戰，因此提出「使用正確的工具和開放數據，是否可以幫助調和安全的需求與金融普惠的原因嗎？」

### 1.1	AML in acryptocurrency world
因為比特幣網絡所引入的加密貨幣，使貨幣轉移類創業公司專注使用比特幣和其他加密貨幣作爲“軌道” ，以實現低成本、點對點的跨境資金轉移。但相對的很多犯罪分子利用比特幣的假名特性隱藏在人們的視線中，然後進行勒索軟件攻擊，並經營暗網市場，以交換非法商品和服務。因此美國金融犯罪執法網絡發佈《銀行保密法》（BSA）要求貨幣服務業務（MSB）生成衡量洗錢、恐怖主義金融和其他金融犯罪的風險評估。這些評估基於客戶構成、服務地區和提供的金融產品或服務。因此在貨幣服務業務（MSB）不僅必須報告可疑帳戶，而且必須對它們進行凍結或關閉帳戶。  
在傳統金融零散的數據生態系統中，這方面的合規性通常是通過 MSB 之間的通話來執行的。但在比特幣的開放系統中，整個圖形交易網絡數據是公開的，這對於調查人員抓取使用假名的犯罪分子是關鍵優勢。

## 2 THE ELLIPTIC DATA SET
Elliptic是一家致力於保護加密貨幣生態系統不受犯罪活動侵害的加密貨幣情報公司。

我們介紹了Elliptic Data Set，他是一個具有手工特徵的比特幣交易的圖網絡，Elliptic也同意公開此資料集。它構成了世界上最大的帶有標籤的交易數據集，可在任何加密貨幣中公開取得。

### 2.1 Graph Construction
Elliptic Data Set將比特幣交易分成兩個類別，合法類別及非法類別。合法類別包含交易所、錢包供應商、礦工、合法服務等等，非法類別包含詐騙、惡意軟體、恐怖組織、勒索軟體、龐茲騙局等等。

從原始比特幣數據中，建造並標記了一個圖形。其中每個節點代表交易，邊表示從一次交易到下一筆交易比特幣(BTC)的流動。

如果發起交易的實體(即控制與特定交易的輸入地址相關的私鑰實體)屬於合法類別，則該交易被視為合法(若是非法類別則被視為非法)。

重要的是僅使用公開可得到的資訊來建構所有的特徵。

#### 2.1.1 Nodes and Edges.
有203769個節點交易和234355個有向邊的支付流。為了便於理解，在撰寫本文時，使用相同的圖形表示，整個比特幣網絡大約有483M個節點和1.1B個邊。在Elliptic Data Set中，有2%(4545)被標記成class1(非法)，21%(42019)被標記成class2(合法)。剩餘的交易未標明合法或非法，但是有其他的特徵。

#### 2.1.2 Features.
每個節點具有相關的166個特徵。前94個特徵表示有關交易的本地訊息，包括time step、輸入和輸出的數量、交易費用、產出量和總共的價格，例如輸入和輸出所接受(花費)的平均BTC，以及與輸入和輸出相關交易的平均數量。

其餘72個稱為aggregated features，是從中心節點向前或向後一個的總交易資訊而獲得 - 給出相同的訊息數據(輸入和輸出數量，交易費用等等)的鄰居的最大、最小、標準差和相關係數。

#### 2.1.3 Temporal Information.
時間戳與每個節點有關，表示比特幣網絡確認交易的時間估計。有49個不同的time step，平均間隔約兩周。每個time step都包含彼此之間少於三小時的single connected component of transactions;沒有連接不同time steps的邊。

顯然在特定time step中的節點具有彼此非常接近的相關時間戳，因此有效的可以將他們的每個視為時間上的瞬間snapshot。

每個time step的節點數在時間上相當均勻(從1000到8000個節點不等)。如圖一。

![image](https://github.com/cislab-yzu/Project6-5_Open/blob/master/%E8%AA%AA%E6%98%8E%E5%9C%96%E7%89%87/2-1-3.)
▲圖一 (上)數據集中不同段時間的非法節點數和合法節點數的比(下)節點數與time step的關係

### 2.2 Notes on Feature Construction
合法與非法的標記過程是基於啟發式推理的過程。例如更高數量的輸入和相同地址的重新使用，通常與更高的address-clustering[10]相關聯，這導致簽署交易的實體匿名性下降。另一方面，在一次交易中合併多個地址控制的資金可以提供交易成本(費用)方面的好處。隨之而來的是針對大量用戶請求而避開anonymity-preserving方法的實體可能是合法的(例如，交換)。相反，非法活動可能傾向於使用較少輸入的交易，以減少對address-clustring進行匿名處理的影響。

此外，在建構比特幣交易功能有兩個主要的挑戰。第一個是比特幣區塊鍊的大小，總計200GB的壓縮數據和大約4億筆已處理的交易。儘管並非所有交易都包含在本研究的子集合中，但仍然有必要存取完整個區塊鍊，以觀察參與所選的交易錢包完整的歷史。為了克服這個問題，Elliptic使用高興能的 all-in memory圖形引擎進行特徵運算。

第二個挑戰是來自數據的基本的圖結構和交易可以擁有鄰居數量的異質性。在建構72個aggregated features時，透過鄰居交易本地特徵建構的數據總和(最大、最小值等等)來解決異質性鄰居的問題。

通常，這個解決方法是次優的，因為它會讓大量的訊息流失。我們在即將進行的圖深度學習方法討論中解決此問題，他可能會更好的由local graph topology證明。
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

