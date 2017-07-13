
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

