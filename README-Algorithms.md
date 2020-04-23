# 304-SA Streaming algorithms on Spark

Module 1, Big Data course (81932), University of Bologna.

## 304-SA-0 Setup

The next exercises are prepared for the shell environment and the ```dataset/tweet.dsv``` dataset. Notice that only HyperLogLog++ (304-SA-1) is available in Spark 1.

## 304-SA-1 Approximating distinct count (HyperLogLog++)

```
import org.apache.spark.sql

val a = sc.textFile("hdfs:/bigdata/dataset/tweet").map(_.split("\\|")).filter(_(0)!="LANGUAGE")
val b = a.filter(_(2)!="").flatMap(_(2).split(" ")).filter(_!="").map(_.replace(",","")).toDF("hashtag").cache()
b.collect()
```

Exact result:
```
b.agg(countDistinct("hashtag")).collect()
```

Approximate result (default relative standard deviation = 0.05):
```
b.agg(approxCountDistinct("hashtag")).collect()
b.agg(approxCountDistinct("hashtag",0.1)).collect()
b.agg(approxCountDistinct("hashtag",0.01)).collect()
```

## 304-SA-2 Approximating membership (Bloom filter)

Exact result:
```
b.filter("hashtag = '#vaccino'").limit(1).count() // returns 1
b.filter("hashtag = '#vaccino2'").limit(1).count() // returns 0
```

```bloomFilter``` triggers an action; n=1000, p=0.01

```
val bf = b.stat.bloomFilter("hashtag", 1000, 0.01)
bf.mightContain("#vaccino") // returns true
bf.mightContain("#vaccino2") // probably returns false

val bf = b.stat.bloomFilter("hashtag", 1000, 10)
bf.mightContain("#vaccino") // returns true
bf.mightContain("#vaccino2") // may return true
```

## 304-SA-3 Approximating frequency (Count-Min sketch)

Exact result:
```b.filter("hashtag = '#vaccino'").count()```

```countMinSketch``` triggers an action; ε=0.01, 1-δ=0.99, seed=10

```
val cms = b.stat.countMinSketch("hashtag",0.01,0.99,10)
cms.estimateCount("#vaccino")

val cms = b.stat.countMinSketch("hashtag",0.1,0.99,10)
cms.estimateCount("#vaccino")

val cms = b.stat.countMinSketch("hashtag",0.01,0.9,10)
cms.estimateCount("#vaccino")
```