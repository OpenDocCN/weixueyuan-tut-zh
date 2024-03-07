# Django 实现数据表数据插入（创建 Model 实例）

> 原文：[`c.biancheng.net/view/7642.html`](http://c.biancheng.net/view/7642.html)

在《Django ORM 进阶之项目实战》一节，我们利用 Model 模型类创建了 index 应用所需的三张数据表，在本节我们介绍如何实现在 Django 项目中创建 Model 的实例对象，即向数据表中插入数据。

Django 为创建 Model 实例提供了两种方法，一种是直接调用 Model 的 save 方法，另一种是通过对象管理器（objects Manager） 的 create 方法。这种中方法都可以实现创建 Model 实例对象。下面我们使用 Django shell 交互环境依次对这两种方法进行解析。

Django shell 是 Django 提供一种交互环境，类似于 Python shell，开发人员可直接通过 shell 环境 与 Django 项目进行交互，它一种很实用的工具。

## 1\. save 方法创建 Model 实例

在 cmd 命令行进入项目 manage.py 目录下，然后使用如下命令进入 Django shell 环境。

python manage.py shell 

然后创建 Model 实例，使用 save() 方法保存数据，如下所示：

```

from index.models import Book #导入相关 model 类
#创建 Book 实例化对象
book=Book(title="Python",public="a",price="59.00",retail_price="59.00")
book.save()# 调用 save 方法进行保存
book=Book(title="Flask",public="b",price="39.00",retail_price="39.00")
book.save()# 调用 save 方法进行保存
book=Book(title="Django",public="b",price="40.00",retail_price="40.00")
book.save()# 
```

调用 save 方法进行保存通过上述代码后我们就在数据库中添加了三条有关书籍的记录。在创建 Book 实例的过程中可以知道，创建 Model 实例就是填充它的各个字段没，如果设置了默认值或者有允许为 null 的字段可以不填，最后调用 save() 方法，该方法没有返回值，在 save() 执行以后，Django 会将 book 实例化数据保存到数据库中。注意，每创建一条数据都需要调用一次 save() 方法，我们通过查看数据库可以得知数据信息已经保存完毕。如下所示：  

```

mysql> select * from index_book;
+----+--------+--------+-------+--------------+
| id | title  | public | price | retail_price |
+----+--------+--------+-------+--------------+
|  1 | Python | a      | 59.00 |        59.00 |
|  2 | Flask  | b      | 39.00 |        39.00 |
|  3 | Django | b      | 40.00 |        40.00 |
+----+--------+--------+-------+--------------+
3 rows in set (0.00 sec)
```

避坑提示，若在实例化数据对象的过程中，Django shell 出现以这样的报错信息：

InternalError: (1366, "Incorrect string value: '\\xE8\\xAF\\xAD\\xE8\\xA8\\x80...' for column 'public' at row 1")

说明我们添加的 public 字段的信息超过可字段本身的最大长度限制，即 manx_length=50。解决方法是将字段 max_length 值变大然后需要重新执行数据库迁移的操作，或者将你输入的信息缩短从而符合其规定的长度。

save() 方法还可以保存已有对象更改后的信息，比如：

```

book.title="JavaScript"
book.save()
```

## 2\. create 方法创建 Model 实例

除了以上方法外，我们也可以使用管理器对象提供的 create() 方法，使用方法如下所示：

```

In [1]: Book.objects.create(title="Java",public="a",price="30.00",retail_price="30.00")
Out[1]: <Book: title:Java public:a price:30.00> #执行 create 方法存在返回值
```

然后继续插入如下数据：

```

from index.models import Book
In [2]: Book.objects.create(title="MySQL",public="c",price="35.00",retail_price="35.00")
Out[2]: <Book: title:MySQL public:c price:35.00>
In [3]: Book.objects.create(title="C#",public="a",price="45.00",retail_price="40.00")
Out[3]: <Book: title:C# public:a price:45.00>
In [4]: Book.objects.create(title="Redis",public="c",price="25.00",retail_price="25.00")
Out[4]: <Book: title:Redis public:c price:25.00>
```

这种创建 Model 实例对象的过程比较简单，create 执行之后就在数据库中插入了一条记录,这也是我们推荐使用的方法。最后访问数据库可以看到最新插入的数据记录：

```

mysql> select * from index_book;
+----+--------+--------+-------+--------------+
| id | title  | public | price | retail_price |
+----+--------+--------+-------+--------------+
|  1 | Python | a      | 59.00 |        59.00 |
|  2 | Flask  | b      | 39.00 |        39.00 |
|  3 | Django | b      | 40.00 |        40.00 |
|  4 | Java   | a      | 30.00 |        30.00 |
|  5 | MySQL  | c      | 35.00 |        35.00 |
|  6 | C#     | a      | 45.00 |        40.00 |
|  7 | Redis  | c      | 25.00 |        25.00 |
+----+--------+--------+-------+--------------+
```

本节我们完成了向数据表插入数据记录的操作，不管是在 shell 环境下，还是项目的代码中都可以使用以上两种方法实现创建 Model 实例。后续章节会将涉及 Django 表的数据查询，我们还会用到本节内容。