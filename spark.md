# Spark (PySpark)
> Notes taken from completing [DataCamp's Introduction to PySpark Course](https://www.datacamp.com/courses/introduction-to-pyspark).

Spark is a platform for cluster computing. Spark lets you spread data and computations over clusters with multiple nodes (think of each node as a separate computer). Splitting up your data makes it easier to work with very large datasets because each node only works with a small amount of data.

## Contents
- [Spark (PySpark)](#spark-pyspark)
  - [Contents](#contents)
  - [Resources](#resources)
  - [PySpark Basics](#pyspark-basics)
    - [Connecting to a cluster](#connecting-to-a-cluster)
    - [Spark data structure](#spark-data-structure)
    - [SparkSession](#sparksession)
    - [Spark SQL](#spark-sql)
    - [Pandafy a Spark DataFrame](#pandafy-a-spark-dataframe)
    - [Sparkify a pandas DataFrame](#sparkify-a-pandas-dataframe)
    - [Spark data sources](#spark-data-sources)
  - [Manipulating Data](#manipulating-data)
    - [Creating columns](#creating-columns)
    - [Filtering data](#filtering-data)
    - [Selecting](#selecting)
    - [Aggregating](#aggregating)
    - [Grouping and aggregating](#grouping-and-aggregating)
    - [Joining](#joining)
  - [Machine Learning Pipelines](#machine-learning-pipelines)
    - [Data types](#data-types)
    - [One-hot vectors](#one-hot-vectors)
    - [Assemble a vector](#assemble-a-vector)
    - [Creating a pipeline](#creating-a-pipeline)
    - [Test vs train](#test-vs-train)
    - [Data transformation](#data-transformation)
    - [Split the data](#split-the-data)
  - [Model Tuning and Selection](#model-tuning-and-selection)
    - [Creating the modeler](#creating-the-modeler)
    - [Cross validation](#cross-validation)
    - [Create the evaluator](#create-the-evaluator)
    - [Make a grid](#make-a-grid)
    - [Make the validator](#make-the-validator)
    - [Fit the model(s)](#fit-the-models)
    - [Evaluating binary classifiers](#evaluating-binary-classifiers)
  - [Conclusion](#conclusion)

## Resources
- [Spark Documentation](https://spark.apache.org/docs/latest/api/python/index.html)
- [DataCamp PySpark SQL Basics Cheat Sheet](http://datacamp-community.s3.amazonaws.com/65076e3c-9df1-40d5-a0c2-36294d9a3ca9)
- [DataCamp PySpark RDD Basics Cheat Sheet](http://datacamp-community.s3.amazonaws.com/4d91fcbc-820d-4ae2-891b-f7a436ebefd4)

## PySpark Basics
To begin using Spark you need to connect to a cluster. Usually, the cluster is hosted on a remote machine that is connected to all other nodes. There will be one `master` computer that manages splitting up the data and computations. The `master` delegates the data and work to the other nodes called `slaves` that perform the computations and return the results to the `master`.

### Connecting to a cluster
Creating the connection is as simple as creating an instance of the `SparkContext` class. The class constructor takes a few optional arguments that allow you to specify the attributes of the cluster you're connecting to. You can create an object holding these attributes for the arguments using the `SparkConf()` constructor.


### Spark data structure
Spark's core data structure is the `Resilient Distributed Dataset (RDD)`. The `RDD` is a low level object that Spark uses to split data across multiple nodes in the cluster.

RDDs are difficult to work with directly, hence you can also use the `Spark DataFrame` which is an abstraction built on top of RDDs. The `Spark DataFrame` was designed to behave a lot like a SQL table (variables in the columns and observations in the rows), are easier to understand and also more optimized for complicated operations than RDDs.

To start working with Spark DataFrames, you have to create a `SparkSession` object from your `SparkContext`. 
- `SparkContext` = connection to cluster
- `SparkSession` = interface with that connection

### SparkSession
Creating multiple `SparkSession` or `SparkContext` may cause issues, therefore best practices dictate to use the `.getOrCreate()` method that returns an existing `SparkSession` or creates a new one otherwise.
```
# import SparkSession
from pyspark.sql import SparkSession

# gets existing SparkSession object if available or creates a new one
spark = SparkSession.builder.getOrCreate()
```

`SparkSession` has an attribute `catalog` that lists all the data inside the cluster. You can use the `.listTables()` method to return the names of all tables in your cluster as a list.  
```
print(spark.catalog.listTables())
```

### Spark SQL
An advantage of the Spark DataFrame interface is that you can run SQL queries on the tables in your Spark cluster using `.sql()`. In this Spark cluster, we have access to the `flights` table that contains a row for every flight that left Portland International Airport (PDX)
 or Seattle-Tacoma International Airport (SEA) in 2014 and 2015.

```
# sql query
query = "FROM flights SELECT * LIMIT 10"

# get first 10 rows of flights
flights10 = spark.sql(query)

# display the results
flights10.show()
```

### Pandafy a Spark DataFrame
Sometimes it makes sense to take a smaller table and work with it locally using a tool like `pandas` (perhaps after running a query on your huge dataset and aggregated it down to something more manageable). You can do that easily using the `toPandas()` method on a Spark DataFrame.

```
# query
query = ""SELECT origin, dest, COUNT(*) as N FROM flights GROUP BY origin, dest"

# run the query
flight_counts = spark.sql(query)

# convert Spark DataFrame into pandas DataFrame
pd_counts = flight_counts.toPandas()
```

### Sparkify a pandas DataFrame
You can also do the reverse using `.createDataFrame()`. However, the output of this method is stored locally and not in the `SparkSession` catalog. Hence you can only use Spark DataFrame methods on it but not access the data in other contexts such as using `.sql()`

Use the `.createTempView()` or `createOrReplaceTempView()` method to create and register a temporary table in the catalog. As this table is temporary, it can only be accessed from within the SparkSession used to create this Spark DataFrame.

```
import numpy as np
import pandas as pd

# create temp pandas DataFrame
pd_temp = pd.DataFrame(np.random.random(10))

# create temp Spark DataFrame from pd_temp
spark_temp = spark.createDataFrame(pd_temp)

# examine the tables in the catalog
# you will notice that spark_temp is not in the catalog
print(spark.catalog.listTables())

# add spark_temp to the catalog
spark_temp.createOrReplaceTempView("temp)

# examine the tables in the catalog again
# this time spark_temp is in the catalog as "temp"
print(spark.catalog.listTables())
```

### Spark data sources
It may also be easier to read the data directly into Spark, bypassing pandas. The `SparkSession` has a `.read` attribute that has several methods for reading different data sources directly into Spark DataFrames. 

```
# filepath
file_path = "/usr/local/share/datasets/airports.csv"

# read the airports data
airports = spark.read.csv("file_path, header=True)

# show the data
airports.show()
```

## Manipulating Data
Spark's `DataFrame` class has many methods to perform common data operations.

### Creating columns
You can create new columns in Spark using `.withColumn()`.  The new column to be added must also be an object of class `Column` which can be extracted from your DataFrame using `df.colName`. However, as Spark DataFrames are *immutable*, columns **cannot** be updated in place.

These methods return a new DataFrame and you must reassign the newly returned DataFrame in order to overwrite the original DataFrame.

```
# create the flights DataFrame
flights = spark.table("flights)

# add new duration_hrs column which is the flight time in hours
flights = flights.withColumn("duration_hrs", flights.air_time / 60)
```

### Filtering data
The `.filter()` method is the Spark counterpart to SQL's `WHERE` clause. It takes either a Spark `Column` of boolean values or the `WHERE` clause of a SQL expression as a string. For example, the two expressions below will produce the same output.

```
# filter flights with a boolean column
flights.filter(flights.air_time > 120).show()

# filter flights with a SQL expression
flights.filter("air_time > 120").show()
```

### Selecting
The `.select()` method is Spark's variant of SQL's `SELECT` clause. It takes in multiple arguments - one for each column you want to select. The arguments can either be the column name as a string or a column object. 

```
# select the first set of columns
selected1 = flights.select("tailnum", "origin", "dest")

# select the second set of columns
temp = flights.select(flights.origin, flights.dest, flights.carrier)

# define filters
filterA = flights.origin == "SEA"
filterB = flights.dest == "PDX"

# filter the data, first by filterA then by filterB
selected2 = temp.filter(filterA).filter(filterB)
```

If you use a column object, you can perform operations such as addition or subtraction on the column to change the data just like in `.withColumn()`. The difference between `.select()` and `.withColumn()` is that `.select()` returns only the specified columns whereas `.withColumn()` returns all the columns of the DataFrame plus the new column you defined.

It's generally a good idea to drop the unnecessary columns using `.select()` at the beginning of an operation so that you're not dragging around extra data when doing data wrangling. 

You can use the `.alias()` method to rename columns you're selecting. Using `.selectExpr()` also allows you to do the same with a SQL expression as a string.

```
# define avg_speed
avg_speed = (flights.distance / (flights.air_time / 60)).alias("avg_speed)

# select the correct columns
speed1 = flights.select("origin", "dest", "tail_num", avg_speed)

# create the same table using a SQL expression
speed2 = flights.selectExpr("origin", "dest", "tail_num", "distance / (air_time / 60) as avg_speed")
```

### Aggregating
The common aggregation methods like `.min()`, `.max()` and `.count()` are `GroupedData` methods. These are created by calling the `.groupBy()` DataFrame method.

```
# find shortest flight from PDX in terms of distance
flights.filter(flights.origin == "PDX).groupBy().min("distance").show()

# find longest flight from SEA in terms of duration
flights.filter(flights.origin == "SEA").groupBy().max("air_time).show()

# find average duration of Delta flights
flights.filter(flights.carrier == "DL").filter(flights.origin == "SEA").groupBy().avg("air_time).show()

# find total hours in the air
flights.withColumn("duration_hrs", flights.air_time / 60).groupBy().sum("duration_hrs").show()
```

### Grouping and aggregating
Previously, you used the `.groupBy()` method without any arguments. Passing in the name of one of more columns as arguments into the `.groupBy()` method makes the aggregation behave like the `GROUP BY` clause in SQL.

```
# group by tailnum
by_plane = flights.groupBy("tailnum")

# number of flights each plane made
by_plane.count().show()

# group by origin
by_origin = flights.groupBy("origin")

# average duration of flights from PDX and SEA
by_origin.avg("air_time").show()
```

In addition, the `.agg()` method lets you pass an aggregate column expression that uses any of the aggregate functions from the `pyspark.sql.functions` module.

```
# import pyspark.sql.functions 
import pyspark.sql.functions as F

# group by month and dest
by_month_dest = flights.groupBy("month", "dest")

# average departure delay by month and destination
by_month_dest.avg("dep_delay").show()

# get standard deviation
by_month_dest.agg(F.stddev("dep_delay")).show()
```

### Joining
Joins are another very common data operation and as they are a whole topic on their own, we will only look at simple joins here. A join combines two different tables along a shared column called the *key* such as the `tailnum` and `carrier` columns from the `flights` table.

In PySpark, joins are performed using the DataFrame `.join()` method which takes in three arguments. 

1. The second DataFrame to join with the first.
2. `on` specifies the name of the key column(s) as a string.
3. `how` specifies the kind of join to perform.

```
# examine the data
print(airports.show())

# rename the faa column
airports = airports.withColumnRenamed("faa", "dest")

# join the DataFrames
flights_with_airports = flights.join("airports, on="dest", how="leftouter")

# examine the data again
print(flights_with_airports.show())
```

## Machine Learning Pipelines
At the core of the `pyspark.ml` module are the `Transformer` and `Estimator` classes.

`Transformer` classes have a `.transform()` method that takes a DataFrame and returns a new DataFrame, usually the original one with a new column appended. Some examples are the `Bucketizer` to create discrete bins from a continuous feature or `PCA` to reduce the dimensionality of your data set.

`Estimator` classes all implement a `.fit()` method that takes a DataFrame and returns a model object. Some examples are the `StringIndexerModel` for including categorical data saved as strings in your models or the `RandomForestModel` that uses the random forest algorithm for classification or regression.

Let's build a model that predicts whether or not a flight will be delayed based on the flights data we have been working with! The model will also include information about the planes that flew the route, hence we start by joining the `flights` and `planes` tables.

```
# rename year column
planes = planes.withColumnRenamed("year", "plane_year")

# join the DataFrames
model_data = flights.join(planes, on="tailnum", how="leftouter")
```

### Data types
**Important**: Spark only handles numeric data (integers or doubles).

Spark can guess what kind of information each column holds when importing the data. However, Spark isn't always correct and we can remedy this using the `.cast()` method in combination with the `.withColumn()` method. Remember that `.cast()` works on *columns* while `.withColumn()` works on *DataFrames*.

```
# cast columns as integers
model_data = model_data.withColumn("arr_delay", model_data.arr_delay.cast("integer"))
model_data = model_data.withColumn("air_time", model_data.air_time.cast("integer"))
model_data = model_data.withColumn("month", model_data.month.cast("integer"))
model_data = model_data.withColumn("plane_year", model_data.plane_year.cast("integer"))
```

You have successfully casted the `plane_year` column to an integer. However, the model will use the planes' *age* which is slightly different.

```
# create the column plane_age
model_data = model_data.withColumn("plane_age", model_data.year - model_data.plane_year)
```

As you are modeling a yes or no question (is the flight late?), you will need to create a boolean column that shows if the flight was late or not using the arrival delay in minutes for each flight column. 

```
# Create is_late
model_data = model_data.withColumn("is_late", model_data.arr_delay > 0)

# Convert to an integer
model_data = model_data.withColumn("label", model_data.is_late.cast("integer"))

# Remove missing values
model_data = model_data.filter("arr_delay is not NULL and dep_delay is not NULL and air_time is not NULL and plane_year is not NULL")
```

Here, `label` is the default name for the response variable in spark's machine learning routines.

### One-hot vectors
As Spark requires numeric data for modeling, you can use the `pyspark.ml.features` submodule to create `one-hot vectors` to convert categorical attributes into numeric attributes.

A `one-hot vector` is a way of representing a categorical feature where every observation has a vector in which all elements are zero for at most one element, which has a value of (1). To encode a categorical feature:

1. Create a `StringIndexer`.
    - Members of this class are also of the `Estimator` class.
    - The `Estimator` takes in a DataFrame with a column of strings and maps each unique string to a number.
    - The `Estimator` then returns a `Transformer` that takes a DataFrame, attaches the mapping to it as metadata, then returns a new DataFrame with a numeric column corresponding to the string column.
2. Encode this numeric column as a one-hot vector using a `OneHotEncoder`.
    - This works exactly the same way as the StringIndexer by creating an `Estimator` and then a `Transformer`.
    - The result is a column that encodes the categorical feature as a vector that is suitable for machine learning routines.

In short, create a `StringIndexer`, a `OneHotEncoder`, then let the `Pipeline` take care of the rest.

Here you will encode the `carrier` and `dest` columns where the `inputCol` is the name of the column to index or encode and the `outputCol` is the name of the new column that the `Transformer` should create.
```
# create a StringIndexer
carr_indexer = StringIndexer(inputCol="carrier", outputCol="carrier_index")
dest_indexer = StringIndexer(inputCol="dest", outputCol="dest_index")

# create a OneHotEncoder
carr_encoder = OneHotEncoder(inputCol="carrier_index", outputCol="carrier_fact")
dest_encoder = OneHotEncoder(inputCol="dest_index", outputCol="dest_fact")
```

### Assemble a vector
The last step in the `Pipeline` is to combine all of the columns containing our features into a single column. 

- This can be done by storing each of the values from a column as an entry in a vector.
- From the model's point of view, every observation is a vector that contains all of the information about it and a label that tells the modeler what value the observation corresponds to.
- All spark modeling routines expect the data to be in this form.

As such, the class `VectorAssembler` from `pyspark.ml.feature` submodule is a `Transformer` that takes all of the columns you specify and combines them into a new vector column.

```
# make a VectorAssembler
vec_assembler = VectorAssembler(
    inputCols=["month", "air_time", "carrier_fact", "dest_fact", "plane_age"], 
    outputCol="features")
```

### Creating a pipeline
`Pipeline` is a class in the `pyspark.ml` module that combines all the `Estimators` and `Transformers` that you've already created. This allows you to reuse the same modeling process over and over again by wrapping it up in one simple object.

```
# import Pipeline
from pyspark.ml import Pipeline

# make the pipeline
flights_pipe = Pipeline(
    stages=[dest_indexer, dest_encoder, carr_indexer, carr_encoder, vec_assembler])
```

### Test vs train
After data cleaning, one of the most important steps is to split your data into a *test set* and *train set* to prepare it for modeling. Make sure not to touch the test data until the very end when you believe you have a good model! Use the training data to build your models and test your hypotheses to check their performance.

In Spark it's important to make sure you split the data **only after** all the transformations. This is because operations like `StringIndexer` don't always produce the same index even when given the same list of strings

### Data transformation
Pass your data through the `Pipeline` created earlier.
```
# fit and transform the data
piped_data = pipeline.fit(model_data).transform(model_data)
```

### Split the data
The last step before modeling is to split the data.
```
# split the data into training and test sets
train, test = piped_data.randomSplit([.6, .4])
```

## Model Tuning and Selection
You can tune models by testing different values for several `hyperparameters`. A *hyperparameter* is a value in the model that is not estimated from the data, but rather is supplied by the user to maximize performance.

### Creating the modeler
Here, the `Estimator` you will use the is `LogisticRegression` from `pyspark.ml.classification`.  

```
# import LogisticRegression
from pyspark.ml.classification import LogisticRegression

# create a LogisticRegression Estimator
lr = LogisticRegression()
```

### Cross validation
We will use a procedure called `k-fold cross validation` to tune the logistic regression model. This is a way of estimating the model's performance on unseen data such as the `test` DataFrame.

- `k-fold cross validation` splits the training data into `k` number of partitions which is three by default in PySpark.
- One of the partitions is set aside and the model is fit on the rest of the training data
- The error is then measured against the held out partition.
- This is repeated for every partition so every block of data is held out and used as a test set exactly once.
- The error on every partition is then averaged. This is the `cross validation error` of the model and is a good estimate of the actual error on the held out data.

By using cross validation and creating a grid of possible pairs of values for the two hyperparameters `elasticNetParam` and `regParam` and using the cross validation error to compare the different models, you can choose the best hyperparameters for the model.

### Create the evaluator
In order to compare the different models when doing cross validation for model selection, you will need an `Evaluator`. Since logistic regression is a binary classification model, you will use the `BinaryClassificationEvaluator` from the `pyspark.ml.evaluation` submodule.

This evaluator calculates the area under the ROC. This is a metric that combines the two kinds of errors a binary classifier can make (false positives & false negatives) into a simple number.

```
# import the evaluation submodule
import pyspark.ml.evaluation as evals

# create a BinaryClassificationEvaluator
evaluator = evals.BinaryClassificationEvaluator(metricName="areaUnderROC")
```

### Make a grid
You can use the `ParamGridBuilder` class from the `pyspark.ml.tuning` submodule to create a grid of values to search over when looking for the optimal hyperparameters. Use the `.addGrid()` and `.build()` methods to create the grid to be used for cross validation.

- `.addGrid()` method takes a model parameter (an attribute of the model `Estimator`, `lr` that was previously created) and a list of values to try.
- `.build()` method takes no arguments and just returns the grid to be used.

```
# import the tuning submodule
import pyspark.ml.tuning as tune

# create the parameter grid
grid = tune.ParamGridBuilder()

# add the hyperparameters
grid = grid.addGrid(lr.regParam, np.arrange(0, .1, .01))
grid = grid.addGrid(lr.elasticNetParam, [0, 1])

# build the grid
grid = grid.build()
```

### Make the validator
The `pyspark.ml.tuning` sumodule also has a `CrossValidator` class for performing cross validation. It's an `Estimator` that takes the model to fit, the grid of hyperparameters and the evaluator to use to compare the models.

```
# create the CrossValidator
cv = tune.CrossValidator(
    estimator=lr,
    estimatorParamMaps=grid,
    evaluator=evaluator)
```

### Fit the model(s)
Cross Validation is computationally expensive so just be warned!

```
# using cross validation
# fit cross validation models
models = cv.fit(training)

# extract the best model
best_lr = models.bestModel

# fitting a single model
# best_lr = lr.fit(training)
```

### Evaluating binary classifiers
`AUC (area under the curve)` is a common metric used to evaluate binary classification algorithms. In this case, the curve is the `ROC (receiver operating curve)`. 

Simply put, the closer the `AUC` is to `1`, the better the model is

```
# use the model to predict the test set
test_results = best_lr.transform(test)

# evaluate the predictions
print(evaluator.evaluate(test_results))

# results: 0.7125950520012991
```

## Conclusion
This is only an introduction to Spark, next steps include learning how to create large scale Spark clusters, managing and submitting jobs so that you can use models in the real world.
