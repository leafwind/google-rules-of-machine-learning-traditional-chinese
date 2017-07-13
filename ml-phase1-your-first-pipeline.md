# 你的第一條 Pipeline

對你的第一條 pipeline，專注在你的系統基礎建設 \(infrastructure\)   上。

雖然去構思那些你將要去做的天馬行空 ML 演算法很有趣，但如果無法在一開始就信任你的 pipeline，你將會很難搞清楚發生什麼事。

> 按：拿料理比喻：你不要會想要在端上料理之後才試圖找出是哪一種原料過期、有毒，或是哪一個產線機器裡面有屍體；所以在做出好吃的創意料理之前，第一件事情得先確保你的資料生產線沒有問題，這是基本卻是最常見也最麻煩的問題。

### Rule 4 - 確保第一個模型 \(model\) 簡單，並做好正確的基礎建設

第一個模型為你的產品提供了最大的推進 \(boost\)，所以它不需要很花俏，但你將會遇到很多預期之外的基礎建設問題。

在任何人可以使用你那很 cooool 的新 ML 系統之前，你必須決定：

1. 如何取得 examples \(i.e. training data\) 給你的學習演算法使用。

2. 初步決定「好」跟「壞」在你的系統中指的是什麼。

3. 如何將 model 整合到你的應用？  
   你可以將 model 應用到線上，或是預先在線下算好並存到一張表。比如你可以預先分類好網頁，將結果存起來給應用查詢；但若是要分類聊天訊息，你可能會需要在線上即時分類。

選擇簡單的特徵 \(features\) 會更容易保證：

1. 這些特徵正確地「到達」\(reach\) 你的演算法

   > 按：無法正確到達的情況，指的就是特徵生錯了，或是特徵更新失敗等情況。至於什麼叫做「簡單」的特徵？則是你不需要將特徵做複雜的轉換，或是經過很多不同的系統、也不需要仰賴額外的系統 input，以致於在中間增加出差錯的可能。

2. 模型學到合理的權重 \(weights\)。

   > 按：承上，壞掉的特徵常會讓你的權重爆掉，如果連權重都不合理，那後面都會是做白工。

3. 這些特徵正確地在「伺服器上」到達你的演算法

   > 按：特別強調這點，表示作者很清楚 production 環境有太多變因：機器資源（CPU、記憶體、硬碟）、網路連線、環境設定不一致、不可抗力等因素，都會是問題，簡單來說上線就是戰場，任何事情都會發生。

一旦你有一個能可靠地做到這三點的系統，你已經做好大部分的工作了。你的簡單模型提供了最基本的指標跟行為，以讓你可以用來測試更多複雜的模型。

有些團隊（甚至）會將目標訂在「中性」的第一次推出（系統）-- 也就是第一次推出的時候，特別不將機器學習的成效擺在第一位，以避免分心。

### Rule 5 - 將測試基礎建設與測試機器學習分開

確保基礎建設是可以測試的，並且系統的學習部分是被封裝的 \(encapsulated\) 以便你可以測試周邊的所有環境，特別是：

1. 測試將 data 餵進演算法。檢查該被填的 feature columns 有被填好。如果隱私政策允許，手動檢查你的訓練演算法的輸入。

   如果可能，檢查 pipeline 裡面的統計數據、跟其他地方做比較，像是 RASTA。

   > 按：關於 RASTA，我找到的解釋是 Google 的一套 A/B testing experiment framework，它可以分流 production 流量並讓你對不同屬性的流量做切割。所以這裡的意思應該是：找一些類似（或同樣來源）的 pipeline 去驗證一下你的演算法拿到的數據有沒有異常。

2. 測試將模型搬出訓練演算法。確保模型能在 serving environment  給出跟訓練環境同樣的成效 \(see Rule **\#37**\)。

機器學習有不可預測性，所以確保你有測試 have tests for the code for creating examples in training and serving, and that you can load and use a fixed model during serving

並且，了解你的資料也是很重要的，參考 [Practical Advice for Analysis of Large, Complex Data Sets.](http://www.unofficialgoogledatascience.com/2016/10/practical-advice-for-analysis-of-large.html)

### Rule 6 - 小心複製 pipeline 時遺漏了資料

Often we create a pipeline by copying an existing pipeline \(i.e. [cargo cult programming](https://en.wikipedia.org/wiki/Cargo_cult_programming)\), and the old pipeline drops data that we need for the new pipeline. For example, the pipeline for Google  
Plus What’s Hot drops older posts \(because it is trying to rank fresh posts\). This pipeline was copied to use for Google Plus Stream, where older posts are still meaningful, but the pipeline  
was still dropping old posts. Another common pattern is to only log data that was seen by the user. Thus, this data is useless if we want to model why a particular post was not seen by the  
user, because all the negative examples have been dropped. A similar issue occurred in Play. While working on Play Apps Home, a new pipeline was created that also contained examples  
from two other landing pages \(Play Games Home and Play Home Home\) without any feature to disambiguate where each example came from.

#### Rule 7 - 將啟發式/捷思法轉換成特徵，或在外部處理它們

通常 ML 試圖解決的問題都不是全新的。排名 (ranking)、分類 (classifying) 或任何其他問題，都有現成的系統在解決。這代表著有一大堆的規則或啟發式方法可用。這些類似的啟發式作法可以在調整 ML 演算法的時候順道載你一程 (give you a lift)。

這些啟發式作法應該要被挖掘 (mined) 出任何它們所擁有的資訊，這出於兩個原因：第一，這將會讓你在轉換到 ML 系統的過程中比較平滑、順利；第二，通常這些規則包含了關於這個系統所需的大量直覺 (intuition)，而你不會想把它們丟掉。有四種可以利用現成啟發式的方法：

1. Preprocess using the heuristic. If the feature is incredibly awesome, then this is an option. For example, if, in a spam filter, the sender has already been blacklisted, don’t try
   to relearn what “blacklisted” means. Block the message. This approach makes the most sense in binary classification tasks.
2. Create a feature. Directly creating a feature from the heuristic is great. For example, if you use a heuristic to compute a relevance score for a query result, you can include the score as the value of a feature. Later on you may want to use machine learning techniques to massage the value \(for example, converting the value into one of a finite
   set of discrete values, or combining it with other features\) but start by using the raw value produced by the heuristic.
3. Mine the raw inputs of the heuristic. If there is a heuristic for apps that combines the number of installs, the number of characters in the text, and the day of the week, then
   consider pulling these pieces apart, and feeding these inputs into the learning separately. Some techniques that apply to ensembles apply here \(see **Rule \#40**\).
4. Modify the label. This is an option when you feel that the heuristic captures information not currently contained in the label. For example, if you are trying to maximize the number of downloads, but you also want quality content, then maybe the solution is to multiply the label by the average number of stars the app received. There is a lot of space here for leeway. See the section on [“Your First Objective”](#your-first-objective). Do be mindful of the added complexity when using heuristics in an ML system. Using old heuristics in your new machine learning algorithm can help to create a smooth transition, but think about whether there is a simpler way to accomplish the same effect.



