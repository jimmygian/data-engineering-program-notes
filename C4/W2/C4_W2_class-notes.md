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


## Training an ML Algorithm on Image Data

Aside from predicting sales and segmenting customers based on tabular data, you can also use machine learning algorithms to identify patterns and images. 

Some applications of this include 
- image classification like identifying the type of plant from an object, 
- object detection, such as recognizing a pedestrian in a photo of an intersection, 
- and pixel segmentation, such as detecting cancerous regions in an x-ray image. 

When preparing images to be trained by a machine learning algorithm, what you'll do will depend on the **type of algorithm being used**. Let's take a look at these different cases. 

As we discussed in the previous lesson, traditional machine learning algorithms or even regular neural networks expect data to be in **tabular form.** Consider, for example, the set of gray scale images with these pixel representations of each image. Let's say the machine learning team wants to use a set of images to train a traditional machine learning algorithm. 

Then you would need to flatten each image into a long vector of pixel values and then concatenate the vectors from all images into a tabular form like this that can be used by the machine learning algorithm. Each row in this table corresponds to the flatten pixel values of one image. 

![[Screenshot 2026-02-04 at 17.31.08.png]]

- However, this approach has limitations. When you flatten an image, you ignore the spatial structure of the image, and with that, you lose a special information that can be extracted from how one pixel is located with respect to other pixels. 

- Moreover, this approach can create a high dimensional vector of features for each image. For instance, if each image is of Size, 1,000 pixels by 1,000 pixels, then by flattening each image, you obtain a vector size one million, which would require a lot of compute and memory resources if trained on a regular neural network. 

Without going into too much of the technical details, this can also affect the performance of the machine learning algorithm, especially if the dataset contains way fewer than one million images. 



The alternative approach is a train and more advanced deep learning algorithm known as a convolutional neural network or CNN on these images. 

A convolutional neural network can work directly on images without having to first flatten them. These types of networks consist of several layers where each layer tries to identify more and more features in an image that can help with the machine learning task. 

- The first layer learns generic features that could be applied to any images, things like lines, horizontal and vertical edges, and simple textures. 
- Later layers learn features such as more complex patterns and textures, features that are more specific to the task at hand. 

![[Screenshot 2026-02-04 at 17.33.16.png]]

This is why you'll find that many machine learning teams use pre trained convolutional neural network algorithms that were trained on a large set of images, and then fine tune these models for their specific tasks by training the deeper layers using their own specific set of images. 

You might be responsible for providing the machine learning team with the set of specific images that they'll use to fine tune a convolutional neural network or even to train these networks from scratch. 

In any case, you would still need to prepare the images for the training algorithm. 


For instance, you would need to **resize the images** into a shape that is expected by the neural network algorithm, or you would need to **scale the values of the pixels** so that they are in the range of values expected by the algorithm. Another pre processing technique that you might apply is **data augmentation**. Augmenting images is a technique that you can use to create new versions of existing images by applying geometric transformations, such as flipping and rotating or other techniques, such as cropping or adjusting the brightness of the images. 

>	*Data augmentation can help increase the size and variety of the training data, which in general, can help with the performance of the machine learning algorithm.* 


You can apply these pre processing steps using open source tools like **TensorFlow**, which is a framework that's used to build and deploy deep learning models. 


### Tensorflow

TensorFlow provides pre processing functions that you can apply directly to your images. I included an optional code example in the reading item after this video that shows how you can use TensorFlow to resize, scale, and augment a set of images. That was a brief overview of the techniques you can use to pre process images. 

Aside from pre processing image files, you might also need to pre process a PDF file that contains a scan document that you need to extract textual data or tables from. Also techniques that can help you with this task. 

For more information, I included a link to a short course on pre processing unstructured data for large language model applications that shows you examples of these techniques. 

In the next section, we'll continue discussing how you can pre process unstructured data for machine learning algorithms, but now we'll focus on textual data.

#### [Optional] Code Example: Image Preprocessing Using TensorFlow

