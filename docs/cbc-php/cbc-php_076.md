# PHP key()：返回数组中当前元素的键名

> 原文：[`c.biancheng.net/view/6225.html`](http://c.biancheng.net/view/6225.html)

PHP 中的 key() 函数用来获得数组中当前元素的键名。

每个 PHP 数组内部都有一个指针，该指针指向数组的某个元素，这个被指向的元素就是“当前元素”。可以通过 current() 函数返回数组的当前元素。

key() 函数的语法格式如下：

mixed key ( array $arr )

参数 arr 表示要被操作的数组。

返回值：key() 函数返回数组中内部指针指向的当前元素的键名，但它不会移动指针。如果内部指针超出了数组的范围，或者数组是空的，那么 key() 就会返回 NULL。

示例如下：

```

<?php
$info = array(
    'name' => 'C 语言中文网',
    'url' => 'http://c.biancheng.net',
    'age' => 8,
    'desc' => '一个学习编程的网站',
    'course' => 'PHP 教程'
);
for ($i=0,$len=count($info); $i<$len; $i++) {
    echo key($info) . "<br/>";  //输出内部指针指向的当前元素的键
    next($info);  // 将数组内部指针向后移动一位
}
?>
```

执行以上程序的结果为：

name
url
age
desc
course