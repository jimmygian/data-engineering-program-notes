Last week, we covered several data modeling approaches for **batch analytics** that structure data in a way that supports **analytical queries**. 

This week, we'll cover how you can prepare and model data **for machine learning use cases**. The goal of this type of modeling is to structure the data in a way that can help the data scientist or a machine learning engineer **understand what the data represents** so they can use the data to develop machine learning systems and discover *hidden patterns* in the data. 

Building a machine learning system involves several phases, so here I'm going to refer to the machine learning project lifecycle framework that Andrew presented in his machine learning and production course. 

![[Screenshot 2026-02-01 at 09.27.46.png]]

These phases include 
- scoping out the machine learning project, 
- collecting and preparing the data, 
- developing the machine learning model, 
- and deploying the machine learning system. 

Your job as a data engineer is to build and maintain the data pipelines that serve data to one or more of these stages. 

![[Screenshot 2026-02-01 at 09.31.36.png]]

In these pipelines, 
- you **collect data** from multiple sources, 
- then **combine** the data into a format that is suitable for the machine learning algorithm. 
- You might also be tasked with **cleaning** the data, converting it, 
- or even **creating some additional columns** or features. 
- And finally, you **store and share the data** with the machine learning or data science team to help you avoid confusing a machine learning model with what you'll be doing as a data engineer, which is modeling the data. 

>	I'll rename this modeling phase to algorithm development to clearly indicate that this phase of the machine learning project lifecycle is all about developing the algorithm for the machine learning project. 

![[Screenshot 2026-02-01 at 09.30.09.png]]

This framework is highly iterative, and we'll dive into the details of each phase later this week. 

So you might wonder how the role of a **Data Engineer** is different from that of a **data scientist** or **ML engineer**. Admittedly, the boundaries between machine learning engineering, data science, and data engineering are increasingly fuzzy, and the responsibilities of each of these roles vary dramatically between organizations. 

![[Screenshot 2026-02-01 at 09.33.46.png]]

- Some organizations might have entirely separate data teams that handle the entire lifecycle for all machine learning projects. 
- In other settings, you, the data engineer, might be responsible for serving just the raw data to the machine learning or data science team. Then the machine learning engineers or data scientists take over the data processing tasks. 
- Or you might be asked to process the raw data so that these downstream stakeholders can use the process data directly to train the machine learning algorithms. 
- If your organization is small or doesn't have a mature machine learning team, you might even handle some of these tasks that are extremely specific to machine learning, such as featurization of data. 

But in any case, as a data engineer, you play a key role in helping your organization adopt a data centric approach to machine learning, which focuses on enhancing the machine learning system by collecting high quality data. 

You might have heard the saying ***"garbage in, garbage out"***, which refers to the idea that the quality of the output of any system is determined by the quality of its inputs. 

So by **carefully preparing the data** for the machine learning algorithms, you can help the data scientists or machine learning engineers extract accurate and meaningful insights, creating more useful machine learning systems. 

And so I suggest you develop a basic understanding of how machine learning works because this will go a long way in helping you provide value to your organization when it comes to building machine learning systems. 

![[Screenshot 2026-02-01 at 09.39.06.png]]


## Machine Learning Overview

Let's go through some basic machine learning terminology and concepts that you should be familiar with as a data engineer. In this section, I'll cover topics such as: 
- supervised versus unsupervised learning, 
- classification versus regression, 
- and training versus testing. 

I'll also go over the different phases of the machine learning project lifecycle to give you a framework for thinking about how data is used throughout the development of a machine learning system. 

Let's say you work as a data engineer at an e-commerce company that's interested in developing three machine learning systems:

**ML System 1** - Supervised / Classification

The first system aims to predict customer churn, in other words, whether a customer will stop purchasing from the company over a specific period of time. 

![[Screenshot 2026-02-01 at 09.49.01.png]]

Suppose you have access to a historical customer demographic data, their browsing and purchase history, and the past ten years worth of customer churn data. 

