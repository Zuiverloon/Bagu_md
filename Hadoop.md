## hadoop

## Map reduce

一种可并行处理大量数据的框架
先把数据分成不同 chunk，由不同机器并行的执行 Map task，然后把 map 完的结果排序作为输入给 reduce task，
Mapreduce 任务的输入输出都是<key,value>对

## word count

https://hadoop.apache.org/docs/stable/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html

```java
public class WordCount {

  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```

## hadoop config

core-site.xml

```xml
<!-- 指定namenode的地址 -->
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://vm-1:9000</value>
</property>
//todo:需要加一下存储位置
<property>
    <name>hadoop.tmp.dir</name>
    <value></value>
</property>
```

hadoop-env.sh
export JAVA_HOME=/usr
export HADOOP_CLASSPATH=/usr/lib/jvm/java-1.8.0-openjdk-amd64/lib/tools.jar

hdfs-site.xml

```xml
<property>
    <name>dfs.replication</name>
    <value>1</value>
</property>
<property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>vm-2:50090</value>
</property><!-- 如果是单集群，配置为vm-1 -->
```

yarn-env.sh
export JAVA_HOME=/usr

yarn-site.xml

```xml
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>vm-1</value>
</property>
```

mapred-env.sh
export JAVA_HOME=/usr

mapred-site.xml

```xml
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```

## yarn

yarn 是 resourcemanager，hadoop 有许多 resourcemanager，yarn/kubernates 是其中之一
