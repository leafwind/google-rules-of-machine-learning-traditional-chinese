## Slow Growth and Optimization and Complex models

> There will be certain indications that the second phase is reaching a close. First of all, your monthly gains will start to diminish. You will start to have tradeoffs between metrics: you will see some rise and others fall in some experiments. This is where it gets interesting. Since the gains
are harder to achieve, the machine learning has to get more sophisticated. A caveat: this section has more blue-­sky rules than earlier sections. We have seen many teams
go through the happy times of Phase I and Phase II machine learning. Once Phase III has been reached, teams have to find their own path.

#### Rule 38 - Don't waste time on new features if unaligned objectives have become the issue.

As your measurements plateau, your team will start to look at issues that are outside the scope of the objectives of your current machine learning system. As stated before, if the product goals are not covered by the existing algorithmic objective, you need to change either your objective
or your product goals. For instance, you may optimize clicks, plus-­ones, or downloads, but make launch decisions based in part on human raters.

#### Rule 39 - Launch decisions are a proxy for long-term product goals.

Alice has an idea about reducing the logistic loss of predicting installs. She adds a feature. The
logistic loss drops. When she does a live experiment, she sees the install rate increase. However, when she goes to a launch review meeting, someone points out that the number of
daily active users drops by 5%. The team decides not to launch the model. Alice is disappointed, but now realizes that launch decisions depend on multiple criteria, only some of
which can be directly optimized using ML. The truth is that the real world is not dungeons and dragons: there are no “hit points” identifying the health of your product. The team has to use the statistics it gathers to try to effectively
predict how good the system will be in the future. They need to care about engagement, 1 day active users (DAU), 30 DAU, revenue, and advertiser’s return on investment. These metrics that are measureable in A/B tests in themselves are only a proxy for more long­term goals: satisfying users, increasing users, satisfying partners, and profit, which even then you could consider proxies for having a useful, high quality product and a thriving company five years from now.

**The only easy launch decisions are when all metrics get better (or at least do not get worse).** If the team has a choice between a sophisticated machine learning algorithm, and a
simple heuristic, if the simple heuristic does a better job on all these metrics, it should choose the heuristic. Moreover, there is no explicit ranking of all possible metric values. Specifically, consider the following two scenarios:

| Experiment | Daily Active Users | Revenue/Day |
|------------|--------------------|-------------|
| A          | 1 million          | $4 million  |
| B          | 2 million          | $2 million  |

If the current system is A, then the team would be unlikely to switch to B. If the current system is B, then the team would be unlikely to switch to A. This seems in conflict with rational behavior: however, predictions of changing metrics may or may not pan out, and thus there is a large risk involved with either change. Each metric covers some risk with which the team is concerned. Moreover, no metric covers the team’s ultimate concern, “where is my product going to be five
years from now”?

**Individuals, on the other hand, tend to favor one objective that they can directly optimize**. Most machine learning tools favor such an environment. An engineer banging out new features
can get a steady stream of launches in such an environment. There is a type of machine learning, multi­-objective learning, which starts to address this problem. For instance, one can
formulate a constraint satisfaction problem that has lower bounds on each metric, and optimizes some linear combination of metrics. However, even then, not all metrics are easily framed as machine learning objectives: if a document is clicked on or an app is installed, it is because that the content was shown. But it is far harder to figure out why a user visits your site. How to predict the future success of a site as a whole is [AI­complete](https://en.wikipedia.org/wiki/AI-complete), as hard as computer vision or
natural language processing.

#### Rule 40 - Keep ensembles simple.

Unified models that take in raw features and directly rank content are the easiest models to debug and understand. However, an ensemble of models (a “model” which combines the scores of other models) can work better. **To keep things simple, each model should either be an ensemble only taking the input of other models, or a base model taking many features,
but not both.** If you have models on top of other models that are trained separately, then combining them can result in bad behavior.

Use a simple model for ensembling that takes only the output of your “base” models as inputs.
You also want to enforce properties on these ensemble models. For example, an increase in the score produced by a base model should not decrease the score of the ensemble. Also, it is best
if the incoming models are semantically interpretable (for example, calibrated) so that changes of the underlying models do not confuse the ensemble model. **Also, enforce that an increase in the predicted probability of an underlying classifier does not decrease the predicted
probability of the ensemble.**

#### Rule 41 - When performance plateaus, look for qualitatively new sources of information to add rather than refining existing signals.

You’ve added some demographic information about the user. You've added some information about the words in the document. You have gone through template exploration, and tuned the
regularization. You haven’t seen a launch with more than a 1% improvement in your key metrics in a few quarters. Now what?
It is time to start building the infrastructure for radically different features, such as the history of documents that this user has accessed in the last day, week, or year, or data from a different property. Use [wikidata](https://en.wikipedia.org/wiki/Wikidata) entities or something internal to your company (such as Google’s [knowledge graph](https://en.wikipedia.org/wiki/Knowledge_Graph)). Use deep learning. Start to adjust your expectations on how much return you expect on investment, and expand your efforts accordingly. As in any engineering project, you have to weigh the benefit of adding new features against the cost of increased complexity.

#### Rule 42 - Don't expect diversity, personalization, or relevance to be as correlated with popularity as you think they are.

Diversity in a set of content can mean many things, with the diversity of the source of the content being one of the most common. Personalization implies each user gets their own results. Relevance implies that the results for a particular query are more appropriate for that query than any other. Thus all three of these properties are defined as being different from the ordinary.
The problem is that the ordinary tends to be hard to beat.

Note that if your system is measuring clicks, time spent, watches, +1s, reshares, et cetera, you are measuring the popularity of the content. Teams sometimes try to learn a personal model with diversity. To personalize, they add features that would allow the system to personalize (some features representing the user’s interest) or diversify (features indicating if this document has any features in common with other documents returned, such as author or content), and find that those features get less weight (or sometimes a different sign) than they expect. This doesn’t mean that diversity, personalization, or relevance aren’t valuable.\* As pointed out in the previous rule, you can do post-­processing to increase diversity or relevance. If you see longer term objectives increase, then you can declare that diversity/relevance is valuable, aside from popularity. You can then either continue to use your post­-processing, or directly modify the objective based upon diversity or relevance.

<sup>[Google Research Blog - App Discovery With Google Play](https://research.googleblog.com/2016/12/app-discovery-with-google-play-part-2.html?m=1)

#### Rule 43 - Your friends tend to be the same across different products. Your interests tend not to be.

Teams at Google have gotten a lot of traction from taking a model predicting the closeness of a connection in one product, and having it work well on another. Your friends are who they are. On the other hand, I have watched several teams struggle with personalization features across product divides. Yes, it seems like it should work. For now, it doesn’t seem like it does. What has sometimes worked is using raw data from one property to predict behavior on another. Also, keep in mind that even knowing that a user has a history on another property can help. For instance, the presence of user activity on two products may be indicative in and of itself.
