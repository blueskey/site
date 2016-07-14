---
layout: post 
title: "windows下使用IDEA开发MapReduce"
date: 2016-06-30 10:12:21
categories: hadoop mapreduce windows idea
---

在虚拟机上搭建hadoop，并启动（这里采用伪分步式。）
```
hdfs-site.xml：

<configuration>

<property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>

 <property>
        <name>dfs.name.dir</name>
        <value>/usr/local/hadoop/hdfs/name</value>
    </property>
    <property>
        <name>dfs.data.dir</name>
        <value>/usr/local/hadoop/hdfs/data</value>
    </property>
<property>
    <!-- 是否对DFS中的文件进行权限控制(测试中一般用false)-->
    <name>dfs.permissions</name>
    <value>false</value>
</property>
</configuration>
```
core-site.xml：
```
<configuration>
 <property>
        <name>fs.defaultFS</name>
        <value>hdfs://192.168.169.148:9000</value>
    </property>
      <property>
        <name>hadoop.tmp.dir</name>
        <value>/usr/local/hadoop/tmp</value>
    </property>
</configuration>
```
mapred-site.xml：
```
<configuration>
 <property>
        <name>fs.defaultFS</name>
        <value>hdfs://192.168.169.148:9000</value>
    </property>
      <property>
        <name>hadoop.tmp.dir</name>
        <value>/usr/local/hadoop/tmp</value>
    </property>
</configuration>
```
```
[root@bogon hadoop-2.7.2]# cat etc/hadoop/mapred-site.xml
<configuration>
    <property>
        <name>mapred.job.tracker</name>
        <value>192.168.169.148:9001</value>
    </property>
</configuration>
```
开启hadoop:
```
  [root@bogon hadoop-2.7.2]# ./sbin/start-dfs.sh
```
查看hadoop 是否成功：
```
[root@bogon hadoop-2.7.2]# jps
3680 DataNode
3571 NameNode
4371 HRegionServer
3880 SecondaryNameNode
6479 Jps
```

准备测试文件：
```
[root@bogon files]# pwd
/root/study/files
[root@bogon files]# ll
total 12
-rw-r--r--. 1 root root 31 Jun 29 18:54 a.txt
-rw-r--r--. 1 root root 27 Jun 29 18:55 b.txt
-rw-r--r--. 1 root root 37 Jun 29 18:55 c.txt
[root@bogon files]#
```
在a.txt，b.txt，c.txt中随意输入单词或语句；

导入到hdfs文件系统：
```
[root@bogon hadoop-2.7.2]# ./bin/hdfs dfs -mkdir -p /wordcount/input
root@ubuntu:~/study/hadoop/hadoop-2.7.2# ./bin/hadoop fs -put ~/study/files/*  /wordcount/input
```
导入后查看是否成功：
```
[root@bogon hadoop-2.7.2]# ./bin/hdfs dfs -ls -R /wordcount
drwxr-xr-x   - root supergroup          0 2016-06-30 00:44 /wordcount/input
-rw-r--r--   1 root supergroup         31 2016-06-30 00:44 /wordcount/input/a.txt
-rw-r--r--   1 root supergroup         27 2016-06-30 00:44 /wordcount/input/b.txt
-rw-r--r--   1 root supergroup         37 2016-06-30 00:44 /wordcount/input/c.txt
```

在IDEA中编写MapReduce程序：

``` java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.reduce.IntSumReducer;

import java.io.IOException;
import java.util.StringTokenizer;


/**
 * Created by Administrator on 2016/5/31 0031.
 */
public class WordCount  {

    public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {
        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
            StringTokenizer stringTokenizer = new StringTokenizer(value.toString());
            while (stringTokenizer.hasMoreTokens()) {
                word.set(stringTokenizer.nextToken());
                context.write(word, one);
            }
        }
        public static class IntSumReducer  extends Reducer<Text,IntWritable,Text,IntWritable> {
            private IntWritable result = new IntWritable();

            public void reduce(Text key, Iterable<IntWritable> values,   Context context ) throws IOException, InterruptedException {
                int sum = 0;
                for (IntWritable val : values) {
                    sum += val.get();
                }
                result.set(sum);
                context.write(key, result);
            }
        }
    }

    public static void main(String[] args) {
        Configuration conf = new Configuration();
        conf.set("dfs.permissions","false");
        conf.set("fs.default.name", "hdfs://192.168.169.148:9000");
        conf.set("hadoop.job.user", "root");
        conf.set("mapred.job.tracker", "192.168.169.148:9001");
        conf.set("mapred.jar","G:\\study\\workspace\\hadooptest\\output\\hadoopTest.jar");
        try {
            Job job = Job.getInstance(conf, "word count");
            job.setJarByClass(WordCount.class);
            job.setMapperClass(TokenizerMapper.class);
            job.setCombinerClass(IntSumReducer.class);
            job.setReducerClass(IntSumReducer.class);
            job.setOutputKeyClass(Text.class);
            job.setOutputValueClass(IntWritable.class);
//        FileInputFormat.addInputPath(job, new Path(args[0]));
//        FileOutputFormat.setOutputPath(job, new Path(args[1]));
            FileInputFormat.addInputPath(job, new Path("hdfs://192.168.169.148:9000/wordcount/input"));
            FileOutputFormat.setOutputPath(job, new Path("hdfs://192.168.169.148:9000/wordcount/output"));
            System.exit(job.waitForCompletion(true) ? 0 : 1);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```
