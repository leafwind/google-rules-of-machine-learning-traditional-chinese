# Before Machine Learning

## Rule 1 - 不要害怕去推出一個沒有機器學習的產品

機器學習很酷，但它需要資料。理論上，你可以從不同的問題中取得資料，然後為一個新的產品調整 model，但這可能會表現地比基本的直覺（heuristics）還要差。如果你認為機器學習會給你 100% 的提昇，那直覺作法將會讓你達到 50%。

（編按：50% 是比較保守的估計，我的經驗是加上一些 rule based 控制可以達到 80% 以上）

舉例來說，如果你正在替 App 市集作 App 排名，你可以直接使用下載比例或下載量。
如果你在偵測垃圾郵件，那就過濾那些曾經發送過垃圾郵件的發行商（publishers）。
也不要擔心去使用人工編輯。
如果你需要替聯絡人排名，那就將最近使用的排最前面（或甚至按照字母排序）。
如果機器學習對你的產品不是絕對必要，直到你有資料之前都不要使用它。

<sup>[Google Research Blog - The 280-Year-Old Algorithm Inside Google Trips](https://research.googleblog.com/2016/09/the-280-year-old-algorithm-inside.html?m=1)</sup>

## Rule 2 - 首先，設計跟實作指標（Metrics）

在正式決定你的機器學習系統要做什麼事前，要盡可能地追蹤目前的系統。這麼做是為了以下原因：

1. 在早期，要獲得系統使用者的許可（permission）相對容易

2. 如果你認為某件事情在未來會是一個顧慮，最好現在就開始收集歷史資料

3. If you design your system with metric instrumentation in mind, things will go better for
you in the future. Specifically, you don’t want to find yourself grepping for strings in logs
to instrument your metrics!

4. 你會發現有些東西會改變，有些則不變。舉例來說，你想要直接優化一天的活躍使用者，然而，經過你對系統的的早期操作，你可能會發現，即使使用者體驗有劇烈的變化，這個指標也不會有明顯的改變。

Google Plus team measures expands per read, reshares per read, plus­-ones per read, comments/read, comments per user, reshares per user, etc. which they use in computing the goodness of a post at serving time. Also, note that an experiment framework, where you can group users into buckets and aggregate statistics by experiment, is important. See Rule **#12**.

By being more liberal about gathering metrics, you can gain a broader picture of your system. Notice a problem? Add a metric to track it! Excited about some quantitative change on the last
release? Add a metric to track it!

## Rule 3 - 選擇機器學習而非複雜的直覺（complex heuristic）

簡單的 heuristic 可以讓產品走出市場；然而複雜的 heuristic 難以維護。
一旦你有了資料，以及對你要嘗試達到的目標有了基本的想法，就轉移到機器學習。
因為在大多數軟體工程的任務中，你將會需要頻繁地更新你的作法（approach），不管他是一個 heuristic 或是一個機器學習模型，而你會發現，後者的更新與維護比較簡單。（參考 Rule 16）
machine-­learned model is easier to update and maintain (see Rule **#16**).
