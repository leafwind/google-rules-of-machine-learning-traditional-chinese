## Your First Pipeline

> Focus on your system infrastructure for your first pipeline. While it is fun to think about all the
imaginative machine learning you are going to do, it will be hard to figure out what is happening
if you don’t first trust your pipeline.

#### Rule 4 - Keep the first model simple and get the infrastructure right.

The first model provides the biggest boost to your product, so it doesn't need to be fancy. But you will run into many more infrastructure issues than you expect. Before anyone can use your
fancy new machine learning system, you have to determine:

1. How to get examples to your learning algorithm.
2. A first cut as to what “good” and “bad” mean to your system.
3. How to integrate your model into your application. You can either apply the model live, or pre­compute the model on examples offline and store the results in a table. For example,
you might want to pre­classify web pages and store the results in a table, but you might want to classify chat messages live.

Choosing simple features makes it easier to ensure that:

1. The features reach your learning algorithm correctly.
2. The model learns reasonable weights.
3. The features reach your model in the server correctly.

Once you have a system that does these three things reliably, you have done most of the work. Your simple model provides you with baseline metrics and a baseline behavior that you can use
to test more complex models. Some teams aim for a “neutral” first launch: a first launch that explicitly de-­prioritizes machine learning gains, to avoid getting distracted.

#### Rule 5 - Test the infrastructure independently from the machine learning.

Make sure that the infrastructure is testable, and that the learning parts of the system are
encapsulated so that you can test everything around it. Specifically:

1. Test getting data into the algorithm. Check that feature columns that should be populated
are populated. Where privacy permits, manually inspect the input to your training algorithm. If possible, check statistics in your pipeline in comparison to elsewhere, such
as RASTA.

