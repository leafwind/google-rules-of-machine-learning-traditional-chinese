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

