# JSP EL 点（.）运算符和下标（[]）运算符

EL 提供“.”（点操作）和 [] 两种运算符来实现数据存取运算。“.”（点操作）和 [] 是等价的，可以相互替换。例如，下面两者所代表的意思是一样的。

${sessionScope.user.sex}

等价于

${sesionScope.user["sex"]}

但是，需要保证要取得对象的那个属性有相应的 setXxx() 和 getXxx() 方法才行。

有时，“.”和 [] 也可以混合使用，例如：

${sessionScope.shoppingCart[0].price}

#### 提示：

注意下面两种情况，“.”（点操作）和 [] 不能互换。

(1) 当要存取的数择名称中包含不是字母或数字的特殊字符时，只能使用 []。例如：

${sessionScope.user.["user-sex"]}

不能写成

${sessionScope.user.usis-sex}

(2) 当取得的数据为动态值时，只能使用 []。例如：

${sessionScope.user[param]}

其中，param 是自定义的曼量，其值可以是 user 对象的 name、sex、age 等。