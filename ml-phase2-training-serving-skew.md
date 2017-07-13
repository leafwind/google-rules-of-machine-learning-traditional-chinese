
### Training-Serving Skew

> Training­-serving skew is a difference between performance during training and performance
during serving. This skew can be caused by:
* a discrepancy between how you handle data in the training and serving pipelines, or
* a change in the data between when you train and when you serve, or
* a feedback loop between your model and your algorithm.

> We have observed production machine learning systems at Google with training-­serving skew
that negatively impacts performance. The best solution is to explicitly monitor it so that system
and data changes don’t introduce skew unnoticed.

#### Rule 29 - The best way to make sure that you train like you serve is to save the set of features used at serving time, and then pipe those features to a log to use them at training time.

Even if you can’t do this for every example, do it for a small fraction, such that you can verify the consistency between serving and training (see Rule **#37**). Teams that have made this measurement at Google were sometimes surprised by the results. YouTube home page switched to logging features at serving time with significant quality improvements and a reduction in code complexity, and many teams are switching their infrastructure as we speak.

#### Rule 30 - Importance weight sampled data, don't arbitrarily drop it!

When you have too much data, there is a temptation to take files 1­12, and ignore files 13­99. This is a mistake: dropping data in training has caused issues in the past for several teams (see Rule **6**). Although data that was never shown to the user can be dropped, importance weighting is best for the rest. Importance weighting means that if you decide that you are going
to sample example X with a 30% probability, then give it a weight of 10/3. With importance weighting, all of the calibration properties discussed in Rule **#14** still hold.

#### Rule 31 - Beware that if you join data from a table at training and serving time, the data in the table may change.

Say you join doc ids with a table containing features for those docs (such as number of comments or clicks). Between training and serving time, features in the table may be changed.
Your model's prediction for the same document may then differ between training and serving. The easiest way to avoid this sort of problem is to log features at serving time (see Rule **#32**). If the table is changing only slowly, you can also snapshot the table hourly or daily to get reasonably close data. Note that this still doesn’t completely resolve the issue.

#### Rule 32 - Re-use code between your training pipeline and your serving pipeline whenever possible.

Batch processing is different than online processing. In online processing, you must handle each request as it arrives (e.g. you must do a separate lookup for each query), whereas in batch
processing, you can combine tasks (e.g. making a join). At serving time, you are doing online processing, whereas training is a batch processing task. However, there are some things that
you can do to re­use code. For example, you can create an object that is particular to your system where the result of any queries or joins can be stored in a very human readable way,
and errors can be tested easily. Then, once you have gathered all the information, during serving or training, you run a common method to bridge between the human-­readable object
that is specific to your system, and whatever format the machine learning system expects. **This eliminates a source of training-­serving skew.** As a corollary, try not to use two different programming languages between training and serving ­ that decision will make it nearly impossible for you to share code.

#### Rule 33 - If you produce a model based on the data until January 5th, test the model on the data from January 6th and after.

In general, measure performance of a model on the data gathered after the data you trained the model on, as this better reflects what your system will do in production. If you produce a model based on the data until January 5th, test the model on the data from January 6th. You will expect that the performance will not be as good on the new data, but it shouldn’t be radically worse. Since there might be daily effects, you might not predict the average click rate or conversion rate, but the area under the curve, which represents the likelihood of giving the positive example a score higher than a negative example, should be reasonably close.

#### Rule 34 - In binary classification for filtering (such as spam detection or determining interesting e­mails), make small short­term sacrifices in performance for very clean data.

In a filtering task, examples which are marked as negative are not shown to the user. Suppose you have a filter that blocks 75% of the negative examples at serving. You might be tempted to
draw additional training data from the instances shown to users. For example, if a user marks an
email as spam that your filter let through, you might want to learn from that. But this approach introduces sampling bias. You can gather cleaner data if instead during
serving you label 1% of all traffic as “held out”, and send all held out examples to the user. Now your filter is blocking at least 74% of the negative examples. These held out examples can
become your training data. Note that if your filter is blocking 95% of the negative examples or more, this becomes less
viable. Even so, if you wish to measure serving performance, you can make an even tinier sample (say 0.1% or 0.001%). Ten thousand examples is enough to estimate performance quite
accurately.

#### Rule 35 - Beware of the inherent skew in ranking problems.

When you switch your ranking algorithm radically enough that different results show up, you have effectively changed the data that your algorithm is going to see in the future. This kind of skew will show up, and you should design your model around it. There are multiple different approaches. These approaches are all ways to favor data that your model has already seen.

1. Have higher regularization on features that cover more queries as opposed to those features that are on for only one query. This way, the model will favor features that are
specific to one or a few queries over features that generalize to all queries. This approach can help prevent very popular results from leaking into irrelevant queries. Note
that this is opposite the more conventional advice of having more regularization on feature columns with more unique values.
2. Only allow features to have positive weights. Thus, any good feature will be better than a feature that is “unknown”.
3. Don’t have document­only features. This is an extreme version of #1. For example, even if a given app is a popular download regardless of what the query was, you don’t want to
show it everywhere<sup>4</sup>. Not having document­only features keeps that simple.

<sup>4 - The reason you don’t want to show a specific popular app everywhere has to do with the importance of
making all the desired apps reachable. For instance, if someone searches for “bird watching app”, they
might download “angry birds”, but that certainly wasn’t their intent. Showing such an app might improve
download rate, but leave the user’s needs ultimately unsatisfied.</sup>

#### Rule 36 - Avoid feedback loops with positional features.

The position of content dramatically affects how likely the user is to interact with it. If you put an app in the first position it will be clicked more often, and you will be convinced it is more likely to be clicked. One way to deal with this is to add positional features, i.e. features about the position of the content in the page. You train your model with positional features, and it learns to weight, for example, the feature "1st­position" heavily. Your model thus gives less weight to other factors for examples with "1st­position=true". Then at serving you don't give any instances the positional feature, or you give them all the same default feature, because you are scoring candidates before you have decided the order in which to display them. Note that it is important to keep any positional features somewhat separate from the rest of the
model because of this asymmetry between training and testing. Having the model be the sum of a function of the positional features and a function of the rest of the features is ideal. For example, don’t cross the positional features with any document feature.

#### Rule 37 - Measure training/serving skew.

There are several things that can cause skew in the most general sense. Moreover, you can divide it into several parts:

1. The difference between the performance on the training data and the holdout data. In general, this will always exist, and it is not always bad.
2. The difference between the performance on the holdout data and the “next­day” data. Again, this will always exist. **You should tune your regularization to maximize the next­day performance.** However, large drops in performance between holdout and next­day data may indicate that some features are time-­sensitive and possibly degrading
model performance.
3. The difference between the performance on the “next­day” data and the live data. If you apply a model to an example in the training data and the same example at serving, it
should give you exactly the same result (see Rule **#5**). Thus, a discrepancy here probably indicates an engineering error.
