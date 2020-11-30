# yelp-reviews-analysis

[Github link to README here](https://github.com/zafirah-b/yelp-reviews/blob/main/README.md)

## Cluster Configuration
![cluster_image](assets/cluster_configuration.png)

## Notebook Configuration
![notebook_image](assets/notebook_configuration.png)

## Set Up
* Create S3 Bucket and upload Yelp dataset
* Datasets described here: *https://www.yelp.com/dataset/documentation/main*

## Part I - Installation
* Install and import pandas, seaborn, and matplotlib as plt
* Load business dataset from the S3 bucket created earlier
`businessDF = spark.read.json("s3://sta9760-zb-yelp/yelp_academic_dataset_business.json")`
* Obtain number of rows and columns in dataset
`print(f'Columns: {len(businessDF.dtypes)} | Rows: {businessDF.count():,}')`
* Obtain the schema using `businessDF.printSchema()`

## Part II - Analyze Business Categories
* Itemize multiple categories per business `all_cats = businessDF.withColumn('category',explode(split('categories',", ")))`
* Calculate total unique categories `all_cats.select('category').distinct().count()`
* Identify top 20 categories and plot it `all_cats.select('category').groupby('category').count().orderBy('count',ascending=False)`

![PartII_Plot][1]

[1]:https://github.com/zafirah-b/yelp-reviews/blob/main/part2_plot.png

*
## Part III - Do Yelp reviews skew negative?
* Load user, reviews datasets from S3 bucket
* Obtain avg stars for reviews that contain written text as part of the review
```
reviewdf.createOrReplaceTempView("review")
df = spark.sql("""
SELECT business_id, AVG(stars)
FROM review
WHERE CHAR_LENGTH(text) > 0
GROUP BY business_id
""")
```
* Join the reviews and business datasets `bus_rev = bdf.join(df, bdf.business_id == df.business_id)`
* Calculate the skew and plot it `br.withColumn("diff", (F.col("avg(stars)") - F.col("stars"))/F.col("stars"))`

![PartIII_Plot](https://github.com/zafirah-b/yelp-reviews/blob/main/part3_plot.PNG)

## Part IV - Elite Analysis
## How do reviews vary based on Elite status?
* Users are grouped into 3 categories - "none" (not Elite), "early" (early Elite - prior to 2012), "late" (late Elite - 2012 and after)
* *Background* - Yelp's cultural peak is assumed to be 2011, so we assume post-2011 is when Yelp Elite became more *corrupted*, for lack of a better word. *Source: 
https://www.eater.com/2019/2/7/18214520/instagram-influencers-yelp-elite-online-restaurant-reviews*

* Explode elite year column, create elite status groupings `userdf.withColumn('elite_yr',explode(split('elite',",")))`
```
udf.createOrReplaceTempView("user")
udfee = spark.sql("""
SELECT user_id, useful, average_stars, elite_yr
FROM user
WHERE elite_yr IN (2008, 2009, 2010, 2011)
""")
udfee = udfee.withColumn('elite_group', lit('early'))
```
* Join datasets of different groupings using `UNION`
```
udff = spark.sql("""
SELECT *
FROM user_eg

UNION ALL

SELECT *
FROM user_ne
""")
```
* aggregate dataset in spark dataframe format before converting to Pandas df for plotting to avoid errors
* plot review length by grouping

![PartIV_Plot](https://github.com/zafirah-b/yelp-reviews/blob/main/part4_plot.PNG)
