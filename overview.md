# 概述

為了做出好產品：

> do machine learning like the great engineer you are, not like the great machine learning expert you aren’t.

事實上，**你將會面對的絕大多數問題都是工程問題（engineering problems）。**

即使是一個傑出的 ML 專家擁有他該有的資源，**絕大多數的產出（gains）來自好的特徵（features），而不是厲害的 ML 演算法。**

所以，基本作法是：

1. 確保你的 pipeline 從頭到尾（end to end）都是穩固的。

2. 從一個合理的目標開始。

   > 按：不要一開始就嘗試優化連人都很難做到的多目標（雖然多目標才是現實的問題）
   >
   > 或是還沒有嘗試 baseline，就將一個完全不可能的精準度當做目標

3. 用簡單的方式增加符合常識的特徵（features）。

   > 按：如果用「工人智慧」來做，那麼人腦會怎麼做？

4. 確保你的 pipeline 能夠維持穩固。

   > 按：跟第一點的差別是，第一點要求「從頭到尾」建構一個完整的 pipeline
   >
   > 而這一點要求這個 pipeline 可以「維持」下去
   >
   > 像是模型要隨著時間更新嗎？多久更新？
   >
   > 會不會因為外部系統些微的改變就失控？

這個作法的確會賺錢，而且足夠讓很多人滿意好一陣子。

只有當沒有任何簡單的技巧（tricks）可以讓系統得到改進的時候，才考慮從這樣的作法發散出去。

然而也要注意，增加複雜度的同時也會拖慢未來產品的釋出速度

一旦你已經窮盡任何簡單的技巧，那麼最前緣的（cutting-edge）機器學習演算法就可能是你未來要進入的領域了。

這份文件分為四個部分：

1. 第一部分會幫助你瞭解：建構一個 ML 系統的時機是否成熟。

2. 第二部分是關於佈署（deploying）你的第一版pipeline.

3. 第三部分是關於啟動（launching）產品與疊代（iterating）產品，當增加新 feature 到你的 pipeline 時，如何去評估模型和訓練-應用偏差（training-serving skew）。

4. 第四部分是關於當你遇到瓶頸的時候要做什麼。

5. 最後是 related work 的清單，以及附錄（appendix）告訴你一些用來在這份文件中作為範例的系統背景知識。



