# Exercise: Flattening Array Columns (From Datasets of Arrays to Datasets of Array Elements)

Develop a standalone Spark SQL application (using IntelliJ IDEA) that creates a Dataset with columns that have the elements of an array column as values and their names being positions (in the array column).

Protips™:

1. (intermediate) Assume that the number of elements of all arrays is the same

1. (advanced) Consider [pivot](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.RelationalGroupedDataset) & [posexplode](https://spark.apache.org/docs/3.1.2/api/scala/org/apache/spark/sql/functions$.html#posexplode) operator

Module: **Spark SQL**

Duration: **30 mins**

## Input Dataset

```text
val input = Seq(
  Seq("a","b","c"),
  Seq("X","Y","Z")).toDF
scala> input.show
+---------+
|    value|
+---------+
|[a, b, c]|
|[X, Y, Z]|
+---------+

scala> input.printSchema
root
 |-- value: array (nullable = true)
 |    |-- element: string (containsNull = true)
```

## Result

```text
+---+---+---+
|  0|  1|  2|
+---+---+---+
|  a|  b|  c|
|  X|  Y|  Z|
+---+---+---+
```

<!--
// The solution assumes that the number of elements is the same across arrays
val header = input.as[Array[String]].head
val columns = header.indices.map(n => 'value(n) as n.toString)
val s = input.select(columns: _*)

// The solution uses groupBy so it introduces a shuffle
// pivot needs values or it does full scan
// Possible case for Adaptive Query Execution
val psd = input.select(posexplode('value))
// Note the values specified explicitly
val s = psd.groupBy().pivot('pos, Array(0,1,2)).agg(first('col))

-->
