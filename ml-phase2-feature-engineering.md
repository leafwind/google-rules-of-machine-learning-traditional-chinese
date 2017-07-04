## Feature engineering

> In the first phase of the lifecycle of a machine learning system, the important issue is to get the training data into the learning system, get any metrics of interest instrumented, and create a serving infrastructure. **After you have a working end to end system with unit and system tests instrumented, Phase II begins.**

#### Rule 16 - Plan to launch and iterate.

Don’t expect that the model you are working on now will be the last one that you will launch, or even that you will ever stop launching models. Thus consider whether the complexity you are
adding with this launch will slow down future launches. Many teams have launched a model per quarter or more for years. There are three basic reasons to launch new models:

1. you are coming up with new features,
2. you are tuning regularization and combining old features in new ways, and/or
3. you are tuning the objective.

Regardless, giving a model a bit of love can be good: looking over the data feeding into the example can help find new signals as well as old, broken ones. So, as you build your model, think about how easy it is to add or remove or recombine features. Think about how easy it is to create a fresh copy of the pipeline and verify its correctness. Think about whether it is possible to have two or three copies running in parallel. Finally, don’t worry about whether feature 16 of 35 makes it into this version of the pipeline. You’ll get it next quarter.

#### Rule 17 - Start with directly observed and reported features as opposed to learned features.

This might be a controversial point, but it avoids a lot of pitfalls. First of all, let’s describe what a
learned feature is. A learned feature is a feature generated either by an external system (such as an unsupervised clustering system) or by the learner itself (e.g. via a factored model or deep learning). Both of these can be useful, but they can have a lot of issues, so they should not be in
the first model. If you use an external system to create a feature, remember that the system has its own
objective. The external system's objective may be only weakly correlated with your current objective. If you grab a snapshot of the external system, then it can become out of date. If you
update the features from the external system, then the meanings may change. If you use an external system to provide a feature, be aware that they require a great deal of care.
The primary issue with factored models and deep models is that they are non­-convex. Thus, there is no guarantee that an optimal solution can be approximated or found, and the local
minima found on each iteration can be different. This variation makes it hard to judge whether the impact of a change to your system is meaningful or random. By creating a model without
deep features, you can get an excellent baseline performance. After this baseline is achieved, you can try more esoteric approaches.

#### Rule 18 - Explore with features of content that generalize across contexts.

Often a machine learning system is a small part of a much bigger picture. For example, if you imagine a post that might be used in What’s Hot, many people will plus­-one, re-­share, or
comment on a post before it is ever shown in What’s Hot. If you provide those statistics to the learner, it can promote new posts that it has no data for in the context it is optimizing. YouTube Watch Next could use number of watches, or co­-watches (counts of how many times one video was watched after another was watched) from YouTube search. You can also use explicit user
ratings. Finally, if you have a user action that you are using as a label, seeing that action on the document in a different context can be a great feature. All of these features allow you to bring new content into the context. Note that this is not about personalization: figure out if someone likes the content in this context first, then figure out who likes it more or less.

#### Rule 19 - Use very specific features when you can.

With tons of data, it is simpler to learn millions of simple features than a few complex features. Identifiers of documents being retrieved and canonicalized queries do not provide much
generalization, but align your ranking with your labels on head queries.. Thus, don’t be afraid of groups of features where each feature applies to a very small fraction of your data, but overall coverage is above 90%. You can use regularization to eliminate the features that apply to too
few examples.

#### Rule 20 - Combine and modify existing features to create new features in human-understandable ways.

