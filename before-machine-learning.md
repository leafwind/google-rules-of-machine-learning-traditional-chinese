## Before Machine Learning

#### Rule 1 - Don't be afraid to launch a product without machine learning.*
Machine learning is cool, but it requires data. Theoretically, you can take data from a different problem and then tweak the model for a new product, but this will likely underperform basic heuristics. If you think that machine learning will give you a 100% boost, then a heuristic will get you 50% of the way there. For instance, if you are ranking apps in an app marketplace, you could use the install rate or number of installs. If you are detecting spam, filter out publishers that have sent spam before. Don’t be afraid to use human editing either. If you need to rank contacts, rank the most recently used highest (or even rank alphabetically). If machine learning is not absolutely required for your product, don't use it until you have data.

<sup>[Google Research Blog - The 280-Year-Old Algorithm Inside Google Trips](https://research.googleblog.com/2016/09/the-280-year-old-algorithm-inside.html?m=1)</sup>

#### Rule 2 - First, design and implement metrics.
Before formalizing what your machine learning system will do, track as much as possible in your current system. Do this for the following reasons:

1. It is easier to gain permission from the system’s users earlier on.
2. If you think that something might be a concern in the future, it is better to get historical
data now.
3. If you design your system with metric instrumentation in mind, things will go better for
you in the future. Specifically, you don’t want to find yourself grepping for strings in logs
to instrument your metrics!
4. You will notice what things change and what stays the same.

For instance, suppose you want to directly optimize one-­day active users. However, during your early manipulations of the system, you may notice that dramatic alterations of the user experience don’t noticeably change this metric.
Google Plus team measures expands per read, reshares per read, plus­-ones per read, comments/read, comments per user, reshares per user, etc. which they use in computing the goodness of a post at serving time. Also, note that an experiment framework, where you can group users into buckets and aggregate statistics by experiment, is important. See Rule **#12**.

By being more liberal about gathering metrics, you can gain a broader picture of your system. Notice a problem? Add a metric to track it! Excited about some quantitative change on the last
release? Add a metric to track it!

#### Rule 3 - Choose machine learning over complex heuristic.

A simple heuristic can get your product out the door. A complex heuristic is unmaintainable. Once you have data and a basic idea of what you are trying to accomplish, move on to machine
learning. As in most software engineering tasks, you will want to be constantly updating your approach, whether it is a heuristic or a machine-learned model, and you will find that the
machine-­learned model is easier to update and maintain (see Rule **#16**).
