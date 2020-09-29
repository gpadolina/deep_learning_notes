# Fundamentals of machine learning

## Four branches of machine learning
The three specific types of machine-learning problems: binary classification, multiclass classification, and scalar regression are all instances of *supervised learning*,
where the goal is to learn the relationship between training inputs and training targets.

#### Supervised learning
This is by far the most common case. Although supervised learning mostly consists of classfication and regression, there are more exotic variants as well.
* *Sequence generation* - Given a picture, predict a caption describing it.
* *Syntax tree prediction* Given a sentence, predict its decomposition into a syntax tree.
* *Object detection* - Given a picture, draw a bounding box around certain objects inside the picture.
* *Image segmentation* - Given a picture, draw a pixel-lvel mask on a specific object.
#### Unsupervised learning
Consists of finding interesting transformation of the input data without the help of any targets. Unsupervised learning is the bread and butter of data analytics and it's
often a necessary step in better understanding a dataset before attempting to solve a supervised-learning problem. *Dimensionality reduction* and *clustering* are
well-known categories of unsupervised learning.
#### Self-supervised learning
This is a specific instance of supervised learning, but it's different enough that it deserves its own category. Self-supervised learning is supervised learning without
human-annotated labels. There are still labels involved, but they're generated form the input data, typically using a heuristic algorithm.
#### Reinforcement learning
In reinforcement learning, an *agent* receives information about its environment and learns to choose actions that will maximize some reward.

## Evaluating machine-learning models
The reason not to evaluate the models on the same data they were trained on quickly became evident: after just a few epochs, all three models began to *overfit*. That is,
their performance on never-before-seen data started stalling or worsening compared to their performance on the training data - which always improves as training
progresses.
In machine learning, the goal is to achieve models that *generalize* - that perform on never-before-seen data and overfitting is the central obstacle. You can only control
that which you can observe, so it's crucial to be able to reliably measure the generalization power of your model.
#### Traning, validation, and test sets
Evaluating a model always boils down to splitting the available data into three sets: training, validation, and test. You train on the training data and evaluate your model
on the validation data. Once your model is ready for prime time, you test it one final time on the test data.

Why not have two sets? The reason is that developing a model always involves tuning its configuration: for example, choosing the number of layers or the size of the layers
(called the *hyperparameters* of the model, to distinguish them from the *parameters*, which are the network's weights). You do this tuning by using as a feedback signal
the performance of the model on the validation data. In essence, this tuning is a form of *learning*: a search for a good configuration in some parameter space. Tuning the
configuration of the model based on its performance on the validation set can quickly result in *overfitting to the validation set*, even though your model is never directly
trained on it.

Central to this phenomenon is the notion of *information leaks*. Every time you tune a hyperparameter of your model based on the model's performance on the validation set,
some information about the validation data leaks into the model.

At the end of the day, you'll end up with a model that performs artifically well on the validation data, because that's why you optimized it for. You care about performance
on completely new data, not the validation data, so you need to use a completely different, never-before-seen dataset to evaluate the model: the test dataset.

Let's review three classic  evaluation recipes: simple hold-out validation, K-fold avalidation, and iterated K-fold validation with shuffling.
* *Simple hold-out validation*: Set apart some fraction of your data as your test set. Train on the remaining data, and evaluate on the test set. This is the simplest
evaluation protocol and it suffers from one flaw: if little data is available, then your validation and test sets may contain too few samples to be statistically
representative of the data at hand.
* *K-fold validation*: With this approach, you split your data into K partitions of equal size. For each partition i, train a model on the remaining K-1 partitions, and
evaluate it on partition i. Your final score is then the averages of the K scores obtained. This method is helpful when the performance of your model shows significant
variance based on your train-test split. Like hold-out validation, this method doesn't exempt you from using a distinct validation set for model calibration.
* *Iterated K-fold validation with shuffling*: This one is for situations in which you have relatively little data available and you need to evaluate your model as
precisely as possible. It consists of applying K-fold validation multiple times, shuffling the data every time before splitting it K ways.
## Data preprocessing, feature engineering, and feature learning
How do you prepare the input data and targets before fedding them into a neural network? Many data-preprocessing and feature-engineering techniques are domain specific.
#### Data preproceesing for neural networks
Data preprocessing aims at making the raw data at hand more amenable to neura networks. This includes vectoriation, normalization, handling missing values, and feature
extraction.
* *Vectorization* - All inputs and targets in a neural network must be tensors of floating-point data. Whatever data you need to process - sound, images, text - you must
first turn into tensors, a step called *data normalization*.
* *Value normalization* - Before you feed data into a network, you have to normalize each feature independently so that it has a standard deviation of 1 and a mean of 0.
In general, it isn't safe to feed into a neural network data that takes relatively large values or data that is heterogeneous. Doing so can trigger large gradient updates
that will prevent the network from converging. To make learning easier for your networks, your data should *take small values* - typically in the 0-1 range, and *be
homogeneous* - all features should take values in roughly the same range.
* *Handling missing values* - In general, with neural networks, it's safe to input missing values as 0, with the condition that 0 isn't already a meaningful value. The
network will learn from exposure to the data that the value 0 means *missing data* and will start ignoring the value. Note that if you're expecting missing values in the
test data, but the network was trained on data without any missing values, the network won't have learned to ignore missing values.
#### Feature engineering
*Feature engineering* is the process of using your own knowledge about the data and about the machine-learning algorithm at hand to make the algorithm work better by
applying hardcoded transformations to the data before it goes into the model.

The essence of feature engineering: making a problem easier by expressing it in a simpler way. It usually requires understanding the problem in depth. Fortunately, modern
deep learning removes the need for most feature engineering because neural networks are capable of automatically extracting useful features from raw data.
## Overfitting and underfitting
Overfitting happens in every machine-learning problem. The fundamental issue in machine learning is the tension between optimization and generalization. *Optimization*
refers to the process of adjusting a model to get the best performance possible on the training data (the *learning* in *machine learning*), whereas *generalization*
refers to how well the trained model performs on data it has never seen before. The goal of the game is to get good generalization, but you don't control generalization;
you can only adjust the model based on its training data.

At the beginning of training, optimization and generalization are correlated: the lower the loss on training data, the lower the loss on test data. While this is happening,
your model is said to be *underfit.* But after a certain number of iterations on the training data, generalization stops improving, and validation metrics stall and then
begin to degrade: the model is starting to overfit.