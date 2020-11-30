# yelp-reviews



https://www.eater.com/2019/2/7/18214520/instagram-influencers-yelp-elite-online-restaurant-reviews

## Cluster Configuration


## Notebook Configuration


## Set Up
* Create S3 Bucket and upload Yelp dataset
* Datasets described here: *https://www.yelp.com/dataset/documentation/main*

## Part I - Installation
* Install and import pandas, seaborn, and matplotlib as plt
* Load business dataset from the S3 bucket created earlier
`x`
* Obtain number of rows and columns in dataset
`x`
* Obtain the schema using `businessdf.printSchema()`
* Display the dataframe head as follows `x`

## Part II - Analyze Business Categories
* Itemize multiple categories per business
* Calculate total unique categories
* Identify top 20 categories and plot it


## Part III - Do Yelp reviews skew negative?
* Load user, reviews datasets from S3 bucket
* Obtain avg stars for reviews that contain written text as part of the review
* Join the reviews and business datasets'
* Calculate the skew and plot it

## Part IV - Elite Analysis
## How do reviews vary based on elite status?
* Users are grouped into 3 categories - "none" (not Elite), "early" (early Elite - prior to 2012), "late" (late Elite - 2012 and after)



