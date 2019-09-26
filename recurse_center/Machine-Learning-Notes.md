# Notes: Introduction to Machine Learning
I'm using my time at the RC to go through [the fastAI course in machine learning](http://course18.fast.ai/ml). Here are my notes

## Table of Contents
- [General Setup](#general-setup)
  - [Using Docker](#using-docker)
  - [Using the RC Cluster](#using-the-rc-cluster)
- [L1 - Introduction to Random Forests](#L1---introduction-to-random-forests)
  - [bash tips](#bash-tips)
  - [Python tips](#python-tips)
  - [Machine learning concepts](#machine-learning-concepts)
  - [Jupyter notebook tips](#jupyter-notebook-tips)
- [L2 - Random Forest Deep Dive](#l2---random-forest-deep-dive)
  - [speeding things up](#speeding-things-up)
  - [Single tree](#single-tree)
  - [Bagging](#bagging)
- [L3 - Model Interpretation](#L3---Model-Interpretation)
- [L4 - Feature Importance and the Tree Interpreter](#L4---Feature-Importance-and-the-Tree-Interpreter)
  - [one-hot encoding](#one-hot-encoding)
  - [Removing Redundant Features](#Removing-Redundant-Features)
  - [Partial dependence](#Partial-dependence)
- [L5 - Extrapolation and RF from Scratch](#L5---Extrapolation-and-RF-from-Scratch)
- [L6 - Data Products and Live Coding](#L6---Data-Products-and-Live-Coding)
- [L7 - RF from Scratch and Gradient Decent](#L7---RF-from-Scratch-and-Gradient-Decent)
- [Important Commands](#important-commands)
- [Trying an unrelated dataset](#trying-an-unrelated-dataset)

## General Setup

### Using Docker
Here are the steps I took to set up a docker container for my Jupyter notebook that I needed for the course. I didn't feel like installing anaconda on my own machine, so I made a docker image for it instead

Clone the repository for the course
``` bash
git clone https://github.com/fastai/fastai.git
```

Make sure to set the permissions so that you can use it in your docker container. It's a bit hacky, but I recursively set read/write/execute permissions for everyone
``` bash
chmod -R 777 fastai
```

Make a Dockerfile with the following contents
``` bash
FROM jupyter/scipy-notebook:2c80cf3537ca
WORKDIR /app
ADD . /app
EXPOSE 80
ENV NAME World
```

Build the docker image and run it
``` bash
docker build -t fastai_course .

docker run -p 8888:8888 fastai_course
```

Your terminal should prompt you with the container's prompt. Use conda to install the fastai stuff. [Here's an article about how much it sucks](https://medium.com/@GuruAtWork/fast-ai-lesson-1-7fc38e978d37)
```bash
conda install -c pytorch -c fastai fastai
conda install -c conda-forge bcolz
pip install opencv-python
conda install graphviz
conda install python-graphviz
pip install sklearn_pandas
pip install isoweek

pip install pandas_summary &&
pip install torchtext &&
pip install feather-format  &&
pip install jupyter_contrib_nbextensions &&
pip install plotnine  &&
pip install docrepr &&
pip install awscli &&
pip install kaggle-cli &&
pip install pdpbox &&
pip install seaborn
```

After this, you can stop and start the container whenever you want
- press `ctrl-c` to shut down the notebook server. Leaves container on disk so that you can restart it later
- to restart:
``` bash
docker ps -a # to get the container ID
docker start -a ###CONTAINER_ID###
```
- to remove forever
``` bash
docker ps -a # to get the container ID
docker rm ###CONTAINER_ID###
```

#### How to get data files from your machine to your container?
If you're like me, you're having a hard time getting the cURL command to download the zip files from Kaggle. Just download the zip to your machine, and use the `docker cp` command to get it to your container
``` bash
docker ps -a # to get the container ID
docker cp bluebook-for-bulldozers/. __CONTAINER_ID__:/app/bluebook-for-bulldozers
```

#### You may need to install fastai 0.7 in order for the notebooks to load properly
``` bash
## ssh into the container
docker container ps
docker exec -it __CONTAINER_ID__ /bin/bash

## install the stuff
pip install fastai==0.7.0
pip install torchtext==0.2.3
```
#### Sources:
- [Jupyter's stacks](https://github.com/jupyter/docker-stacks/)
- [Fastai v0.7 installation thread](https://forums.fast.ai/t/fastai-v0-7-install-issues-thread/24652)
- [Jupyter notebook and Docker](https://jupyter-docker-stacks.readthedocs.io/en/latest/)

### Using the RC Cluster
This isn't needed for the machine learning class, but if you're taking the deep learning course, you'll need a GPU
- [setting up fastAI on the RC's cluster:](https://marrri.com/posts/setting-up-fast-ai-cluster/)
- [installing conda on linux](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html)
- [alpine linux docker image](https://hub.docker.com/_/alpine)

ssh into RC cluster:
`ssh nwogwugwu@mercer.cluster.recurse.com`

activate fastai: `source activate fastai`

set up port forwarding (on local machine): `ssh -N -f -L localhost:8888:localhost:6565 nwogwugwu@mercer.cluster.recurse.com`

run the jupyter notebook: `jupyter notebook --no-browser --port 6565`


## L1 - Introduction to Random Forests

### bash tips
* When using a Jupyter notebook, use `!` in front of bash script. This tells the notebook that you're not trying to use python

To display the first few lines of a file:
``` bash
head /path/to/file.csv
```
_copy as cURL_ sounds like a game changer
- make sure to use `-o` to output it into a file instead of displaying it in your terminal

You can use the [wc command](https://linuxconfig.org/wc-1-manual-page) to print out the number of lines in a CSV:
``` bash
wc -l Train.csv
```

### Python tips
[python 3.6 format strings](https://docs.python.org/3/whatsnew/3.6.html#whatsnew36-pep498) are a nifty way to format strings

### Machine learning concepts
- **Structured Data**: This is not an agreed upon term. In some cases, it refers to information where the data is in columns and is the same type. For the purposes of this course, the information doesn't have to be the same type
- **pandas**: important datascience library. So common that it's the standard to abbreviate it to pd
  - includes the concept of dataframes
- **[NumPy](http://www.numpy.org/)**: python scientific computing library. Lets us treat dataframes as python variables
- **Regressors**: Predict _continuous_ variables
- **Classifiers**: Predict _classifiable variables_
- **Ordinal Classifiable Variables**: categories that are ordered
- **Random Forest**: universal machine learning technique for prediction. It's a method for regression and classification. [wiki page](https://en.wikipedia.org/wiki/Random_forest)
  - generally doesn't overfit things
  - generally doesn't make assumptions
  - trivially parallelizable

- **[Curse of dimensionality](https://en.wikipedia.org/wiki/Curse_of_dimensionality)**: Apparently _not quite true_. It's the idea that the more columns/dimentions you add, the more likely you have a point that is on the edge of at least one dimention, so the distance between points becomes less meaningful. This isn't quite true because _k-nearest neighbors works well, regardless of dimentions_
- **[No free lunch theorem](https://en.wikipedia.org/wiki/No_free_lunch_theorem)**: Apparently _not quite true_. Mathematical folklore theorem that says that no mathematical model fits perfectly for any dataset. _Real world datasets generally aren't random_, so you can have useful models for things


Note: When you're working with machine learning, it's important to know what your goal is. In the case of the bulldozer problem, we're looking for the **RMSLE**, or the _root mean squared log error_ 

### Jupyter notebook tips
If you include these in a Jupyter notebook, the stuff will auto-reload
``` python
%load_ext autoreload
%autoreload 2
```

include this if you want to make plots inside of a notebook
``` python
%matplotlib inline
```
- If you want to figure out where a command comes from, just type it into a code box. If you want a man page, add a question mark. Two question marks, and it displays the function source code.

`shift+tab` on a function displays more information. You can hit those keys once, twice or three times, depending on the amount of information you want

If you're missing a library in the Jupyter Notebook, feel free to `pip install the_library` in the terminal. You may need to restart your kernel.


## L2 - Random Forest Deep Dive

The fastai library
- opinionated
- it's part of the fastai repo, so you could use it in your own code

to create a symlink:
``` bash
ln -s /path/to/source/ ./destination
```
symlink is something that can live inside your repo/directory
evaluation metrics
root mean squared error

We need all of our columns to be numbers
- replace date with a set of columns (is start quarter, what's the year, ...)
- train_cats to replace strings with categories `df_raw.UsageBand`. Category is a pandas class. We replace every categorical column with their codes using `proc_df`
- continuous columns with missing values, we populated with the median value. Then we added a boolean column to indicate whether that column was missing

Once you have the data in the right way, you can fit the data
``` python
model = RandomForestRegressor(n_jobs=-1)
model.fit(dataframe, y)
model.score(dataframe, y)
```
The result from this code is a number between negative infinity and one (like `0.9823743627283`), which is your *r-squared*
- **r-squared**: or [Coefficient of determination](https://en.wikipedia.org/wiki/Coefficient_of_determination) - the proportion of the variance in the dependent variable that is predictable from the independent variable(s)
- **overfitting**: when your model has high varience. It becomes very good at predicting points in your dataset, but bad at predicting points outside of it
- **validation set**: helps us diagnose overfitting
- **hyperparameters**: parameters that we use to tune our model

### speeding things up
you can randomly sample data. This will make your model less accurate, but it will let you work faster. When you're happy with your model, you can try again with the full dataset

### Single tree
A tree is a series of binary decisions. For each decision, you're splitting out the data based on a parameter. You choose the parameter out of the set of possible parameters to choose from for this dataset. You determine which parameter to use by getting the most different weighted average for the two sets of data that got split out. Do this over and over again until each node has only one datapoint in it
- your r-squared value ends up being one, because you can perfectly predict what's in the dataset. This means that you're overfitting.

### Bagging
How do we get around this? We split up the dataset into _n_ subsets, randomly. We make single trees for each of them, and each tree will be overfitting in a different way. When we combine them together, the errors should (for the most part) cancel each other out. _this is a random forest_

- the number of trees that you use is a hyperparameter

- Out of bag samples
- out of bag predictions
- out of bag score
- subsampling
- min_samples_leaf -> the higher this is the more the tree will generalize. It will also mean fewer computations and can result in less volotility
- **column sampling**: every binary split, we choose from a different subset of columns. `max_features` parameter

## L3 - Model Interpretation
`proc_df` does a lot of things:
- populates missing rows with medians
- addsa a bool column do inticate whether that row includes the value
- turns categorical objects into pandas code

It won't work perfectly under some circumstances. For instance:
- if the test set and the validation set have different columns that are missing
- the median for the test set might be different

- **star schema**: a [data warehousing](DDIA-Storage-and-Retrieval#data-warehousing) schema. Includes a central transactions table and a set of "metadata tables" that it joins to
- **snowflake schema**: a super star schema. The metadata tables have _their own_ metadata tables
- It's not uncommon to pull separate datasets (like a list of holidays) to act as metadata on your dataset
- standard deviation of the predictions tells us the relative confidence of the predictions. Trees that deviate might be less reliable

- **data leakage**: data that shows up in the training data that wouldn't show up in real life
- **colinearity**: when one column can predict another (they're linearly related)
- **feature importance**: tells us which columns matter within a random forest. Generally, there's only a handful of things that matter, and you can get away with discarding the least important columns. It could make it faster to train your model. **how does this work?**
  1. shuffle each column in the dataset
  2. see how much the score suffered

## L4 - Feature Importance and the Tree Interpreter
Here are some hyperparameters:
- set_rf_samples
- min_samples_leaf
- max_features
- max_n_cat

### one-hot encoding
for categorical features with relatively few categories. We can split them up into a set of boolean columns, where `max_n_cat` determines which features are eligible
- **cardinality**: number of usage bands. This is what we compare with `max_n_cat`

### Removing Redundant Features
- **dendrogram**: a tree diagram. This will help us determine which columns are related
- **hierarchical clustering**: look at every pair of objects, find the closest ones. This is how our dendrograms get plotted
- **correlation coefficients**: tell you how aligned two variables are. Assumes linearity
- **rank correlation**: replace every `x` with its rank and every `y` with _its_ rank. This makes a linear plot, which is needed for hierarchical clustering
- **spearman's r**: name of the most popular rank correlation

### Partial dependence
For reatures that are important, how do they relate to the dependent variables?
- **PDP**: Partial Dependence Plot. Shows the marginal effect one or two features have on the predicted outcome of a machine learning model ([definition source](https://christophm.github.io/interpretable-ml-book/pdp.html))
- sometimes, just looking at a univariate plot won't give you a clear relationship between the feature and the dependent variables. You can plot out prices in a way that's not affected by other variables using an *ICEplot* or a *PDP interaction plot*
- Sometimes important features seems to depend on another feature that's not in the dataset. In this case, it's age

### Tree Interpreter
A tree interpreter will help us explain why our model is making the predictions that it is. You can use the treeinterpreter library like this:
``` python
prediction, bias, contributions = tree_interpreter.predict(model, single_row)
```
Where:
  - `prediction` is what the `model` predicts for the `single_row`
  - `bias` represents what the average prediction was for the entire dataset
  - `contributions` is a list of all of the tree splits, and how each one affected the prediction

## L5 - Extrapolation and RF from Scratch
Machine learning is different from other models because of the emphasis on _generalization_. We care about _generalization accuracy/generalization error_, as opposed to other work, where we care about how well we map to the observations
- **Generalization**: how well your model works with data that's not in your original dataset, or how you can use your model to extrapolate for more general data. It's the opposite of overfitting
- "The most common way that people check for the ability to generalize is to create a random sample"
- To review, we have a **training set** (to train your model), a **validation set** (To make sure that your model generalizes) and a **testing set** (to double check that your model generalizes)
- One alternative to using a valitation set is to use the **OOB Score**. It's not as accurate as the validation score is, because you're using fewer trees per row to get the score
- For validation sets, it's wrong to take datapoints randomly when the set depends on time. Create the validation set that's later in time

### Cross Validation
(from [GeeksforGeeks](https://www.geeksforgeeks.org/cross-validation-machine-learning/)): Cross-validation is a technique in which we train our model using the subset of the data-set and then evaluate using the complementary subset of the data-set.
- benefit: you can use all of the data
- downsides: It takes more time. Also, all of the sets are random data, not temporal data

### More on Tree Interpretation
- **Waterfall charts** can be a helpful way to visualize tree interpretations

### Extrapolation
If the validation set isn't picked at random, then you can build a model to determine whether something is in the validations set. This can be useful if there are some factors that are affected by time, and that you should weigh more/less heavily based on time

### Writing Random Forest from scratch
You need a forest, which is a set of decision trees


## L6 - Data Products and Live Coding
Examples of machine learning use cases
- **Horizontal**: In business, horizontal means something that you do across different kinds of business. i.e. everything involving marketing.
- **Vertical**: Something you do within a business or within a supply chain or a process.
- **Objective**: The reason we would use ML as a tool
- **Levers**: Something your org can do to drive objectives (deals, swag, marketing)
- **Data**: information an org has to determine which levers to pull
- **Models**: simulation models help us predict what will happen when we pull the levers
- predictive model vs optimization models

### Different random forest interpretation methods
- **Confidence based on tree variance**: The variance of the predictions of the trees, how confident you are about the predictions
- **feature importance**: If randomizing a feature messes up your model, it's probably more a more imporant feature
- **partial dependence**: More than one feature determines your dependent variable

### Tree Interpreter
use this to determine what each feature does

### Interaction Importance
Interaction means appears on the same path through a tree. Indicates that the combination of features is important
- **gradient boosting machine**

## L7 - RF from Scratch and Gradient Decent
- historically, there hasn't been a lot of research into random forests. There's been a new wave of research recently
- size of validation sets: If you can improve your model by 0.2%, that can be a lot if your error was small in the first place (if it's 1%, then 0.2% is a 20% overall increase, which can be a big deal). Also, if there are less than 23 items, then it gets unstable
- Important expressions:
```
binomial distribution of n samples and probability p: n*p
standard deviation: n*p*(1-p)
validation set accuracy: standard deviation/sqrt(n)
```
you can use this to determine how big your validation set should be

one-shot/zero-shot learning is good for face recognition. Less common classes might be replicated (or weighted) so that the model takes them into account
- Random tree classifiers are almost identical or can be almost identical to the random tree regressors.
- every split we make should reduce the standard deviation as much as possible

- your validation set size should depend on how accurate you're planning on being
- **Cython**: Python translated to C. You can write Python and it compiles and runs really fast
- **Neural Nets**: Can extrapolate. "Neural networks are going to allow us to go beyond just the kind of nearest neighbors approach of random forests."

## L8 - Gradient Descent and Logistic Regression
- decision trees are limited in the complexity of their models
- vector = 1D array = rank 1 tensor
- matrix - 2D array = rank 2 tensor
- axis/dimention 0

- we need to normalize our data for deep learning. We also need to be able to reorder dimentions and slice into tensors

- neural nets take vectors, do a matrix multiply of that vector and do a matrix multiply on the product. over and over until you get what you want
- we _fit_ a neural net to data

- **loss function**: what we use to score how goot the model is. We want it to be closer to zero
- pytorch: like numpy, but it runs on the GPU instead of the CPU. 
- argmax: returns the index of the max number
- **logistic regression** - model, estimates _m_ and _b_ in _y = mx + b_
  - w: weight
  - b: bias
- **universal approximation theorem**
- we need to make sure that the weight matrix is the appropriate size. Otherwise, it'll cause a feedback loop for numbers that are a little bigger/smaller than we want
- softmax: 

## L9 - 


## Important commands
``` python
# Get a dataframe from a csv
raw_dataframe = pd.read_csv(f'{PATH}Train.csv', low_memory=False, parse_dates=["saledate"])

# Display that dataframe
display(raw_dataframe.tail().T)

# Make a model (random forest regressor). the n_jobs=-1 means you don't have to restrict the number of jobs
model = RandomForestRegressor(n_jobs=-1)

# extract date fields from datetime fields, puts them into categories
add_datepart(df_raw, 'saledate')
df_raw.saleYear.head()

# convert strings to pandas categories
train_cats(df_raw)
df_raw.UsageBand.cat.categories
df_raw.UsageBand.cat.set_categories(['High', 'Medium', 'Low'], ordered=True, inplace=True)
df_raw.UsageBand = df_raw.UsageBand.cat.codes

# save data to feather file
os.makedirs('tmp', exist_ok=True)
df_raw.to_feather('tmp/bulldozers-raw')

```
## Trying an unrelated dataset
I worked with other recursers to try these techniques on [another Kaggle competition](https://www.kaggle.com/c/house-prices-advanced-regression-techniques). Take a look at [our results so far](https://gist.github.com/ngozinwogwugwu/423e360ae5bdf7d204f16c0ec09c6e2f)
