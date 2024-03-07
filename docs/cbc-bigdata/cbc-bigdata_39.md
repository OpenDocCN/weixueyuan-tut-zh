# MapReduce 编程实例：单词计数

> 原文：[`c.biancheng.net/view/3632.html`](http://c.biancheng.net/view/3632.html)

本节介绍如何编写基本的 MapReduce 程序实现数据分析。本节代码是基于 Hadoop 2.7.3 开发的。

## 任务准备

单词计数（WordCount）的任务是对一组输入文档中的单词进行分别计数。假设文件的量比较大，每个文档又包含大量的单词，则无法使用传统的线性程序进行处理，而这类问题正是 MapReduce 可以发挥优势的地方。

在前面《MapReduce 实例分析：单词计数》教程中已经介绍了用 MapReduce 实现单词计数的基本思路和具体执行过程。下面将介绍如何编写具体实现代码及如何运行程序。

首先，在本地创建 3 个文件：file00l、file002 和 file003，文件具体内容如表 1 所示。

**表 1 单词计数输入文件**

| 文件名 | file001 | file002 | file003 |
| --- | --- | --- | --- |
| 文件内容 | Hello world Connected world | One world  One dream | Hello Hadoop  Hello Map 
Hello Reduce |

再使用 HDFS 命令创建一个 input 文件目录。

hadoop fs -mkdir input

然后，把 file001、file002 和 file003 上传到 HDFS 中的 input 目录下。

hadoop fs -put file001 input
hadoop fs -put file002 input
hadoop fs -put file003 input

编写 MapReduce 程序的第一个任务就是编写 Map 程序。在单词计数任务中，Map 需要完成的任务就是把输入的文本数据按单词进行拆分，然后以特定的键值对的形式进行输出。

## 编写 Map 程序

Hadoop MapReduce 框架已经在类 Mapper 中实现了 Map 任务的基本功能。为了实现 Map 任务，开发者只需要继承类 Mapper，并实现该类的 Map 函数。

为实现单词计数的 Map 任务，首先为类 Mapper 设定好输入类型和输出类型。这里，Map 函数的输入是 <key,value> 形式，其中，key 是输入文件中一行的行号，value 是该行号对应的一行内容。

所以，Map 函数的输入类型为 <LongWritable,Text>。Map 函数的功能为完成文本分割工作，Map 函数的输出也是 <key，value> 形式，其中，key 是单词，value 为该单词出现的次数。所以，Map 函数的输出类型为 <Text，LongWritable>。

以下是单词计数程序的 Map 任务的实现代码。

```

public static class CoreMapper extends Mapper<Object,Text,Text,IntWritable> {
    private static final IntWritable one = new IntWritable(1);
    private static Text label = new Text();
    public void map(Object key,Text value,Mapper<Object,Text,Text,IntWritable> Context context)throws IOException,InterruptedException {
        StringTokenizer tokenizer = new StringTokenizer(value.toString());
        while(tokenizer.hasMoreTokens()) {
            label.set(tokenizer.nextToken());
            context.write(label,one);
        }
    }
}
```

在上述代码中，实现 Map 任务的类为 CoreMapper。该类首先将需要输出的两个变量 one 和 label 进行初始化。

*   变量 one 的初始值直接设置为 1，表示某个单词在文本中出现过。
*   Map 函数的前两个参数是函数的输入参数，value 为 Text 类型，是指每次读入文本的一行，key 为 Object 类型，是指输入的行数据在文本中的行号。

StringTokenizer 类机器方法将 value 变量中文本的一行文字进行拆分，拆分后的单词放在 tokenizer 列表中。然后程序通过循环对每一个单词进行处理，把单词放在 label 中，把 one 作为单词计数。

在函数的整个执行过程中，one 的值一直是 1。在该实例中，key 没有被明显地使用到。context 是 Map 函数的一种输出方式，通过使用该变量，可以直接将中间结果存储在其中。

根据上述代码，Map 任务结束后，3 个文件的输出结果如表 2 所示。

**表 2 单词计数 Map 任务输出结果**

| 文件名/Map | file001/Map1 | file002/Map2 | file003/Map3 |
| Map 任务输出结果  | <"Hello",1> <"world",1>
<"Connected",1>
<"world",1> | <"One",1> <"world",1>
<"One",1>
<"dream",1>  | <"Hello",1> <"Hadoop",1>
<"Hello",1>
<"Map",1>
<"Hello", 1> 
<"Reduce",1> |

## 编写 Reduce 程序

编写 MapReduce 程序的第二个任务就是编写 Reduce 程序。在单词计数任务中，Reduce 需要完成的任务就是把输入结果中的数字序列进行求和，从而得到每个单词的出现次数。

在执行完 Map 函数之后，会进入 Shuffle 阶段，在这个阶段中，MapReduce 框架会自动将 Map 阶段的输出结果进行排序和分区，然后再分发给相应的 Reduce 任务去处理。经过 Map 端 Shuffle 阶段后的结果如表 3 所示。

**表 3 单词计数 Map 端 Shuffle 阶段输出结果**

| 文件名/Map | file001/Map1 | file002/Map2 | file003/Map3 |
| Map 端 Shuffle 阶段输出结果 | <"Connected",1>  <"Hello", 1>
<"world",<1,1>> | <"dream",1> <"One", <1, 1>>
<"world", 1> |  <"Map", 1>
<"Hadoop",1>
<"Hello",<1,1,1>>
<"Reduce", 1> |

Reduce 端接收到各个 Map 端发来的数据后，会进行合并，即把同一个 key，也就是同一单词的键值对进行合并，形成<key, <V1, V2, .. Vn>> 形式的输出。经过 Map 端 Shuffle 阶段后的结果如表 4 所示。

**表 4 单词计数 Reduce 端 Shuffle 阶段输出结果**

| Reduce 端 | <"Connected",1> |
|  Shuffle 阶段输出结果  | < "dream",1>  <"Hadoop",1> 
<"Hello",<1,1,1,1>> 
<"Map",1>
<"One",<1,1>>
<"world", <1,1,1>>
<"Reduce", 1> |

Reduce 阶段需要对上述数据进行处理从而得到每个单词的出现次数。从 Reduce 函数的输入已经可以理解 Reduce 函数需要完成的工作，就是首先对输入数据 value 中的数字序列进行求 和。以下是单词计数程序的 Reduce 任务的实现代码。

```

public static class CoreReducer extends Reducer<Text,IntWritable,Text,IntWritable> {

    private IntWritable count = new IntWritable ();
    public void reduce(Text key,Iterable<IntWritable> values,Reducer<Text,IntWritable, Text,IntWritable> Context context)throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable intWritable : values){
            sum += intWritable.get();
        }
        count.set(sum);
        context.write(key, count);
    }
}
```

与 Map 任务实现相似，Reduce 任务也是继承 Hadoop 提供的类 Reducer 并实现其接口。 Reduce 函数的输入、输出类型与 Map 函数的输出类型本质上是相同的。

在 Reduce 函数的开始部分，首先设置 sum 参数用来记录每个单词的出现次数，然后遍历 value 列表，并对其中的数字进行累加，最终就可以得到每个单词总的出现次数。在输出的时候，仍然使用 context 类型的变量存储信息。当 Reduce 阶段结束时，就可以得到最终需要的结果，如表 5 所示。

**表 5 单词计数 Reduce 任务输出结果**

| Reduce 任务输出结果 | <"Connected", 1> |
|   | <"dream", 1> |
|   | <"Hadoop", 1> |
|   | <"Hello", 4> |
|   | <"Map", 1> |
|   | <"One", 2> |
|   | <"world", 3> |
|   | <"Reduce", 1> |

## 
编写 main 函数

为了使用 CoreMapper 和 CoreReducer 类进行真正的数据处理，还需要在 main 函数中通过 Job 类设置 Hadoop MapReduce 程序运行时的环境变量，以下是具体代码。

```

public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf,args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.printIn("Usage:wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job (conf, "WordCount"); //设置环境参数
    job.setJarByClass (WordCount.class); //设置程序的类名
    job.setMapperClass(CoreMapper.class); //添加 Mapper 类
    job.setReducerClass(CoreReducer.class); //添加 Reducer 类
    job.setOutputKeyClass (Text.class); //设置输出 key 的类型
    job.setOutputValueClass (IntWritable.class);  
    //设置输出 value 的类型
    FileInputFormat.addInputPath (job, new Path (otherArgs [0]));
    //设置输入文件路径
    FileOutputFormat.setOutputPath (job，new Path (otherArgs [1]));
    //设置输入文件路径
    System.exit(job.waitForCompletion(true) ? 0 : 1);
}
```

代码首先检查参数是不是正确，如果不正确就提醒用户。随后，通过 Job 类设置环境参数，并设置程序的类、Mapper 类和 Reducer 类。然后，设置了程序的输出类型，也就是 Reduce 函数的输出结果 <key,value> 中 key 和 value 各自的类型。最后，根据程序运行时的参数，设置输入、输出文件路径。

## 核心代码包

编写 MapReduce 程序需要引用 Hadoop 的以下几个核心组件包，它们实现了 Hadoop MapReduce 框架。

```

import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper; 
import org.apache.hadoop.mapreduce.Reducer;

import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;
```

这些核心组件包的基本功能描述如表 6 所示。

**表 6 Hadoop MapReduce 核心组件包的基本功能**

| 包 | 功能 |
| org.apache.hadoop.conf | 定义了系统参数的配置文件处理方法 |
| org.apache.hadoop.fs | 定义了抽象的文件系统 API |
| org.apache.hadoop.mapreduce | Hadoop MapReduce 框架的实现，包括任务的分发调度等 |
| org.apache.hadoop.io | 定义了通用的 I/O API，用于网络、数据库和文件数据对象 进行读写操作 |

## 运行代码

在运行代码前，需要先把当前工作目录设置为 /user/local/Hadoop。编译 WordCount 程序需要以下 3 个 Jar，为了简便起见，把这 3 个 Jar 添加到 CLASSPATH 中。

$export
CLASSPATH=/usr/local/hadoop/share/hadoop/common/hadoop-common-2.7.3.jar:$CLASSPATH
$export
CLASSPATH=/usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-2.7.3.jar:$CLAS SPATH
$export
CLASSPATH=/usr/local/hadoop/share/hadoop/common/lib/common-cli-1.2.jar:$CLASSPATH

使用 JDK 包中的工具对代码进行编译。

$ javac WordCount.java

编译之后，在文件目录下可以发现有 3 个“.class”文件，这是 Java 的可执行文件，将它们打包并命名为 wordcount.jar。

$ jar -cvf wordcount.jar *.class

这样就得到了单词计数程序的 Jar 包。在运行程序之前，需要启动 Hadoop 系统，包括启动 HDFS 和 MapReduce。然后，就可以运行程序了。

$ ./bin/Hadoop jar wordcount.jar WordCount input output

最后，可以运行下面的命令查看结果。

$ ./bin/Hadoop fs -cat output/*