# Apache Spark

[<-- Back to Main Menu](../README.md)

---
### Getting started

- Started using the [quick start guide](https://spark.apache.org/docs/latest/quick-start.html)
    - Able to run first spark application. Needed to make [minor changes](https://github.com/sagnikbanerjeesb/spark-learning-line-counter/blob/f69925313488e71cf4d97dee49133d7e69d3bda4/src/main/java/com/sagnik/sparklearning/App.java) to the example code.
    - [Working Application Code that was deployed on spark](https://github.com/sagnikbanerjeesb/spark-learning-line-counter/commit/f69925313488e71cf4d97dee49133d7e69d3bda4)
    - Script to submit the task on spark:
      ```bash
      $SPARK_HOME/bin/spark-submit \
      --class "com.sagnik.sparklearning.App" \
      --master "local[4]" \
      target/line-counter-1.0-SNAPSHOT.jar
      ```
      NOTE: `SPARK_HOME` variable has been defined as the root folder where spark was installed.

Questions:
1. How exactly does it work?
    1. Does Spark run as a server? -> **Seems so.**
    1. But then the example shows a full fledged java application and the code doesn't seem to connect to any url?
        1. -> **The application is deployed using the `spark-submit` script which should be starting up a spark server locally and the spark server should be executing the application**
    
---

### Reading from kafka

- Intention: Connect with Kafka. Keep reading and processing from a topic (stream).
##### Brain dump:
- Seems like we can use the [Structured streaming approach](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
- And follow the [kafka integration guide](https://spark.apache.org/docs/latest/structured-streaming-kafka-integration.html)
    - Probably this is what we should use
      ```java
      Dataset<Row> df = spark
        .readStream()
        .format("kafka")
        .option("kafka.bootstrap.servers", "host1:port1,host2:port2")
        .option("subscribe", "topic1")
        .load();
      df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)");
      ```
    - After that, probably something like this to process the dataset:
      ```java
      Dataset<String> words = lines
        .as(Encoders.STRING())
        .flatMap((FlatMapFunction<String, String>) x -> Arrays.asList(x.split(" ")).iterator(), Encoders.STRING());
      ```
---