The company wishes to ***learn from this data*** and create a rule or formula that can take in a customer's information and ***predict*** whether they will churn or not within a specific time period. Instead of manually looking at the data to figure out this rule, which will likely be very complex, the company can train a machine learning algorithm to automatically learn the rule through a technique called supervised learning. 


It learns from: 
- the ***characteristics*** of historical customer information, which are also called ***features***, 
- and the value that needs to be predicted, meaning whether or not a customer will churn, which is called a ***label***. 

This type of machine learning is called ***supervised learning*** because the labels supervise what needs to be learned from the features. 

So when you create a data pipeline that serves data for supervised machine learning use cases, you would need to work with the machine learning team to learn **what features and labels they want the data to contain.** 


**ML System 2** - Supervised / Regression

The second system the company wants to develop aims to **predict the sales of products** for the *next new year holiday*. 

![[Screenshot 2026-02-01 at 09.49.14.png]]

This is also a supervised machine learning problem because the machine learning algorithm is learning to predict future sales from a set of features and labels, with the features being characteristics of historical product and sales data and the labels being historical holiday sales amounts. 

But the **type of labels** used in this second system, meaning the kind of values we're aiming to predict, is different from the first system. 

- In the first System, the label is ***categorical*** and only takes on a limited number of values. The customer will either churn or not. This type of supervised learning is called **classification** you are classifying a customer as a customer who will churn or a customer who will not churn. 
- In this second System, the label is ***numerical***, representing the value of sales. This type of supervised learning is called ***regression***. 

So as a data engineer, the type of labels you will serve will be different for classification and regression machine learning use cases. 


**ML System 3** - Unsupervised

The third system the company wants to develop aims to ***segment customers into groups*** based on their purchasing behaviors. 

![[Screenshot 2026-02-01 at 09.50.47.png]]

Although you have lots of historical customer data, this data is not labeled for this purpose, meaning that it does not contain specific information about the groups themselves, such as the number of groups or the characteristics that separate the groups, and it does not indicate which group each customer belongs to. 

This is what the company would like to know by applying what is called an ***unsupervised learning algorithm.*** In this case, there are no labels in the data to supervise the learning. 

The algorithm will instead have to figure out how to group the customers based on the *similarity of the profiles or purchasing behaviors.* 

>	*When you serve data to this type of learning, the data will only contain features without any labels.* 


### The phases of a ML framework

So now that we've covered these three types of machine learning systems, let's take a look at the phases of the machine learning project lifecycle framework that Andrew proposed. You'll find this framework helpful for many machine learning projects, and we'll be focusing on ***where you can provide value as a data enginee***r. 

1. Scoping

![[Screenshot 2026-02-01 at 09.57.33.png]]
The first phase is ***scoping***, where the machine learning engineer, or data scientist, has to 
- define the project 
- and decide exactly what business problems they want to apply machine learning to. 

2.  Data

![[Screenshot 2026-02-01 at 09.57.49.png]]
Then the second phase is all about data. The machine learning engineer will work with you - the Data Engineer - to determine what features and labels you need to collect for training the machine learning algorithms. 

This is the phase that you will be most involved with as a data engineer and includes: 
- ***defining the data*** and establishing a baseline, 
- and also ***labeling and organizing*** the data. 

3. Algorithm Development (Modeling)

The third phase involves selecting and **training the algorithm and performing error analysis.** 

The machine learning engineer takes the data you serve them, maybe processes it further, then uses it to train machine learning algorithms. 

They typically **split the data** into 
- a training set 
- and a test set. 

Then they use the training set data to train several machine learning algorithms with different configurations. They can choose from classical machine learning algorithms such as linear regression, logistic regression, decision trees, random forest, and boosted trees. These classical algorithms are generally easy to implement and train, and they expect data to be in a tabular form. However, as the size of the data increases, the performance of these classical machine learning algorithms will reach a plateau. This is where more complex machine learning algorithms, such as deep neural networks, can help. These complex algorithms not only work with tabular data, but they can also work with image data using convolutional neural networks, time series data using recurrent neural networks, and text data using large language models. 

After the machine learning team train several machine learning algorithms, they select the best one in a process called ***cross-validation***. 

