# [PySpark](https://spark.apache.org/docs/latest/rdd-programming-guide.html#transformations)
- [Start with PySpart](#Start-with-PySpart)
- [Transformations](#Transformation)
- [Actions](#Actions)
- [Associative and Commutative Properties](#Associative-and-Commutative-Properties)
- [Broadcast Variables](#Broadcast-Variables)
Resilient Distributed Datasets.

![alt text](https://github.com/LamPhuocGiau/Data_Engineer/blob/submain/Theories/Images/Resilient-distributed-datasets.png)

## Start with PySpart

Start a new session and save it as spark.

```
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()
```

**Default setting from data saved locally**.

```
rdd_par = spark.sparkContext.parallelize(dataset_name)

```
sparkContext: connect to the cluster.

parallelize(): create an RDDfrom data saved locally.

**External dataset**.

```
# with partition argument of 10
rdd_txt = spark.sparkContext.textFile("file_name.txt", 10)
```
TextFile(): create an RDD.

**Verify the number of partition**.

```
rdd_txt.getNumPartitions()
# output: 10
```
**End SparkSession**.

```
spark.stop()
```
## Transformations

**lambda expression**.

```
add_one = lambda x: x+1 # apply x+1 to x
print(add_one(10)) # this will output 11
```

map(): applies an operation to each element of the RDD.

```
rdd = spark.SparkContent.parallelize([1,2,3,4,5])
rdd.map(lambda x: x+1)
# output RDD [2,3,4,5,6]
```
RDD contains tuples, use specific index to change element values.

```
# input RDD [(1,2,3),(4,5,6),(7,8,9)]
rdd.map(lambda x: (x[0]+1, x[1], x[2]))
# output RDD [(2,2,3),(5,5,6),(8,8,9)]
```
filter() allows us to remove or keep data conditionally.

```
# input RDD [1,2,NULL,4,5]
rdd.filter(lambda x: x is not None)
# output RDD [1,2,4,5]
```
## Actions

Spark might load the values greater than 3 into memory first and perform the map function last. This swap will save memory and time.

```
rdd = spark.SparkContent.parallelize([1,2,3,4,5])
rdd.map(lambda x: x+1).filter(lambda x: x>3)
```
collect() was called to return the entire contents of the new RDD.

take(n) to view the first n elements of a large RDD.

```
# input RDD [1,2,3,4,5]
rdd.take(3)
# output
[1, 2, 3]
```
reduce() to return fewer elements of our RDD.

```
# input RDD [1,2,3,4,5]
rdd.reduce(lambda x,y: x+y)
# output 15
```

## Associative and Commutative Properties

The commutative property allows for all parallel tasks to execute and conclude without waiting for another task to complete.

The associative property allows Spark to partition and distribute our data to multiple nodes because the result will stay the same no matter how tasks are grouped.

## Broadcast Variables

Information that is made available to all nodes is what Spark calls broadcast variables.

```
# list of states
states = ['FL', 'NY', 'TX', 'CA', 'NY', 'NY', 'FL', 'TX']
# convert to RDD
states_rdd = spark.sparkContext.parallelize(states)
# dictionary of regions
region = {"NY":"East", "CA":"West", "TX":"South", "FL":"South"}
# broadcast region dictionary to nodes
broadcast_var = spark.sparkContext.broadcast(region)
# map regions to states
result = states_rdd.map(lambda x: broadcast_var.value[x])
# view first four results
result.take(4)
# output : [‘South’, ‘East’, ‘South’, ‘West’]
```

