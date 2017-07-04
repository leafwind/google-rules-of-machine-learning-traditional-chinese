# Terminology

The following terms will come up repeatedly in our discussion of effective machine learning:

* Instance: 你想要預測的事物。舉例來說，Instance 可能是一個網頁，你想要把它「分類」為「與貓有關」或「與貓無關」。

* Label: 預測的答案 -- 可以是機器學習系統產生的答案，也可以是 training data 提供的正確答案。舉例來說，一個網頁的 label 可能是「與貓有關」。

* Feature: instance 在預測任務中的其中一個屬性。舉例來說，一個網頁可能會有一個「包含『貓』這個字」的 feature。

Feature Column : A set of related features, such as the set of all possible countries in which 1 users might live. An example may have one or more features present in a feature column. A feature column is referred to as a “namespace” in the VW system \(at Yahoo/Microsoft\), or a field.

Example: An instance \(with its features\) and a label.

Model: A statistical representation of a prediction task. You train a model on examples then use the model to make predictions.

Metric: A number that you care about. May or may not be directly optimized.

Objective: A metric that your algorithm is trying to optimize.

Pipeline: The infrastructure surrounding a machine learning algorithm. Includes gathering the data from the front end, putting it into training data files, training one or more models, and exporting the models to production.