![[Screenshot 2026-02-01 at 09.59.56.png]]

Finally, they evaluate the selected model using the data from the test set to analyze the model's performance. They might discover that they need to fix something in the collected data, add more features, or simply collect more data. So the team might ask you to serve an ***updated set of data*** to continue with the algorithm development phase. This iterative process can continue until the team is happy with the performance of the machine learning system. 

4. Deployment

The final phase is ***deployment***, before the machine learning engineer takes the system to deployment, they'll carry out a final check to make sure that the system's performance is good enough and that it's sufficiently reliable for the application. 

Then they will write the software to put the system into production, monitor the system, track the data that continues to come in, and maintain the system. 

During the deployment phase, you might be responsible for:
- ***preparing and serving the data that needs to be fed to the deployed model.*** 
- You might also help with maintaining the machine learning system by serving an updated set of data that's used to retrain and update the model as needed. 

![[Screenshot 2026-02-01 at 10.00.51.png]]

So in the machine learning project lifecycle, you might not be involved with the details of the algorithm development and analysis, but you'll be responsible for setting up the pipeline to serve data that supports these phases. 

![[Screenshot 2026-02-01 at 10.01.13.png]]


## Modeling Data for Traditional Machine Learning Algorithms

When you serve data for training **classical machine learning algorithms,** typically, the data is expected to be in a ***tabular form*** containing *only numerical values*. 

When you model the data for these use cases, you need to **decide** 
- what features to include 
- and what label to use. 

The decisions are usually made by the machine learning or data science team. Depending on the project and the level of iteration, you might just serve raw data to the team if they are interested in exploring the data, or you might need to process the data and convert the features and the labels into a numerical tabular form before serving it to them. 


#### Pre-preocessing steps for preparing Tabular Data for Training

Let's go through some basic **pre-processing steps** to prepare tabular data for training. 

Back to the customer nhurm example that I introduced in the previous video, here's what the raw dataset might look like. 



Each row corresponds to a customer showing the number of purchases they've made, the date of their last purchase, the customer's income, the time they spent on the platform, their account type, and whether they have churned or not. 
![[Screenshot 2026-02-01 at 10.05.23.png]]

Depending on the project and the team you're working with, you may be expected to serve raw data like this, or you might be asked to process the data and convert it into a numerical tabular form, which might look something like this. 

![[Pasted image 20260201100659.png]]
>	Notice that I've separated the churn column into a vector that contains the labels for each customer, where one means that the customer has churned and zero means that they have not. 

You can see there are no missing values or duplicate rows, and each column consists of numerical values that are within a similar range. 

Moreover, notice that **I've created a new purchases-per-minute feature** by dividing the number of items purchased column by the minutes on platform column. 

![[Screenshot 2026-02-01 at 10.09.15.png]]

*Combining* or *modifying* existing columns to create **new features** is something that's typically decided by the machine learning team and communicated to you. 

This numerical tabular data is what most classical machine learning algorithms expect to receive as training data. 

### Feature Engineering
In machine learning, when you process a raw column or create a new feature, that's called feature *engineering*. 

![[Screenshot 2026-02-01 at 10.22.06.png]]

This process includes operations like: 
- handling missing values, 
- feature scaling, 
- converting categorical columns into numerical ones, 
- and creating new columns by combining or modifying existing ones. 

#### Handling Missing Values
Let's take a closer look at these common feature engineering operations. You'll likely encounter missing values when working with data. You should first understand ***why*** the values are missing then determine the most ***appropriate way to handle this issue***. 

**Delete missing values**
The simplest approach is to ***delete*** either the columns or the rows that contain the missing values. But you might unintentionally lose important data this way. Only delete rows or columns ***when there's no risk of losing valuable data.*** 

**Impute with summary statistics**
Another approach would be to impute the missing values with some summary statistics from the column, such as **replacing** the missing values with a **mean or median** of the column or with a value from a similar record. However, when you impute the missing values, you might introduce ***noise*** or ***bias*** to the data. 