pom.xml:
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>my</groupId>
    <artifactId>hadoopTest</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <!--<dependency>-->
            <!--<groupId>org.apache.hadoop</groupId>-->
            <!--<artifactId>hadoop-core</artifactId>-->
            <!--<version>1.2.1</version>-->
        <!--</dependency>-->
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.7.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-mapreduce-client-core</artifactId>
            <version>2.7.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.7.2</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-mapreduce-client-common -->
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-mapreduce-client-common</artifactId>
            <version>2.7.2</version>
        </dependency>

        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-yarn-common</artifactId>
            <version>2.7.2</version>
        </dependency>
    </dependencies>

</project>

```
打开 Project Structure窗口（Ctrl+Alt+Shift+S），Artifacts，勾选Build on make。

这里设置的是就这个build过后的jar包：
     conf.set("mapred.jar","G:\\study\\workspace\\hadooptest\\output\\hadoopTest.jar");


运行WordCount文件。以下代码或配置文件都是修改过后的，可能不会出现下面的问题了，没有修改前，问题多多，如下：

问题1：

     连接不到192.168.169.146:9000上

     解决：将hadoop配置文件中的localhost都改为ip。

问题2：

     Server IPC version 9 cannot communicate with client version 4

     解决：当前使用hadoop版本为2.7.2，  去除pom.xml文件中的依赖：
```
<!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-core -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-core</artifactId>
    <version>1.2.1</version>
</dependency>
```


问题3：
```
     Cannot initialize Cluster. Please check your configuration for mapreduce.framework.name and the corr
```
添加依赖：
```
<!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-mapreduce-client-common -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.2</version>
</dependency>
```

问题4：

     运行到 job.waitForCompletion(true) 时，报NullpointException。

     解决：
               hadoop2.x在windows下编译缺少winutils.exe，需要重新下载一个并且配置hadoop_home环境变量，修改path变量
               同时在system32文件夹下添加hadoop.dll文件。
     步骤：1、下载与hadoop同版本的winutils.exe和hadoop.dll，
               2、替换window下hadoop目录下bin文件；
               3、拷贝hadoop.dll至C:\Windows\System32目录下；
               4、设置环境变量HADOOP_HOME；修改path：添加HADOOP_HOME\bin;
               5、重新运行wordCount.java，如不行，重启下IDEA；


运行成功：

 Process finished with exit code 0

返回linux虚拟机（192.168.169.148）下，查看hdfs文件系统，发现已经有了输出目录产生：

```
[root@bogon hadoop-2.7.2]# ./bin/hdfs dfs -ls -R /
drwxr-xr-x   - root supergroup          0 2016-06-29 19:26 /hh
drwxr-xr-x   - root supergroup          0 2016-06-29 19:26 /hh/.tmp
drwxr-xr-x   - root supergroup          0 2016-06-29 19:26 /hh/.tmp/data
...
-rw-r--r--   3 root supergroup          7 2016-06-29 19:26 /hh/hbase.version
drwxr-xr-x   - root supergroup          0 2016-06-29 19:37 /hh/oldWALs
drwxr-xr-x   - root supergroup          0 2016-06-29 23:57 /wordcount
drwxr-xr-x   - root          supergroup          0 2016-06-29 19:25 /wordcount/input
-rw-r--r--   1 root          supergroup         31 2016-06-29 19:25 /wordcount/input/a.txt
-rw-r--r--   1 root          supergroup         27 2016-06-29 19:25 /wordcount/input/b.txt
-rw-r--r--   1 root          supergroup         37 2016-06-29 19:25 /wordcount/input/c.txt
drwxr-xr-x   - Administrator supergroup          0 2016-06-29 23:57 /wordcount/output
-rw-r--r--   3 Administrator supergroup          0 2016-06-29 23:57 /wordcount/output/_SUCCESS
-rw-r--r--   3 Administrator supergroup        116 2016-06-29 23:57 /wordcount/output/part-r-00000
```

查看输出结果：

```
[root@bogon hadoop-2.7.2]# ./bin/hdfs dfs -cat /wordcount/output/part-r-00000
are    1
book    1
catch    1
dfsa    1
good    1
group    1
hello    1
i    2
like    1
man    1
me    1
my    1
nice    1
ok    2
root    1
super    1
wo    1
you    3
```
想重新运行一遍的话，需要修改为一个新的输出目录，或删除原输出目录。因为如果输出目录已存在的话，会报目录已存在错误！

