
### Monitoring

> In general, practice good alerting hygiene, such as making alerts actionable and having a
dashboard page.

#### Rule 8 - Know the freshness requirements of your system

How much does performance degrade if you have a model that is a day old? A week old? A quarter old? This information can help you to understand the priorities of your monitoring. If you
lose 10% of your revenue if the model is not updated for a day, it makes sense to have an engineer watching it continuously. Most ad serving systems have new advertisements to handle
every day, and must update daily. For instance, if the ML model for Google Play Search is not updated, it can have an impact on revenue in under a month. Some models for What’s Hot in
Google Plus have no post identifier in their model so they can export these models infrequently. Other models that have post identifiers are updated much more frequently. Also notice that
freshness can change over time, especially when feature columns are added or removed from your model.

#### Rule 9 - Detect problems before exporting models.

Many machine learning systems have a stage where you export the model to serving. If there is an issue with an exported model, it is a user­facing issue. If there is an issue before, then it is a training issue, and users will not notice.
Do sanity checks right before you export the model. Specifically, make sure that the model’s performance is reasonable on held out data. Or, if you have lingering concerns with the data, don’t export a model. Many teams continuously deploying models check the area under the [ROC curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) (or [AUC](http://stats.stackexchange.com/questions/132777/what-does-auc-stand-for-and-what-is-it)) before exporting. Issues about models that haven’t been exported
require an e­mail alert, but issues on a user­facing model may require a page. So better to wait and be sure before impacting users.

#### Rule 10 - Watch for silent failures.

This is a problem that occurs more for machine learning systems than for other kinds of systems. Suppose that a particular table that is being joined is no longer being updated. The
machine learning system will adjust, and behavior will continue to be reasonably good, decaying gradually. Sometimes tables are found that were months out of date, and a simple refresh
improved performance more than any other launch that quarter! For example, the coverage of a feature may change due to implementation changes: for example a feature column could be
populated in 90% of the examples, and suddenly drop to 60% of the examples. Play once had a table that was stale for 6 months, and refreshing the table alone gave a boost of 2% in install rate. If you track statistics of the data, as well as manually inspect the data on occasion, you can reduce these kinds of failures.*

* <sup> [*A Framework for Analysis of Data Freshness* - Bouzeghoub & Peralta](https://www.fing.edu.uy/inco/grupos/csi/esp/Publicaciones/2004/iqis2004-mb.pdf)</sup>

#### Rule 11 - Give feature columns owners and documentation.

If the system is large, and there are many feature columns, know who created or is maintaining each feature column. If you find that the person who understands a feature column is leaving, make sure that someone has the information. Although many feature columns have descriptive names, it's good to have a more detailed description of what the feature is, where it came from, and how it is expected to help.

