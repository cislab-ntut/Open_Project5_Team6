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

![image](https://github.com/cislab-yzu/Project6-5_Open/blob/master/%E8%AA%AA%E6%98%8E%E5%9C%96%E7%89%87/2-1-3.jpg)
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

### 3.3 Temporal Modeling
金融數據在它原來的性質上本來就具有時間性，這是因爲交易是有時間戳記的。隨著系統不斷的持續進化，我們可以很合理的去假設存在著某種動力，儘管它是隱藏起來(或是難以觀察出來)的。如果一個預測模型是以捕捉動態的方式設計的，那麼它將更加有用。這樣，在預先給定好時間的訓練模型，可以更好地推廣到後續發展。模型捕捉到的系統動力越好，其所能進入的視界就越長。

一般用來擴展GCN的時間模型是使用EvolveGCN [19]，它會爲每個步驟計算單獨的 GCN 模型，然後通過遞歸神經網絡（RNN）將這些GCN連接起來，以捕捉其系統動力。因此，在未來的GCN模型是從過去的模型不斷進化並捕捉其動力演變而來的。

在EvolveGCN中，GCN權重被集體視爲系統狀態。通過使用RNN（例如 GRU)，模型會在每次系統輸入時進行更新。輸入的東西必須是當前時間點的圖形信息，而圖形信息可以多種方式實體化，在EvolveGCN中，它用圖中top-k 個有影響的節點的嵌入來表示。