2. Test getting models out of the training algorithm. Make sure
that the model in your
training environment gives the same score as the model in your serving environment (see Rule **#37**). Machine learning has an element of unpredictability, so make sure that you have tests for the code for creating examples in training and serving, and that you can load and use a fixed model during serving. Also, it is important to understand your data: see [Practical Advice for Analysis of Large, Complex Data Sets.](http://www.unofficialgoogledatascience.com/2016/10/practical-advice-for-analysis-of-large.html)

#### Rule 6 - Be careful about dropped data when copying pipelines.

Often we create a pipeline by copying an existing pipeline (i.e. [cargo cult programming](https://en.wikipedia.org/wiki/Cargo_cult_programming)), and the old pipeline drops data that we need for the new pipeline. For example, the pipeline for Google
Plus What’s Hot drops older posts (because it is trying to rank fresh posts). This pipeline was copied to use for Google Plus Stream, where older posts are still meaningful, but the pipeline
was still dropping old posts. Another common pattern is to only log data that was seen by the user. Thus, this data is useless if we want to model why a particular post was not seen by the
user, because all the negative examples have been dropped. A similar issue occurred in Play. While working on Play Apps Home, a new pipeline was created that also contained examples
from two other landing pages (Play Games Home and Play Home Home) without any feature to disambiguate where each example came from.

#### Rule 7 - Turn heuristics into features, or handle them externally.

Usually the problems that machine learning is trying to solve are not completely new. There is an existing system for ranking, or classifying, or whatever problem you are trying to solve. This means that there are a bunch of rules and heuristics. These same heuristics can give you a lift when tweaked with machine learning. Your heuristics should be mined for whatever information they have, for two reasons. First, the transition to a machine learned system will be smoother. Second, usually those rules contain a lot of the intuition about the system you don’t want to throw away. There are four ways you can use an existing heuristic:

1. Preprocess using the heuristic. If the feature is incredibly awesome, then this is an option. For example, if, in a spam filter, the sender has already been blacklisted, don’t try
to relearn what “blacklisted” means. Block the message. This approach makes the most sense in binary classification tasks.
2. Create a feature. Directly creating a feature from the heuristic is great. For example, if you use a heuristic to compute a relevance score for a query result, you can include the score as the value of a feature. Later on you may want to use machine learning techniques to massage the value (for example, converting the value into one of a finite
set of discrete values, or combining it with other features) but start by using the raw value produced by the heuristic.
3. Mine the raw inputs of the heuristic. If there is a heuristic for apps that combines the number of installs, the number of characters in the text, and the day of the week, then
consider pulling these pieces apart, and feeding these inputs into the learning separately. Some techniques that apply to ensembles apply here (see **Rule #40**).
4. Modify the label. This is an option when you feel that the heuristic captures information not currently contained in the label. For example, if you are trying to maximize the number of downloads, but you also want quality content, then maybe the solution is to multiply the label by the average number of stars the app received. There is a lot of space here for leeway. See the section on [“Your First Objective”](#your-first-objective). Do be mindful of the added complexity when using heuristics in an ML system. Using old heuristics in your new machine learning algorithm can help to create a smooth transition, but think about whether there is a simpler way to accomplish the same effect.

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

### Your First Objective

> You have many metrics, or measurements about the system that you care about, but your machine learning algorithm will often require a **single objective, a number that your algorithm is “trying” to optimize.** I distinguish here between objectives and metrics: **a metric is any number that your system reports**, which may or may not be important. See also Rule **#2**.

#### Rule 12 - Don't overthink which objective you choose to directly optimize.

You want to make money, make your users happy, and make the world a better place. There are tons of metrics that you care about, and you should measure them all (see Rule **#2**). However,
early in the machine learning process, you will notice them all going up, even those that you do not directly optimize. For instance, suppose you care about number of clicks, time spent on the site, and daily active users. If you optimize for number of clicks, you are likely to see the time
spent increase. So, keep it simple and don’t think too hard about balancing different metrics when you can still
easily increase all the metrics. Don’t take this rule too far though: do not confuse your objective with the ultimate health of the system (see Rule #39). And, **if you find yourself increasing the directly optimized metric, but deciding not to launch, some objective revision may be required.**

#### Rule 13 - Choose a simple, observable and attributable metric for your first objective.

Often you don't know what the true objective is. You think you do but then you as you stare at the data and side­-by-side analysis of your old system and new ML system, you realize you want to tweak it. Further, different team members often can't agree on the true objective. The ML objective should be something that is easy to measure and is a proxy for the “true”
objective . So train on the simple ML objective, and consider having a "policy layer" on top that allows you to add additional logic (hopefully very simple logic) to do the final ranking.

The easiest thing to model is a user behavior that is directly observed and attributable to an
action of the system:

1. Was this ranked link clicked?
2. Was this ranked object downloaded?
3. Was this ranked object forwarded/replied to/e­mailed?
4. Was this ranked object rated?
5. Was this shown object marked as spam/pornography/offensive?

Avoid modeling indirect effects at first:

1. Did the user visit the next day?
2. How long did the user visit the site?
3. What were the daily active users?
Indirect effects make great metrics, and can be used during A/B testing and during launch
decisions.

Finally, don’t try to get the machine learning to figure out:

1. Is the user happy using the product?
2. Is the user satisfied with the experience?
3. Is the product improving the user’s overall well­being?
4. How will this affect the company’s overall health?

These are all important, but also incredibly hard. Instead, use proxies: if the user is happy, they will stay on the site longer. If the user is satisfied, they will visit again tomorrow. Insofar as well­being and company health is concerned, human judgement is required to connect any machine learned objective to the nature of the product you are selling and your business plan, so we don’t end up [here](https://www.youtube.com/watch?v=bq2_wSsDwkQ).

#### Rule 14 - Starting with an interpretable model makes debugging easier.

[Linear regression](https://en.wikipedia.org/wiki/Linear_regression), [logistic regression](https://en.wikipedia.org/wiki/Logistic_regression), and [Poisson regression](https://en.wikipedia.org/wiki/Poisson_regression) are directly motivated by a probabilistic model. Each prediction is interpretable as a probability or an expected value. This makes them easier to debug than models that use objectives (zero­one loss, various hinge losses, et cetera) that try to directly optimize classification accuracy or ranking performance. For example, if probabilities in training deviate from probabilities predicted in side­-by-­sides or by
inspecting the production system, this deviation could reveal a problem.

For example, in linear, logistic, or Poisson regression, **there are subsets of the data where the average predicted expectation equals the average label (1­moment calibrated, or just calibrated)<sup>3</sup>**. If you have a feature which is either 1 or 0 for each example, then the set of examples where that feature is 1 is calibrated. Also, if you have a feature that is 1 for every example, then the set of all examples is calibrated.

With simple models, it is easier to deal with feedback loops (see Rule **#36&**). Often, we use these probabilistic predictions to make a decision: e.g. rank posts in decreasing expected value (i.e. probability of click/download/etc.). However, remember when it comes time to choose which model to use, the decision matters more than the likelihood of the data given the model (see Rule **#27**).

#### Rule 15 - Separate Spam Filtering and Quality Ranking in a Policy Layer.

Quality ranking is a fine art, but spam filtering is a war.\* The signals that you use to determine high quality posts will become obvious to those who use your system, and they will tweak their posts to have these properties. Thus, your quality ranking should focus on ranking content that is posted in good faith. You should not discount the quality ranking learner for ranking spam highly. **Similarly, “racy” content should be handled separately from Quality Ranking.** Spam filtering is a different story. You have to expect that the features that you need to generate will be constantly changing. Often, there will be obvious rules that you put into the system (if a
post has more than three spam votes, don’t retrieve it, et cetera). Any learned model will have to be updated daily, if not faster. The reputation of the creator of the content will play a great role.

At some level, the output of these two systems will have to be integrated. Keep in mind, filtering
spam in search results should probably be more aggressive than filtering spam in email messages. Also, it is a standard practice to remove spam from the training data for the quality
classifier.

<sup>[Google Research Blog - Lessons learned while protecting Gmail](https://research.googleblog.com/2016/03/lessons-learned-while-protecting-gmail.html?m=1)</sup>
