# spark

> + 目的：spark学习
> + 学习资料
>   + [马士兵Java大数据之Spark2.1_入门第一话](https://www.bilibili.com/video/av13627672)
>   + 

## 安装

> spark运行需要依赖于java运行环境，并且版本需要在1.8以上

1. 安装jdk,并配置java环境变量

2. 下载[spark压缩包](http://spark.apache.org/downloads.html)

3. 解压到`/usr/local/`目录下

4. 将解压后的spark目录下的`bin`目录添加到环境变量

5. 进入`spark/conf/`执行

   ```shell
   sudo cp spark-env.sh.template spark-env.sh
   ```

   在`spark-env.sh`文件结尾添加

   ```
   export  SPARK_MASTER_IP=127.0.0.1
   export  SPARK_LOCAL_IP=127.0.0.1
   ```

5. 测试

   在`spark`目录下运行：

   ```shell
   spark-submit --class org.apache.spark.examples.SparkPi ./examples/jars/spark-examples_2.11-2.3.1.jar 10000
   ```

   此时程序会运行一段时间，而且在`localhost:4040`端口能监测到这个job的运行状态

## spark shell

> 1. `spark shell`是spark的命令行，相当于mysql的命令行
> 2. `spark shell`中使用的是scala语法
> 3. `spark shell`下执行的命令都会使用`spark-submit`提交到`local`模式运行，然后将运行结果返回
> 4. 进入`spark shell`的命令为`spark-shell` 

## RDD

> 1. RDD全称`弹性分布式结果集`
> 2. 由于RDD的存在，spark的运算速度是hadoop的10~100倍

### 什么是RDD

+ 在spark shell中按如下步骤执行命令

  ```shell
  scala> val lineRDD=sc.textFile("hello.txt")
  lineRDD: org.apache.spark.rdd.RDD[String] = hello.txt MapPartitionsRDD[1] at textFile at <console>:24
  
  scala> lineRDD.collect
  res0: Array[String] = Array(hello, hello world, hello java, helloc, hello c++, c++, c#, hello python)
  
  scala> val wordRDD=lineRDD.flatMap(line=>line.split(" "))
  wordRDD: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[2] at flatMap at <console>:25
  
  scala> wordRDD.collect
  res1: Array[String] = Array(hello, hello, world, hello, java, helloc, hello, c++, c++, c#, hello, python)
  
  scala> val wordCountRDD = wordRDD.map(word=>(word,1))
  wordCountRDD: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[3] at map at <console>:25
  
  scala> wordCountRDD.collect
  res2: Array[(String, Int)] = Array((hello,1), (hello,1), (world,1), (hello,1), (java,1), (helloc,1), (hello,1), (c++,1), (c++,1), (c#,1), (hello,1), (python,1))
  
  scala> val resultRDD = wordCountRDD.reduceByKey((x,y)=>x+y)
  resultRDD: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[4] at reduceByKey at <console>:25
  
  scala> resultRDD.collect
  res3: Array[(String, Int)] = Array((c#,1), (python,1), (hello,5), (java,1), (world,1), (helloc,1), (c++,2))
  ```

+ 上面运算的过程中，`lineRDD`、`wordRDD`、`wordCountRDD`、`resultRDD`这样的每个中间运行结果都是1个`RDD`
