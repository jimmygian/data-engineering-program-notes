
## Processing Tabular Data Using Scikit-Learn

Video: https://www.coursera.org/learn/data-modeling-transformation-serving/lecture/EgMJu/demo-processing-tabular-data-with-scikit-learn-part-1

Let's apply some of the preprocessing steps you saw in the last video on an example data set. 

I'm going to use the open source machine learning library `scikit-learn`. This library includes several modules that you can use to **define** and **develop** supervised and unsupervised machine learning systems. 

It also provides tools for **preprocessing data**. 

In our example, I'll use two preprocessing methods, **standardization** for the numerical columns and **one hot encoding** for the categorical columns. 

For this demo, I have downloaded a customer churn dataset from the data science platform called **Kaggle**. 

![[Screenshot 2026-02-01 at 11.13.31.png]]

If you'd like to follow along with me, you can find this notebook file and the dataset CSV file, under the resource section of this video. 

https://www.kaggle.com/datasets/muhammadshahidazeem/customer-churn-dataset/data?select=customer_churn_dataset-testing-master.csv

### Plan

![[Screenshot 2026-02-04 at 16.19.25.png]]


## scikit-learn

```Python
from sklearn.model_selection import train_test_split
```

```Python
X_train, X_test, y_train, y_test = train_test_split(
											features,
											labels,
											test_size = 0.2,
											random_state = 42
										)
```

```Python
from sklearn.preprocessing import StandardScaler
```

```Python
numberical_columns = [
	'Age', 'Tenure', 'Usage Frequency', 'Support Calls',
	'Payment Delay', 'Total Spend', 'Last Interaction' 
]

X_train_numerical = X_train[numerical_columns]
```

```Python
scaler = StandardScaler()

scaler.fit(X_train_numerical)

X_train_scaled = scaler.transform(X_train_numerical)

X_train_scaled = pd.DataFrame(data=X_train_scaled,
								index=X_train.index,
								columns=numerical_columns
								)
```