Feel free to check out [this Google Colab notebook](https://colab.research.google.com/drive/1NVBjtIfGuYoSomwBYkPg6hqyhj7HNhLS?usp=sharing#scrollTo=GB_biegiU5xU) that shows you how to perform some of the image preprocessing steps using TensorFlow, including resizing, flipping, rotating, and adjusting the brightness of an image.


## Preprocessing Textual Data for Analysis and Text Classification

As organizations increasingly generate and collect large amounts of text, including product reviews, social media posts, and customer support interactions, it's essential for them to extract insights from this textual data to make critical business decisions. 

![[Screenshot 2026-02-04 at 17.37.11.png]]

As a data engineer, you could be tasked with \
- pre-processing and 
- serving textual data 
to machine learning engineers, so you can analyze the data and use the train machine learning systems for various applications, such as: 
- sentiment analysis of product reviews, 
- classification of news articles, 
- chatbots and virtual assistants, 
- spam detection, 
- customer segmentation, 
- product recommendation, 
- and much more. 

#### Natural Signal Processing (NLP)
Natural language processing or NLP, is a **subfield of AI** that enables computers to process, understand, and generate human texts. NLP is an old field that has existed for more than 50 years, and it encompasses various text analysis techniques that have evolved over time. While classical machine learning algorithms are effective for NLP tasks like text classification, and sentiment analysis, the latest development in NLP, known as Large Language Models or **LLMs**, have significantly enhanced computers capabilities in interpreting and generating text with remarkable accuracy, and fluency. 


In this section, we'll look at some text pre-processing techniques you can use to prepare textual data for training machine learning systems for NLP tasks. 

Let's say your company wants to perform sentiment analysis to analyze customer reviews to determine if they are positive or negative. 

![[Screenshot 2026-02-04 at 17.39.20.png]]

This technique typically relies on using a pre-trained machine learning system or on training a machine learning system on your customer reviews. 

As a data engineer, in addition to collecting and storing these reviews, you can also help transform this textual data into a format that the machine learning algorithms can understand. 


The degree of pre-processing that you need to apply will depend on the type of machine-learning algorithm used. 

- Classical machine learning algorithms cannot work directly with sentences. In this case, you need to apply pre-processing techniques to first clean the text and then vectorize it. ![[Screenshot 2026-02-04 at 17.40.24.png]]

- On the other hand, LLMs can work directly with a sequence of tokens or words from your sentences without you having to first vectorize the sentences into numerical form. ![[Screenshot 2026-02-04 at 17.40.33.png]]

In any case, I think it's important that you are familiar with the various strategies for pre-processing and preparing ***textual data*** for further analysis. This is because textual data might contain typos, inconsistencies, and repetitions that you need to address in order to provide clean and high-quality data to train a machine learning model and ensure its performance, regardless of whether the machine learning algorithm is classical or advanced, like an LLM. Moreover, not all words or characters that are in your textual data might be relevant for the given NLP task. In order to reduce processing and storage costs, you would need to remove any words or characters that do not carry any relevant information for your use case. Finally, it's true that LLM is currently excelling at many NLP tasks, but training such an algorithm is still expensive and time-consuming, so they might not be the best solution for all use cases. The machine learning team you work with might still prefer using classical machine learning algorithms where simpler solutions are enough to address the use case requirements. There are also use cases where you would need to combine numerical, categorical, and textual features in a train dataset. 

![[Screenshot 2026-02-04 at 17.42.12.png]]


### Pre-processing techniques
For all these reasons, let's take a look at some ***common pre-processing techniques*** for prepping textual data. 

#### Cleaning
To clean your text, you can start by: 
- removing punctuations, 
- extra spaces, or 
- any character that doesn't add meaning to the text. 

For example, here are three original text reviews, and here is what you want to end up with after cleaning the data. 
![[Screenshot 2026-02-04 at 17.43.19.png]]


#### Normalization
After cleaning the data, you can apply normalization to standardize a text by converting it into a ***consistent format***. This might include: 
- transforming the characters to lowercase, 
- converting numbers or symbols to characters, 
- or expanding contractions or acronyms to reduce variations in spelling use for the same words. 

For instance, looking at our three example reviews, the customer in the second review wrote amount as AMT, and you could have another review that maybe capitalizes the A in amount. When you apply normalization, you resolve these inconsistencies by replacing all these instances with the lowercase spelling of the word amount. In the third review, you also see the contraction don't, which you can replace with do not. Other examples of text normalization include converting common units like Kg to kilograms or LBS to pounds or replacing acronyms like DE/D.E with data engineering. Again, you can take a look at the Python code that performs this normalization and the reading item that follows. 
![[Screenshot 2026-02-04 at 17.46.49.png]]

#### Tokenization
After you normalize the data, you should end up with text that looks like this. To prepare the text for further analysis, you then apply tokenization for each text review, meaning you split each review into ***individual units or tokens***, which are typically words but can be any meaningful unit of text, such as subwords or short sentences. 

For simplicity, I'll just split each review into a vector of words. 

Next, you can remove frequently used words such as "is", "are", "the", "for, "a" that usually don't add any meaning to the data. These words are referred to as ***stop words***. You can define your own list of stop words or use NLP libraries like SpaCy, NLTK, Gensim, and TextBlob, among others that come with a built-in set of stop words. 

Here's what the example reviews look like after I remove the stop words that belong to the set. 
![[Screenshot 2026-02-04 at 17.48.06.png]]


#### Lemmatization
Finally, you can replace each word with **its base form**, known as its lemma, using a technique called lemmatization. 



For example, the base form of getting and got is get. When you apply lemmatization to text, you replace all these variations with its lemma, which is the word get. Again, you can use NLP libraries to obtain the lemma of each word. This is what I got after applying lemmatization to all three reviews. 
![[Screenshot 2026-02-04 at 17.48.51.png]]


Depending on the needs of the machine learning engineers or data scientists, you might not need to apply all of these steps, or you might need to apply other pre-processing steps. This depends on the models your end users are planing to use and how much processing they want to perform themselves. I included links in the resource section to some additional courses that talk about other text pre-processing steps. 

Feel free to check them out to learn more about preparing textual data for training and LLM or other algorithms. In any case, after you perform the necessary steps to clean and pre-process the data, the next thing you might have to do, especially when working with machine learning algorithms that expect numerical data and tabular form is to vectorize the data. In the next section, I'll cover some common techniques for vectorization, where you convert cleaned and tokenized text into a vector of numbers.

### [Optional] Coding Example - Text Preprocessing with Python

This file contains python functions that you can use to pre-process texts by:
- removing special characters and extra spaces
- expanding contractions
- converting characters to lower case
- removing stop words
- lemmatizing



```Python
import pandas as pd
import re
import unicodedata
import spacy
import numpy as np


"""
a dictionary expanding some common contractions greatly inspired by the following list: 
https://github.com/dipanjanS/practical-machine-learning-with-python/blob/master/
notebooks/Ch07_Analyzing_Movie_Reviews_Sentiment/contractions.py 

"""
CONTRACTION_MAP = {
    "ain't": "is not",
    "aren't": "are not",
    "can't": "cannot",
    "cz": "because",
    "could've": "could have",
    "couldn't": "could not",
    "didn't": "did not",
    "doesn't": "does not",
    "don't": "do not",
    "gonna": "going to",
    "hadn't": "had not",
    "hasn't": "has not",
    "haven't": "have not",
    "he'd": "he would",
    "he'll": "he will",
    "he's": "he is",
    "how'll": "how will",
    "how's": "how is",
    "I'd": "I would",
    "I'll": "I will",
    "I'm": "I am",
    "I've": "I have",
    "i'd": "i would",
    "i'll": "i will",
    "i'm": "i am",
    "i've": "i have",
    "isn't": "is not",
    "it'd": "it would",
    "it'll": "it will",
    "it's": "it is",
    "let's": "let us",
    "ma'am": "madam",
    "mayn't": "may not",
    "might've": "might have",
    "must've": "must have",
    "mustn't": "must not",
    "needn't": "need not",
    "o'clock": "of the clock",
    "oughtn't": "ought not",
    "oughtn't've": "ought not have",
    "shan't": "shall not",
    "sha'n't": "shall not",
    "she'd": "she would",
    "she'd've": "she would have",
    "she'll": "she will",
    "she's": "she is",
    "should've": "should have",
    "shouldn't": "should not",
    "shouldn't've": "should not have",
    "so've": "so have",
    "so's": "so as",
    "that'd": "that would",
    "that's": "that is",
    "there'd": "there would",
    "there'd've": "there would have",
    "there's": "there is",
    "they'd": "they would",
    "they'll": "they will",
    "they'll've": "they will have",
    "they're": "they are",
    "they've": "they have",
    "wanna": "want to",
    "wasn't": "was not",
    "we'd": "we would",
    "we'll": "we will",
    "we'll've": "we will have",
    "we're": "we are",
    "we've": "we have",
    "weren't": "were not",
    "what'll": "what will",
    "what'll've": "what will have",
    "what're": "what are",
    "what's": "what is",
    "what've": "what have",
    "when's": "when is",
    "when've": "when have",
    "where'd": "where did",
    "where's": "where is",
    "where've": "where have",
    "who'll": "who will",
    "who's": "who is",
    "who've": "who have",
    "why's": "why is",
    "why've": "why have",
    "will've": "will have",
    "won't": "will not",
    "won't've": "will not have",
    "would've": "would have",
    "wouldn't": "would not",
    "wouldn't've": "would not have",
    "y'all": "you all",
    "y'all're": "you all are",
    "you'd": "you would",
    "you'd've": "you would have",
    "you'll": "you will",
    "you'll've": "you will have",
    "you're": "you are",
    "you've": "you have"
}



def remove_special_char(text, special_characters = ['~','@', '#', '$', '%','^', '&', '*'], numeric = False):
    """
    This function cleans text from any special characters.
    You can add additional input as a list of special characters you want to remove.
    Or you can decide on removing all types of characters except letters and numbers.
    You can also add an additional boolean input that indicates if the user wants to 
    remove the numbers as well.

    Parameters
    -----------
    text: str

    Returns
    ---------
    filtered_text: str

    Examples
    --------
    >>> remove_special_char('processing$##')
    'processing'

    """
    pattern = '[' + special_characters[0]
    for char in special_characters:
        pattern = pattern + '|' + char
    if (numeric):
        pattern = pattern + '|'+ '0-9'
    pattern = pattern + ']'
    #remove them
    filtered_text = re.sub(pattern, r'', text)
    return filtered_text


def remove_accents(text):
    """
    This function removes accent from text

    Parameters
    ----------
    text: str

    Returns
    --------
    filtered_text: str
        The text without the accents

    Examples
    --------
    >>> remove_special_char('déjà vu')
    'deja vu'

    """
    filtered_text = unicodedata.normalize(
        'NFKD', text).encode('ascii', 'ignore').decode('utf8')

    return filtered_text


def expand_contractions(text):
    """
    This function expands the contractions in text

    Parameters
    ----------
    text: str

    Returns
    --------
    filtered_text: str
        The text with expanded contractions

    Examples
    --------
    >>> remove_special_char('It couldn't be better')
    'It could not be better'

    """
    text = " ".join([CONTRACTION_MAP[word] if word in CONTRACTION_MAP else word for word in text.split()])
    return text


def remove_stopwords_punctuation(text, lang_model, lemmatizing=False, stop_words=False):
    """
    This function uses spacy to remove stop_words and punctuation marks.
    It can also replace words with their lemma.

    Parameters
    -----------
    text: str
    lang_model: spacy Language object
    lemmatizing: boolean
    stop_words: boolean
        True if it is desired to remove stop_words

    Returns
    ---------
    filtered_text:str

    Examples
    --------
    >>> lang_model = spacy.load("en_core_web_sm")
    >>> remove_stopwords_punctuation('Those were amazing days!!!', lang_model, lemmatizing=False, stop_words=False)
    'Those were amazing days'

    >>> remove_stopwords_punctuation('Those were amazing days!!!', lang_model, lemmatizing=True, stop_words=False)
    'Those be amazing day'

    >>> remove_stopwords_punctuation('Those were amazing days!!!', lang_model, lemmatizing=True, stop_words=True)
    'amazing day'

    """

    doc_text = lang_model(text)
   
    if lemmatizing:
        st= " ".join([token.lemma_ for token in doc_text if not(token.is_punct)])
    else:
        st= " ".join([token.text for token in doc_text if not(token.is_punct)])
    return st

  

    return filtered_text


def preprocess_text(text, nlp, special_characters = ['~','@', '#', '$', '%', '^', '&', '*'], numeric = False, lemmatizing=False):
    """
    This function pre-processes the text.

    Parameters
    -----------
    text: str
    lang_model: spacy Language object
    lemmatizing: boolean
    stop_words: boolean
        True if it is desired to remove stop_words

    Returns
    --------
    filtered_text:str


    Examples
    --------
    >>> lang_model = spacy.load("en_core_web_sm")
    >>> preprocess_text("\n\n\nHey that's a $$great news!!", lang_model, lemmatizing=True, stop_words=False)
    'hey that be a great news'

    >>> preprocess_text("\n\n\nHey that's a $$great news!!", lang_model, lemmatizing=True, stop_words=True)
    'hey great news'

    """

    # remove special characters
    text =  remove_special_char(text, special_characters, numeric)

    # convert text to lower case (you can use lower() function)
    # remove extra spaces with strip() function
    text = text.lower().strip()

    # remove accents
    text =  remove_accents(text)

    # expand contractions
    text = expand_contractions(text)

    # remove stop_words amd punctuations
    filtered_text =  remove_stopwords_punctuation(text, nlp, lemmatizing)
   

    return filtered_text

```


## Text Vectorization and Embedding

Now that we've covered the common preprocessing steps for textual data in the previous video, let's go over how you would turn your preprocessed text data into vectors. 

The traditional techniques to **vectorize text** consists of methods like 
- bag of words 
- and term-frequency inverse document frequency, or TF-IDF. 

![[Screenshot 2026-02-04 at 17.56.19.png]]

These methods assign a number to each word in a text based on its frequency of occurrence. 

Here's the example you saw in the previous section. I'll refer to each customer review as a **document**, and the three reviews collectively make up what is known as the **corpus**.
![[Screenshot 2026-02-04 at 17.57.12.png]]

>	*In a real example, the **corpus** would include all the customer reviews and will be much larger.*

Let's first extract the vocabulary of the corpus, meaning, the list of unique words that make up this collection. 
![[Screenshot 2026-02-04 at 17.59.08.png]]

Then you can convert each document, meaning each customer review, into a **vector** that has the same length as this list of vocabulary in the bag of words method. 

Each entry in the vector reflects the number of times that corresponding word appeared in the document. So in the first review, the words this wonderful price amount you and get all appeared once, and all the other words in the vocabulary appeared zero times. 

![[Screenshot 2026-02-04 at 18.00.02.png]]

You can use scikit learn to quickly vectorize your text using the bag of words method. Feel free to check out the specific code in the reading item that follows this video. 

However, the bag of words method only takes into account **how frequently each word in the vocabulary appears within each document.** In a large text corpus, some words might appear very frequently across all documents, but carry very little meaningful information about the actual contents of the document. 

For example, if you're looking at a large dataset of product reviews, the words **purchase** and **buy** might appear many times across many documents. If you were to feed this frequency count data directly to a document classifier that performs sentiment analysis on the reviews, these high frequency but low meaning words might overshadow the frequencies of more rare but more significant words like **break** or **exceptional**. 

#### TF-IDF Method

With the TF-IDF method, you can account for the ***weight*** and rarity of each word or term when vectorizing the documents. 

- For each word, you will consider its term frequency, which represents the number of times a term occurred in a document divided by the total number of words in that document. 

- And you will also consider the inverse document frequency, which indicates how common or rare that word is in the entire corpus. The closer this number is to zero, the more common it is, and the closer it is to one, the more rare it is. 

In practice, you would use `scikit-learn` or another library to help you vectorize your text using the TF IDF method. So I won't go into details of the calculation here, but if you're curious about these details, feel free to check out the reading item after this section. 

So here's the TF-IDF vectorization of these text reviews. 
![[Screenshot 2026-02-04 at 18.03.26.png]]


The traditional way of vectorizing a text is considered a simple, easy to understand, and interpretable method of vectorizing texts, and you might choose to use these methods for quick experimentation. These simple vectorization methods are useful when you have a smaller data set, and they can perform very well on document classification. Where the presence of specific key terms strongly indicate the class of the document. However, it gives no attention to the meaning of the words or the sentences in the document, and you can end up with a high dimensional vector with very sparse values. 


If you want to better capture the semantic meaning of a word, you can replace the word by a vector called a **"word embedding"**. These vectors are generated in such a way that if two words have similar meaning, they're mapped to vectors that are geometrically closer to each other. So, for example, the embedding vector of the word useful should be closer to the embedding vector of the word helpful than that of the word tree. You can generate embedding vectors using popular word embedding algorithms such as word2vec and glove. Which have been trained to learn the embeddings of words from their co-occurrences and very large collections of text. 
![[Screenshot 2026-02-04 at 18.05.36.png]]

The idea is that if two words frequently appear together, they should share similar contexts and those have similar meaning. 

Let's go back to our text reviews, here you want to represent each review, and not just each word, by one vector. 

You could just compute the word embeddings for each word in the sentence, then add a bullet of vectors to get a vector that represents the sentence. But this method doesn't take into account the position of the words in the sentence. 

For example, a man ate a snake has a different meaning than a snake ate a man. But if you add the word embeddings of each word in these two sentences, you'll get the same vector. This is where sentence embeddings can be helpful. 
![[Screenshot 2026-02-04 at 18.06.15.png]]

A sentence embedding is a vector that reflects the semantic meaning of the entire sentence. It takes into account the position of the words in a sentence and the meaning of each word. 

Similar to word embeddings. If two sentences have similar meaning, their embedding vectors would be close to each other. Moreover, a sentence embedding is a vector that has a lower dimension than the dimension of the vector generated by a TF-IDF. 


You can obtain these sentence embeddings using NLP models that are pre trained on large datasets. Several open source and closed source embedding models exist, but these state of the art models are based on large language models. For example, sentence transformers is a Python framework that enables you to access open source embedding models. On the other hand, LLM research companies such as OpenAI, Anthropic, and Google offer closed source API embeddings. 

![[Screenshot 2026-02-04 at 18.07.43.png]]

Let's apply sentence embeddings to the text reviews. Here, I'm using the sentence transformers Python package. First, I need to instantiate an instance of the sentence transformer and specify an open source LLM model. 

>	In the documentation page you can find a list of open source models that you can use with sentence transformer. 

I'm going to use a model called all-MiniLM-L6- v2, which you'll see again in the lab exercise later on. Then I'll take the text reviews that I've cleaned and normalized and pass it to the embedding model. For each review, the model returns an embedding vector that has 384 entries. 

Let's see how similar these vectors are to each other. I'm choosing to use the cosine similarity metric here, but you can choose other distance metrics as well. The cosine similarity between the first review and the second review is 0.51, and the cosine similarity between the first review and the third review is 0.14. Meaning that the first review is closer in meaning to the second review than it is to the third. This makes sense since both the first and second reviews are about the amount of product the customer received. Now, what can you do with these embeddings? These embeddings can serve as features to train a machine learning algorithm for product recommendation, or they can be used for clustering or performing similarity search. 
![[Screenshot 2026-02-04 at 18.08.54.png]]


In the second lab of this week, you'll be provided with the text review data set you saw in the previous course. You'll apply embeddings to this dataset and process the tabular metadata using similar techniques that you used in the first lab, and after that join me for a summary of this week.

#### [Optional] Coding Example - Vectorizing Text with scikit-learn

This notebook file shows how you use scikit learn to vectorize a list of texts (corpus) using the bag of words and TF-IDF models.


**Optional Courses:**

Feel free to check out these courses if you're interested in learning more about machine learning topics

- [Machine Learning Specialization](https://www.deeplearning.ai/courses/machine-learning-specialization/)
- [Deep Learning Specialization](https://www.deeplearning.ai/courses/deep-learning-specialization/)
    - In [this video](https://www.coursera.org/learn/deep-neural-network/lecture/lXv6U/normalizing-inputs), Andrew explains the best practice of applying the same preprocessing steps and computed statistics from the training set to the testing set
    - [Course 4](https://www.coursera.org/learn/convolutional-neural-networks?specialization=deep-learning) is all about Convolutional Neural Networks
- [Natural Language Processing Specialization](https://www.deeplearning.ai/courses/natural-language-processing-specialization/)
- [Machine Learning in Production Specialization](https://www.deeplearning.ai/courses/machine-learning-in-production/)
- [Generative AI with LLMs Course](https://www.deeplearning.ai/courses/generative-ai-with-llms/)
- [Preprocessing Unstructured Data for LLM Applications](https://www.deeplearning.ai/short-courses/preprocessing-unstructured-data-for-llm-applications/)
- [Understanding and applying text Embeddings](https://www.deeplearning.ai/short-courses/google-cloud-vertex-ai/)
- [Large Language Models with Semantic Search](https://www.deeplearning.ai/short-courses/large-language-models-semantic-search/)
- [Serverless LLM apps with Amazon Bedrock](https://www.deeplearning.ai/short-courses/serverless-llm-apps-amazon-bedrock/)
- [Pretraining LLMs](https://www.deeplearning.ai/short-courses/pretraining-llms/)

- Includes additional preprocessing steps to prepare your textual data for training an LLM


**Optional reading and reference material:**

- Pandas tutorials:
    - [Kaggle Pandas tutorials](https://www.kaggle.com/learn/pandas).
    - [W3 School Pandas tutorials](https://www.w3schools.com/python/pandas/default.asp)

- Sci-kit learn user guide:
    - [Preprocessing data](https://scikit-learn.org/stable/modules/preprocessing.html)
    - [Imputation of missing values](https://scikit-learn.org/stable/modules/impute.html)
    - [Text extraction](https://scikit-learn.org/stable/modules/feature_extraction.html#text-feature-extraction)

- Resources for the Demo on Processing Tabular Data with Scikit-Learn:
    - [Customer churn dataset from Kaggle](https://www.kaggle.com/datasets/muhammadshahidazeem/customer-churn-dataset)
- [Medium article](https://medium.com/@lars.chr.wiik/best-embedding-model-openai-cohere-google-e5-bge-931bfa1962dc) - Comparing between the embedding models of openAI, cohere, google and others

- Embedding model: [all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2)
- [SentenceTransformers documentation](https://www.sbert.net/index.html)
- [SeattleDataGuy article](https://seattledataguy.substack.com/p/data-engineering-vs-machine-learning) - Date Engineering vs Machine Learning pipelines
- [Cohere embeddings](https://docs.cohere.com/docs/embeddings)
    - [Convert text into vectors](https://docs.cohere.com/docs/how-to-convert-text-into-vectors)
    - [Word and sentence embeddings](https://cohere.com/blog/sentence-word-embeddings?_gl=1*r938ho*_ga*MTgxNTg3NDExMS4xNzE3ODk3NjU3*_ga_CRGS116RZS*MTcxODI0MTg3Mi44LjEuMTcxODI0NTI5OS4xNS4wLjA)
- [Word2vec tutorial](https://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/) (article on [Word2vec properties](https://p.migdal.pl/blog/2017/01/king-man-woman-queen-why))
- [Medium article](https://medium.com/deeper-learning/glossary-of-deep-learning-word-embedding-f90c3cec34ca) - Word embedding
- [Medium article](https://medium.datadriveninvestor.com/from-word-embeddings-to-sentence-embeddings-part-1-3-7ba9a715e917) - From word to sentence embedding