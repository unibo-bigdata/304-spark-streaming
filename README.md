# 304 Spark Streaming

Module 1, Big Data course (81932), University of Bologna.

## 304-0 Setup

For the streaming scenario we need two terminal instances:
- One to run a Netcat server to send some data
- One to run the streaming application

### Netcat server

To simulate the streaming scenario we need a source that sends data. 
Both the cluster and the VM are equipped with Netcat.
- Usage: ```nc -lk <port>```

Use an available port number, e.g., ```9999```. 
Beware of possible conflicts when doing this on the cluster. 

### Streaming application via spark-submit

This is the best way to run streaming applications.

- Usage: ```spark2-submit --class Exercise BD-304-spark-streaming.jar <exerciseNumber> <host> <port>``` (on the cluster, use ```spark-submit``` )
  - ```<host>``` is ```localhost``` on the VM, or the IP of your machine on the cluster
   (IPs are 137.204.72.[233+N] where N is isi-vclustN)
  - Some exercises require additional parameters; in such cases, proper usage is indicated in the exercises' description.

### Streaming application via spark-shell

The Spark shell is not the best tool for the streaming scenario: 
once a StreamingContext has been stopped, it cannot be restarted. 
- The whole application must be rewritten every time.
- If the SparkContext is accidentally stopped, the shell must be restarted as well.

Fastest way to operate on the shell:
- Write your application in a text editor (e.g., Notepad++) 
or an IDE (e.g., Eclipse/IntelliJ)
- Copy/paste the whole application in the shell
  - No need to use ```:paste``` mode; just paste
- Call ```ssc.stop(false)``` to stop the StreamingContext 
and leave the SparkContext alive
  - Beware: it may happen that Spark does not close correctly
  the StreamingContext; in such case, the shell must be restarted
- Repeat

### Note for VM users

The minimum setup for the VM may not be enough for the streaming
application to correctly print output on the terminal's console.

## 304-1 Testing the streaming application

Copy/Paste some content (e.g., the text from the ```divinacommedia.txt```
file) into the Netcat server and see what happens on the application's console.

## 304-2 Word count

Same as above

## 304-3 Enabling checkpoint

Checkpoints allow the application to restart from where it last stopped.
               
NOTICE: you need to create a directory on HDFS to store the checkpoint data.
For instance:
- ```hdfs dfs -mkdir streaming```
- ```hdfs dfs -mkdir streaming/checkpoint3```

Then run the application:
- ```spark2-submit --class Exercise BD-304-spark-streaming.jar <exerciseNumber> <host> <port> <path>```
- ```<path>``` is the absolute path on HDFS, e.g., ```/user/egallinucci/streaming/checkpoint3```

## 304-4 Enabling State

State allows the job to continuously update a temporary result (i.e., the state).
   
NOTICE: you need to either
- create a DIFFERENT directory on HDFS to store the checkpoint data
- empty the previous directory

Otherwise, the application will re-run the job already checkpointed in the directory.

- ```spark2-submit --class Exercise BD-304-spark-streaming.jar <exerciseNumber> <host> <port> <path>```

## 304-5 Sliding windows

This job carries out word counting on a sliding window 
that is wide 30 seconds and is updated every 3 seconds.

## 304-6 Trending hashtags

The dataset for this exercise (and for the next ones) 
is the content of ```dataset/tweet.dsv```, 
which contains a set of public tweets that discussed the topic
of vaccines back in 2016.
 
This job is a simple evolution of word counting to carry out hashtag 
counting via sliding windows. 
The window is wide 1 minute and it is updated every 5 seconds.

## 304-7 Incremental count of tweets by city

This is a stateful job to incrementally count the number of tweets by city. 

Remember to either create a new directory on HDFS or to empty the previous one.
- ```spark2-submit --class Exercise BD-304-spark-streaming.jar <exerciseNumber> <host> <port> <path>```

## 304-8 Incremental count of tweets by city

This job extends the previous one by calculating also the average sentiment 
(per country instead of per city).

Remember to either create a new directory on HDFS or to empty the previous one.
- ```spark2-submit --class Exercise BD-304-spark-streaming.jar <exerciseNumber> <host> <port> <path>```

## 304-9 Streaming algorithms

Go to "README-Algorithms.md" for exercises on streaming algorithms.