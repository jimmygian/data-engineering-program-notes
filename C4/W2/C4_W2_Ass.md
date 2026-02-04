
# Modeling and Transforming Text Data for ML

In this assignment, you will work with the Amazon Reviews dataset to generate a training dataset for an ML use case. The purpose of this lab is to apply feature engineering to process the numerical and categorical features of the raw JSON files, and to transform the text reviews and product information into text embeddings. You will finally store the generated features in a provided Postgres database, which will serve as a vector database.

## 1 - Introduction

Imagine you are employed as a Data Engineer at a prominent e-commerce retailer. The Machine Learning (ML) team has initiated a new research project and obtained a dataset comprising Amazon Reviews for different products. They have requested you to build a pipeline to refine the raw JSON data into structured datasets suitable for training ML models. To start the development, they have provided you with two sample files from the original dataset to validate the logic and develop an initial pipeline prototype within this notebook. Additionally, the Data Analytics team has requested that you generate embeddings from the reviews and product texts and then store the vectors in a vector database for future analysis; for this purpose, the ML team has enabled an API that runs a text embedder ML model for you to consume and generate the vectors.

The main requirements regarding the datasets are the following:

1. Process the textual, categorical and numerical features.
2. Generate text embeddings based on the review text and product information (provided from the product description or the product title)
3. Divide the original data into three tables:
   - Reviews embeddings dataset: it must contain the reviewer ID, product ASIN, the review text and the corresponding embedding vector.
   - Product embeddings dataset: it must contain the product ASIN, the product information and the corresponding embedding vector.
   - Review metadata dataset: it must contain the remaining features related to the reviews and products for each review from the original data.
1. Store the new features in the provisioned RDS Postgres instance.


<a id='2'></a>
## 2 - Source Data Exploration

The dataset is comprised of two compressed JSON files, one with the reviews and one with the metadata of the reviewed products. You've already worked with this dataset in C3W2 lab. Here is an example of a review:

```json
{
  "reviewerID": "A2SUAM1J3GNN3B",
  "asin": "0000013714",
  "reviewerName": "J. McDonald",
  "helpful": [2, 3],
  "reviewText": "I bought this for my husband who plays the piano.  He is having a wonderful time playing these old hymns.  The music  is at times hard to read because we think the book was published for singing from more than playing from.  Great purchase though!",
  "overall": 5.0,
  "summary": "Heavenly Highway Hymns",
  "unixReviewTime": 1252800000,
  "reviewTime": "09 13, 2009"
}
```

Here is the description of the fields:

- `reviewerID` - ID of the reviewer, e.g. A2SUAM1J3GNN3B
- `asin` - ID of the product, e.g. 0000013714
- `reviewerName` - name of the reviewer
- `helpful` - helpfulness rating of the review, e.g. 2/3
- `reviewText` - text of the review
- `overall` - rating of the product
- `summary` - summary of the review
- `unixReviewTime` - time of the review (unix time)
- `reviewTime` - time of the review (raw)

And this is an example of the review metadata:
```json
{
  "asin": "0641843224",
  "description": "Set your phasers to stun and prepare for a warp speed ride through the most memorable vocabulary from the sci-fi/fantasy genre.",
  "title": "McNeill Designs YBS Sci-fi/Fantasy Add-on Deck", 
  "price": 5.19,  
  "imUrl": "http://ecx.images-amazon.com/images/I/418t9AN9hiL._SY300_.jpg", 
  "related": 
  {
    "also_bought": ["B000EVLZ9U", "0641843208", "0641843216", "0641843267", "1450751210", "0641843232", "B00ALQFYGI", "B004G7B3NQ", "B002PDM288", "B009ZNJZV8", "B009YG928W", "B0063NC3N0"], 
    "also_viewed": ["B000EVLZ9U", "1450751210", "0641843208", "0641843267", "0641843232", "0641843216", "B003EIK136", "B004G7B3NQ", "B003N2Q5JC"], 
    "bought_together": ["B000EVLZ9U"]
  },
  "salesRank": {"Toys & Games": 154868}, 
  "brand": "McNeill Designs", 
  "categories": [["Toys & Games", "Games", "Card Games"]]
}
```

With the following fields:

- `asin` - ID of the product, e.g. 0000031852
- `description` - Description of the product
- `title` - name of the product
- `price` - price in US dollars (at time of crawl)
- `imUrl` - url of the product image
- `related` - related products (`also_bought`, `also_viewed`, `bought_together`)
- `salesRank` - sales rank information
- `brand` - brand name
- `categories` - list of categories the product belongs to