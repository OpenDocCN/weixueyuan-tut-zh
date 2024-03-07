# 正则表达式匹配字母 大写字母 小写字母 大小写字母 数字和字母组

匹配特定字符串：
只能输入长度为 3 的字符："^.{3}$"。
只能输入由 26 个英文字母组成的字符串："^[A-Za-z]+$"。
只能输入由 26 个大写英文字母组成的字符串："^[A-Z]+$"。
只能输入由 26 个小写英文字母组成的字符串："^[a-z]+$"。
只能输入由数字和 26 个英文字母组成的字符串："^[A-Za-z0-9]+$"。
只能输入由数字、26 个英文字母或者下划线组成的字符串："^\w+$"。