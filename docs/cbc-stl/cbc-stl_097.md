# C++ partition_point(STL partition_point)算法使用详解

可以用 partition_point() 算法来获取分区序列中第一个分区的结束迭代器，它的前两个参数定义检查范围的正向迭代器，最后一个参数是用来对序列进行分区的谓词。我们通常不知道每个分区中元素的个数，这个算法使我们能够访问和提取这些分区中的元素。例如：

```
std::vector<double> temperatures {65, 75, 56, 48, 31, 28, 32, 29, 40, 41, 44, 50};
auto average = std::accumulate(std::begin(temperatures),std::end(temperatures), 0.0)/ temperatures.size();
auto predicate = average { return t < average; };
std::stable_partition(std::begin(temperatures), std::end(temperatures), predicate);
auto iter = std::partition_point(std::begin(temperatures),std::end(temperatures), predicate);
std::cout << "Elements in the first partition: ";
std::copy(std::begin(temperatures), iter,std::ostream_iterator<double>{std::cout, " "});
std::cout << "\nElements in the second partition: ";std::copy(iter, std::end(temperatures),std::ostream_iterator<double>{std::cout, " "});
std::cout << std::endl;
```

这段代码会相对于平均温度对 temperatures 中的元素进行分区，并通过调用 partition_point() 找到这个序列的分区点。这个分区点是第一个分区的结束迭代器，它被保存在 iter 中。所以 std::begin(temperatures)，iter) 对应的就是第一个分区中的元素，[iter，std::end(temperatures)) 包含的是第二个分区中的元素。这里使用两次 copy() 算法来输出分区，输出内容如下：

Elements in the first partition: 31 28 32 29 40 41 44
Elements in the second partition: 65 75 56 48 50

在使用 partition_point() 之前，需要确定序列是否已经被分区。如果对此不是很确定，在这种情况下可以使用 is_partitioned() 来判断。它的参数是用来指定序列的输入迭代器和用来对序列进行分区的谓词。如果这个序列已经被分区，这个算法就返回 true，否则返回 false。

在对 temperatures 使用 partition_point() 算法之前，可以先用它来验证这个序列：

```
if(std::is_partitioned(std::begin(temperatures), std::end(temperatures),[average { return t < average; }))
{
    auto iter = std::partition_point(std::begin(temperatures),std::end(temperatures), average { return t < average; });
    std::cout << "Elements in the first partition: ";
    std::copy(std::begin(temperatures), iter,std::ostream_iterator<double>{std::cout, " " });
    std::cout << "\nElements in the second partition: ";
    std::copy(iter, std::end(temperatures),std::ostream_iterator<double>{std::cout," "});
    std::cout << std::endl;
}
else
    std::cout << "Range is not partitioned." << std::endl;
```

只有在 is_partitioned() 返回 true 时，这段代码才会执行。如果 if 语句为 true，iter 变量会指向分区点。如果想在后面继续使用 iter，可以按如下方式在 if 语句之前定义它：

```
std::vector<double>::iterator iter;
```

在所有容器类型的模板中都定义了这样的迭代器类型别名，它们使迭代器变得可以使用。它对应于这个容器类型的成员函数 begin() 和 end() 所返回的迭代器类型。