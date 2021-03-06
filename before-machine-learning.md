# 在機器學習之前

## Rule 1 - 不要害怕去推出一個沒有機器學習（ML）的產品

機器學習很酷，但**它需要資料**。理論上，你可以從不同的問題中取得資料，然後為一個新的產品調整 model，但這可能會表現地比基本的直覺（heuristics）還要差。

如果你認為機器學習會給你 100% 的提昇，那直覺作法將會讓你達到 50%。

> 按：50% 是比較保守的估計，個人經驗加上一些 rule based 控制，達到相對於 ML 80% 以上的效果都不過分

> 解釋：取決於基礎建設（infrastructure）有多穩定，越穩定的基礎建設，可以讓越複雜的 ML model，發揮越好的效益

> 以作者在 Google 的情況，可以預期他們的 ML model 是建構在「足夠穩定」的軟體工程基礎上

> 反過來說，若是在很糟的環境，ML 甚至不一定會優於 heuristic

舉例來說，如果你正在替 App 市集作 App 排名，你可以直接使用下載比例或下載量。

如果你在偵測垃圾郵件，那就過濾那些曾經發送過垃圾郵件的發行商（publishers）。

也**不要擔心去使用人工編輯**。（按：工人智慧是很厲害的）

如果你需要替聯絡人排名，那就將最近使用的排最前面（或甚至按照字母排序）。

如果機器學習對你的產品不是絕對必要，直到你有資料之前都不要使用它。

> 按：有權力決定是否使用 ML 的人，必須非常清楚目前擁有的資料，在 heuristic 與 ML 分別能達到的效益

> 如果對資料不夠熟悉，那就先拿來玩一玩、嘗一嘗，看能怎麼運用它

> 沒有資料就開始使用 ML，就像你完全不知道有什麼食材，卻想做出一頓大餐一樣不切實際

<sup>[Google Research Blog - The 280-Year-Old Algorithm Inside Google Trips](https://research.googleblog.com/2016/09/the-280-year-old-algorithm-inside.html?m=1)</sup>

## Rule 2 - 首先，設計跟實作指標（Metrics）

在正式決定你的機器學習系統要做什麼事之前，要盡可能地追蹤目前的系統。這麼做是為了以下原因：

1. 在早期，要獲得使用者的許可（permission）相對容易

  > 按：因為使用者還沒有建立出「對系統的期待」，因此系統的改變是相對可以接受的

  > 一旦時間久了，系統穩定了，使用者「習慣」了這個系統

  > 便會預期一些目前的系統行為是「系統需求」的一部分（即使這些使用者看到的效果，並不是一開始設計的目的）

2. 如果你認為某件事情在未來會是一個顧慮，最好現在就開始收集歷史資料

  > 按：假設目前產品只有要求 metric A，但你預期接下來會被要求 B

  > 那最好預先收集那些「為了優化 metric B 所需要的資料」

  > 或是另一種情況：你認為這個系統的 feature C 受限於外部因素並不穩定

  > 你不會想要等到 model 爆炸了才發現原來是 C 的 input 根本就壞了
3. 如果你用指標工具化（metric instrumentation）的思維設計你的系統，事情會在未來比較順利。你不會想要為了查出某個指標的成效，而讓自己身陷在 grep log 字串的工作中！

  > 按：可以參考 [prometheus](https://prometheus.io/docs/practices/instrumentation/) 的 practice

4. 你會發現有些東西會改變，有些則不變。舉例來說，你想要直接優化一天的活躍使用者，然而，經過你對系統的的早期操作，你可能會發現，即使使用者體驗有劇烈的變化，這個指標也不會有明顯的改變。

  > 按：這段話有點繞口，簡單地說，你對於各種「操作 A 會不會影響 B」的掌握度變高，可以類比成所謂的 domain knowledge

Google Plus team 衡量了每個閱讀的展開（expands）、分享、+1、評論；以及每個使用者的評論、分享等等。
這些是他們在服務時用來計算一個貼文的質量的指標。並且要注意，一個你可以將使用者分群、整合各個實驗統計資料的實驗框架（experiment framework）很重要。（參考 Rule 12）

透過更自由地收集指標，你能夠用更寬廣的視野來觀察你的系統。發現一個問題？增加一個指標追蹤它！對上一次釋出所造成的量化改變感到興奮？增加一個指標來追蹤它！

## Rule 3 - 選擇機器學習而非複雜的直覺（complex heuristic）

簡單的 heuristic 可以讓產品走出市場；然而複雜的 heuristic 卻難以維護。

一旦你有了資料，以及對你要嘗試達到的目標有了基本的想法，就轉移到機器學習。

因為不管他是一個 heuristic 或是一個機器學習模型，在大多數軟體工程的任務中，你會需要頻繁地更新作法（approach）

而你會發現，後者的更新與維護比較簡單。（參考 Rule 16）

> 按：要注意，不管是從 heuristic 轉移到 ML，或是從一個 ML model 轉移到另一個 ML 都需要成本。

> 在現實中，當使用者已經建立出「對系統的期待」，要轉移就並非那麼單純

> 舉例來說：原本優化 metric A 的模型，其實對 B 也做得不錯（即使並沒有寫在需求裡面）

> 當你轉移到另一個模型，即使它對 A 優化地更好，卻在 B 作得不好，那使用者可能不會接受

> 追根究柢，因為人生就是這麼困難，每個人的工作要優化的目標通常是多個、並且多少有所差異

> 而機器學習（或這個功能）通常只優化其中一兩個目標而已