>	*There's no single perfect way to handle missing values, and you'll usually work with the machine learning team to select the best approach that doesn't impact the performance of the machine learning system.* 


### Scale features
After dealing with any missing values, you want to scale the numerical features so that the values of each feature end up within a similar range. 

Without going into the technical details, machine learning algorithms are essentially ***optimization algorithms*** that use training data to **calculate a set of parameters** that result in the most optimal outputs. 
- If the feature values vary drastically, it might take a long time for the algorithms to converge. 

Moreover, certain machine learning algorithms are based on **distance metrics**, so their accuracies can be affected by the different ranges of the feature values. 


In our customer turn example, the range of values for the number of items purchased feature will be much smaller than the range for the customer income feature. 

![[Screenshot 2026-02-01 at 10.28.03.png]]

To scale the values for each column, you can apply **standardization** or **min-max scaling**. 

**Standartization**
With standardization, you take each value within a column, subtract the column mean, and then divide by the column standard deviation.

![[Screenshot 2026-02-01 at 10.35.16.png]]

Mathematically, for a value $x_i$ in column $X$, the standardized value $z_i$ is:
$$

z_i = \frac{x_i - \mu_X}{\sigma_X}

$$

where $\mu_X$ is the mean of column $X$ and $\sigma_X$ is the standard deviation of column $X$.

The standardized values in the column will have a mean of zero and a variance of one. 

**Min-Max Scaling**
With min-max scaling, you take each value within a column, subtract the minimum column value, and then divide by the difference between the maximum and minimum column values. 

![[Screenshot 2026-02-01 at 10.34.56.png]]

Mathematically, for a value $x_i$ in column $X$, the normalized value $n_i$ is:

$$
n_i = \frac{x_i - \min(X)}{\max(X) - \min(X)}
$$

where $\min(X)$ is the minimum value in column $X$ and $\max(X)$ is the maximum value in column $X$.

This way, the normalized values in the column will be between zero and one.

**Example:**
Let's say the customer income feature has a minimum value of $0 and a maximum value of 100,000. What values would you get if you were to apply min-max scaling to these first two values in the customer income column? 

![[Screenshot 2026-02-01 at 10.37.23.png]]

![[Screenshot 2026-02-01 at 10.40.14.png]]


### Converting Categorical Columns into Numerical

Now, what if your raw dataset contains non-numerical categorical values? For example, suppose that the account type can either be `basic`, `family`, or `platinum`, like you see here. 
![[Screenshot 2026-02-01 at 10.41.01.png]]

Since classical machine learning algorithms expect each feature to be numerical, you need to apply a pre-processing step to convert this categorical feature into a numerical one. 

#### One Hot Encoding
One way to transform this column into a numerical one is to apply a method called **one-hot encoding**. With this method, you replace the account type column with three columns, the first one representing basic, the second representing family, and the third representing platinum. 
![[Screenshot 2026-02-01 at 10.42.52.png]]
>	Since the first customer here has a family account type, I will denote the family column with a `1` and the other two columns with `0`. You do the same for all of the other rows. 

The converted columns are easy to interpret, but if the number of unique values in a column is large, it can increase the number of columns in the dataset significantly. 


#### Ordinal Encoding
Another encoding approach is to use ordinal encoding, which is useful when there is a natural ordering between the unique values of the categorical column. 

Suppose that the account types can be ordered by their subscription fee with basic being the cheapest, family falling in the middle, and platinum being the most expensive account type. Then you can replace basic with a one, family with a two, and platinum with a three. 

![[Screenshot 2026-02-01 at 10.44.15.png]]

This way, you can convert a categorical column into an numerical one without adding columns to your dataset. 

### Hashing & Embeddings
There's also other methods such as hashing where you apply a mathematical hash function to replace a category with a calculated hash value, or you can create embeddings, which is something I'll go into later this week. 

Again, you will work with the machine learning engineer to figure out what method to use depending on your use case. 


These are some of the pre-processing or feature engineering steps that you might apply to prepare data for training and machine learning algorithm. 


## Processing Tabular Data Using Scikit-Learn