#### 3.3.1 循環神經網路(Recurrent Neural Network, RNN)
如果要建立一個語言的相關模型，必須要能夠考慮上下文的關係，因此，有學者提出『循環神經網路』(Recurrent Neural Network, RNN)演算法，它是『自然語言處理』領域最常使用的 Neural Network 模型，簡單的RNN模型(Vanilla RNN)額外考慮前文的關係，把簡單迴歸的模型 (y=W*x+b)，改為下列公式，其中h就是預測值(y)，同樣，將它加一個 Activation Function(通常使用 tanh)，就變成第二個公式：
![image alt](https://ithelp.ithome.com.tw/upload/images/20171210/20001976JvMnANowAr.jpg)
![image alt](https://ithelp.ithome.com.tw/upload/images/20171210/20001976JwABjxzSAr.jpg)
![image alt](https://i.imgur.com/x320pk6.png)
▲此圖為RNN示意圖

#### 3.3.2 GRU 門控循環單元(Gated Recurrent Unit)
GRU是循環神經網絡的一種，是為了解決長期記憶和反向傳播中的梯度等問題而提出來的。

為了解決標準RNN的梯度消失問題，GRU使用了更新門（update gate）與重置門（reset gate）。 這兩個門控機制的特殊之處在於，它們能夠保存長期序列中的信息，並且不會隨時間而清除或因為與預測不相關而可移除。

以下展示了個別門控循環單元的具體結構

![image alt](https://i.imgur.com/k7xSKwk.jpg)

下面是指引符號

![image alt](https://i.imgur.com/Cyu8PJI.jpg)

1.更新門

在時間為t時，需要下列公式計算更新門z_t

![image alt](https://i.imgur.com/XzuRzxF.jpg)

其中x_t為第t個時間步的輸入向量，即輸入序列X的第t個分量，它會經過一個線性變換（與權重矩陣W（z）相乘）。h_（t-1）保存的是前一個時間步t-1的信息，它也同樣經過一個線性變換。更新門將兩個部分信息相加並存到Sigmoid激活函數中，因此將激活結果壓縮到0到1之間。以下是更新門在整個單元的位置與表示方法。
![image alt](https://i.imgur.com/RZeT6xE.jpg)

2.重置門
重置門主要決定了到底有多少過去的信息需要遺忘，我們可以使用以下公式表達：
![image alt](https://i.imgur.com/L9RTDl0.jpg)

這個公式和更新門的是一樣的，只是線性變換的參數和用處不同而已，下圖展示出運算過程的表示方式。
![image alt](https://i.imgur.com/bI2vKr8.jpg)

3.當前記憶內容
在重置門的使用中，新的記憶內容將使用重置門存儲過去相關的信息，它的公式為：
![image alt](https://i.imgur.com/93MKjaZ.jpg)

輸入x_t與上一時間步信息h_（t-1）先通過一個線性變換，即分別右乘矩陣W和U。
計算重置門r_t與Uh_（t-1）的Hadamard乘積，即r_t與Uh_（t-1）的對應元素乘積。因為前面計算的重置門是一個由0到1組成的向量，它會衡量門控開啟的大小。例如某個元素對應的門控比例為0，那麼它就代表這個元素的信息完全被遺忘掉了。該Hadamard乘積將確定所要保留與遺忘的以前的信息。
將這兩部分的計算結果相加再補充雙曲正切激活函數中。該計算過程可表示為：
![image alt](https://i.imgur.com/ebFWYlm.jpg)

4.當前時間的最終記憶
在最後一步，網絡需要計算h_t，該向量將保留當前單元的信息並傳遞到下一個單元中。在這個過程中，我們需要使用更新門，它決定了當前記憶內容h'_t和前一時間步 h_（t-1）中需要收集的信息是什麼。這一過程可以表示為：

![image alt](https://i.imgur.com/Se2V31I.jpg)

z_t為更新門的激活結果，它同樣以門控的形式控制了信息的流入。
z_t與h_（t-1）的Hadamard乘積表示前一時間步保留到最終記憶的信息，該信息加上當前記憶保留至最終記憶的信息就等於最終門控循環單元輸出的內容。
以上表達式可以展示為：
![image alt](https://i.imgur.com/QGbqP3Q.jpg)

由上述步驟可以知道門控循環單元不會隨時間而清除以前的信息，它會保留相關的信息並傳遞到下一個單元，因此它利用了全部信息而避免了梯度消失問題。

## 4. Experiments

以下是用Elliptic Data Set的實驗結果，訓練和測試資料以7:3的比例切分。

用測試合法/非法的預測分類模型：
 - Logistic Regression
 - 隨機森林
 - Multilayer Perceptron


MLP中一個隱藏層，由50個神經員組成，訓練200 epoch，learning rate為1。
使用166個特徵及94個局部特徵來評估模型(如下表)
![image](https://i.imgur.com/NT60RMx.jpg)
```
上半：沒有利用graph structure information的結果，每個模型都顯示了具有不同輸入的結果：AF->所有特徵，LF->局部特徵，NE->由GCN計算的節點embeddings。
下半：使用GCN的結果
```

三種模型中，聚合的信息的準確性較高，解釋了graph structure information的重要。作者將從GCN獲得的節點嵌入與原始特徵X連接起來。結果增強的特徵集提高了全特徵（AF + N E）和局部特徵（LF + N E）模型的精度。

下表比較了非時態GCN和時態EvolveGCN的預測性能。結果顯示 EvolveGCN的性能優於GCN，進一步研究的方法是使用其他形式的系統輸入來驅動GRU內部的重複更新。

![image](https://i.imgur.com/5QqF3sk.png)
```
GCN v.s. EvolveGCN
```
![image](https://i.imgur.com/mmMN4mX.png)

黑市關閉：反洗錢的一個重要考慮因素是預測模型對新出現事件而呈現出的穩健性。這一數據集的一個有趣方面，是在數據的時間跨度內有一個黑市突然被關閉。如上圖所示，此事件導致所有方法在黑市關閉後的表現都出現了不佳的情況。即使是一個隨機森林模型，在每一個測試時間步長後重新訓練，假設每次測試後都能獲得真實的信息，也無法可靠地捕獲黑市關閉後新的非法交易。對於此類事件的穩健性，是需要解決的重大挑戰。


## 5. Discussion
隨機森林方法顯著的優於Logistic Regression方法，也優於有graph structure information的GCN。
* 隨機森林使用投票的機制，對數個決策數的預測結果進行訓練。
* GCN則和大多數深度學習模型一樣，使用Logistic Regression最為最後的輸出層。


問題：
能否將隨機森林與graph neural network?
在運行隨機森林前，用GCN計算的embeddings增加節點的特徵，但沒什麼顯著效果。
利用feedforward neural network的節點進行參數化，將隨機森林和神經網路結合在一起，但沒建議如何整合資訊。
未來作者將會用別的決策樹替換GCN的Logistic Regression輸出層，進而實現end-to-end的訓練。

