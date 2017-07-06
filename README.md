# 繁中版「機器學習法則」

* 原文：["Rules of Machine Learning"](http://martin.zinkevich.org/rules_of_ml/rules_of_ml.pdf)

* 編/譯：[leafwind](https://medium.com/@leafwind)

* Git Book [URL](https://leafwind.gitbooks.io/rules-of-machine-learning-traditional-chinese/content/)

## 簡介與免責聲明

這系列文章原文為 [Martin Zinkevich](http://martin.zinkevich.org/) \(Research Scientist @ Google\) 所著之 ["Rules of Machine Learning"](http://martin.zinkevich.org/rules_of_ml/rules_of_ml.pdf)

看了一部分之後覺得深得我心（尤其「你會面對的絕大多數問題是工程問題」這一點）

於是想要深入細讀、做些筆記，將它推廣到繁體中文界

作為一個參與過部分機器學習開發的工程師

試著儘量在維持原意的情況下翻譯、排版

並將一些原文較為精簡的敘述，補上（以個人經驗推斷的）詳細說明

個人所知所學有限，若有好心人發現錯誤或艱澀難懂之處，還請多不吝指教

另外也感謝有國外的好心人整理成純文字版的 [Google's 43 Rules of Machine Learning](https://github.com/thundergolfer/google-rules-of-machine-learning) 讓翻譯比較容易

## 簡中版

過程中發現有雷鋒網翻譯的簡中版 [谷歌机器学习白皮书全解析 43条黄金法则（一）](https://www.leiphone.com/news/201701/FmC6Z2X6UeCvgGEV.html)

但不盡人意，原因除了用詞文化差異之外，過度地翻譯成中文也失去了部分原文的意思

以至於出現一些「每個詞都懂，但是拼湊起來的句子就看不懂」的情況。

比如：

* 「关乎启动和重复」原文是 "launching and iterating \(a product\)"

* 「设置你的第一个流水线」原文是 "deploy your first pipeline"

* 「能通过试验把用户分组并整合数据的试验框架非常重要」原文為 "can group users into buckets and aggregate statistics by experiment, is important."

* 訓練-應用偏差（training-serving skew）甚至沒有翻譯到。

不過仍然很感謝有這樣的中文資源（相比之下台灣卻很少...）

