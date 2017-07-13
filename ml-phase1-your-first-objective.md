
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