There are a variety of ways to combine and modify features. Machine learning systems such as TensorFlow allow you to pre­process your data through [transformations](https://www.tensorflow.org/tutorials/linear/overview#feature-columns-and-transformations). The two most standard approaches are “discretizations” and “crosses”.

Discretization consists of taking a continuous feature and creating many discrete features from it. Consider a continuous feature such as age. You can create a feature which is 1 when age is less than 18, another feature which is 1 when age is between 18 and 35, et cetera. Don’t overthink the boundaries of these histograms: basic quantiles will give you most of the impact. Crosses combine two or more feature columns. A feature column, in TensorFlow's terminology, is a set of homogenous features, (e.g. {male, female}, {US, Canada, Mexico}, et cetera). A cross is a new feature column with features in, for example, *{male, female} × {US,Canada, Mexico}*. This new feature column will contain the feature (male, Canada). If you are using TensorFlow and you tell TensorFlow to create this cross for you, this (male, Canada) feature will be present
in examples representing male Canadians. Note that it takes massive amounts of data to learn models with crosses of three, four, or more base feature columns.

Crosses that produce very large feature columns may overfit. For instance, imagine that you are doing some sort of search, and you have a feature column with words in the query, and you
have a feature column with words in the document. You can combine these with a cross, but you will end up with a lot of features (see Rule **#21**). When working with text there are two
alternatives. The most draconian is a dot product. A dot product in its simplest form simply counts the number of common words between the query and the document. This feature can
then be discretized. Another approach is an intersection: thus, we will have a feature which is present if and only if the word “pony” is in the document and the query, and another feature
which is present if and only if the word “the” is in the document and the query.

#### Rule 21 - The number of feature weights you can learn in a linear model is roughly proportional to the amount of data you have.

There are fascinating statistical learning theory results concerning the appropriate level of complexity for a model, but this rule is basically all you need to know. I have had conversations in which people were doubtful that anything can be learned from one thousand examples, or that you would ever need more than 1 million examples, because they get stuck in a certain method of learning. The key is to scale your learning to the size of your data:

1. If you are working on a search ranking system, and there are millions of different words in the documents and the query and you have 1000 labeled examples, then you should use a dot product between document and query features, [TF­IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf), and a half-­dozen other highly human-­engineered features. 1000 examples, a dozen features.
2. If you have a million examples, then intersect the document and query feature columns, using regularization and possibly feature selection. This will give you millions of features,
but with regularization you will have fewer. Ten million examples, maybe a hundred thousand features.
3. If you have billions or hundreds of billions of examples, you can cross the feature columns with document and query tokens, using feature selection and regularization. You will have a billion examples, and 10 million features.

Statistical learning theory rarely gives tight bounds, but gives great guidance for a starting point.
In the end, use Rule **#28** to decide what features to use.

#### Rule 22 - Clean up features you are no longer using.

Unused features create technical debt. If you find that you are not using a feature, and that combining it with other features is not working, then drop it out of your infrastructure. You want to keep your infrastructure clean so that the most promising features can be tried as fast as
possible. If necessary, someone can always add back your feature. Keep coverage in mind when considering what features to add or keep. How many examples are covered by the feature? For example, if you have some personalization features, but only
8% of your users have any personalization features, it is not going to be very effective. At the same time, some features may punch above their weight. For example, if you have a
feature which covers only 1% of the data, but 90% of the examples that have the feature are positive, then it will be a great feature to add.

### Human Analysis of the System

> Before going on to the third phase of machine learning, it is important to focus on something that is not taught in any machine learning class: how to look at an existing model, and improve it. This is more of an art than a science, and yet there are several anti-­patterns that it helps to avoid.

#### Rule 23 - You are not a typical end user.*

This is perhaps the easiest way for a team to get bogged down. While there are a lot of benefits to fish-fooding (using a prototype within your team) and dog-fooding (using a prototype within your company), employees should look at whether the performance is correct. While a change which is obviously bad should not be used, anything that looks reasonably near production should be tested further, either by paying laypeople to answer questions on a crowdsourcing platform, or through a live experiment on real users. There are two reasons for this. The first is that you are too close to the code. You may be looking for a particular aspect of the posts, or you are simply too emotionally involved (e.g. confirmation bias). The second is that your time is too valuable. Consider the cost of 9 engineers sitting in a one hour meeting, and think of how many contracted human labels that buys on a crowdsourcing platform.

If you really want to have user feedback, **use user experience methodologies**. Create user personas (one description is in Bill Buxton’s [~~Designing~~ *Sketching User Experiences*](https://www.amazon.com/Sketching-User-Experiences-Interactive-Technologies/dp/0123740371)) early in a process and
do usability testing (one description is in Steve Krug’s [*Don’t Make Me Think*](https://www.amazon.com/Dont-Make-Me-Think-Usability/dp/0321344758)) later. User personas involve creating a hypothetical user. For instance, if your team is all male, it might help to design a 35­-year old female user persona (complete with user features), and look at the results it generates rather than 10 results for 25­-40 year old males. Bringing in actual people to watch their reaction to your site (locally or remotely) in usability testing can also get you a fresh perspective.

<sup>[Google Research Blog - How to measure translation quality in your user interfaces](https://research.googleblog.com/2015/10/how-to-measure-translation-quality-in.html?m=1)

#### Rule 24 - Measure the delta between modules

One of the easiest, and sometimes most useful measurements you can make before any users have looked at your new model is to calculate just how different the new results are from production. For instance, if you have a ranking problem, run both models on a sample of queries through the entire system, and look at the size of the symmetric difference of the results
(weighted by ranking position). If the difference is very small, then you can tell without running an experiment that there will be little change. If the difference is very large, then you want to make sure that the change is good. Looking over queries where the symmetric difference is high
can help you to understand qualitatively what the change was like. Make sure, however, that the system is stable. Make sure that a model when compared with itself has a low (ideally zero)
symmetric difference.

#### Rule 25 - When choosing models, utilitarian performance trumps predictive power.

Your model may try to predict click­-through-­rate. However, in the end, the key question is what you do with that prediction. If you are using it to rank documents, then the quality of the final ranking matters more than the prediction itself. If you predict the probability that a document is spam and then have a cutoff on what is blocked, then the precision of what is allowed through matters more. Most of the time, these two things should be in agreement: when they do not
agree, it will likely be on a small gain. Thus, if there is some change that improves log loss but degrades the performance of the system, look for another feature. When this starts happening more often, it is time to revisit the objective of your model.

#### Rule 26 - Look for patterns in the measured errors, and create new features.

Suppose that you see a training example that the model got “wrong”. In a classification task, this could be a false positive or a false negative. In a ranking task, it could be a pair where a positive was ranked lower than a negative. The most important point is that this is an example that the
machine learning system knows it got wrong and would like to fix if given the opportunity. If you give the model a feature that allows it to fix the error, the model will try to use it.
On the other hand, if you try to create a feature based upon examples the system doesn’t see as mistakes, the feature will be ignored. For instance, suppose that in Play Apps Search,
someone searches for “free games”. Suppose one of the top results is a less relevant gag app. So you create a feature for “gag apps”. However, if you are maximizing number of installs, and people install a gag app when they search for free games, the “gag apps” feature won’t have the effect you want.

Once you have examples that the model got wrong, look for trends that are outside your current feature set. For instance, if the system seems to be demoting longer posts, then add post
length. Don’t be too specific about the features you add. If you are going to add post length, don’t try to guess what long means, just add a dozen features and the let model figure out what to do with them (see Rule **#21**). That is the easiest way to get what you want.

#### Rule 27 - Try to quantify observed undesirable behavior.

Some members of your team will start to be frustrated with properties of the system they don’t like which aren’t captured by the existing loss function. At this point, they should do whatever it takes to turn their gripes into solid numbers. For example, if they think that too many “gag apps” are being shown in Play Search, they could have human raters identify gag apps. (You can feasibly use human-­labelled data in this case because a relatively small fraction of the queries account for a large fraction of the traffic.) If your issues are measurable, then you can start using them as features, objectives, or metrics. The general rule is **“measure first, optimize second”**.

#### Rule 28 - Be aware that identical short-term behavior does not imply identical long-term behavior.

Imagine that you have a new system that looks at every doc_id and exact_query, and then calculates the probability of click for every doc for every query. You find that its behavior is
nearly identical to your current system in both side by sides and A/B testing, so given its simplicity, you launch it. However, you notice that no new apps are being shown. Why? Well,
since your system only shows a doc based on its own history with that query, there is no way to learn that a new doc should be shown.

The only way to understand how such a system would work long­term is to have it train only on data acquired when the model was live. This is very difficult.

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
