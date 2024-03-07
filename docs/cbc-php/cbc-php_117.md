# PHP preg_filter()：执行一个正则表达式的搜索和替换

> 原文：[`c.biancheng.net/view/6250.html`](http://c.biancheng.net/view/6250.html)

PHP preg_filter() 函数也用于执行一个正则表达式的搜索和替换，等价于我们前面介绍的《preg_replace() 函数》，不同的是 preg_filter() 函数只返回匹配成功的结果，而 preg_replace() 返回所有结果，不管是否匹配成功。

关于 preg_filter() 的工作原理和参数说明请参考 preg_replace() 函数。

preg_filter() 函数的使用示例如下：

```

<?php
    echo "<pre>";
    $arr1 = array(
        'http://c.biancheng.net/php/',
        'http://c.biancheng.net/java/',
        'http://c.biancheng.net/python/',
        'http://www.google.com/cn/',
        'http://cn.bing.com/',
    );
    $pattern = array('/c\./','/biancheng/');
    $replacement = array('www.','baidu');
    print_r( preg_replace($pattern, $replacement, $arr1) );
    print_r( preg_filter($pattern, $replacement, $arr1) );
?>
```

执行以上程序的结果如下：

Array
(
    [0] => http://www.baidu.net/php/
    [1] => http://www.baidu.net/java/
    [2] => http://www.baidu.net/python/
    [3] => http://www.google.com/cn/
    [4] => http://cn.bing.com/
)
Array
(
    [0] => http://www.baidu.net/php/
    [1] => http://www.baidu.net/java/
    [2] => http://www.baidu.net/python/
)