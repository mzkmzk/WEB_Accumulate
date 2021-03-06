# ASCII、Unicode和UTF-8

# 简介

## ASCII码

ASCII码一共规定了128个字符的编码

一个ASCII占用8个bit(即一个字节),可以表示256种状态,但是ASCII最前面一位规定为0

具体的ASCII码转换表: https://zh.wikipedia.org/wiki/ASCII

## 非ASCII编码

例如主动的GBK2313编码,就属于非ASCII,因为很多国家128个编码并不够用,就自己折腾出一套转换表

例如GBK2313,一个字节只能表示256个字符,所以汉字需要两个字符(256x256=65535)个符号

但是GBK2313和后面的Unicode和UTF-8是毛关系都没有的

## Unicode

Unicode是一个很大的集合，现在的规模可以容纳100多万个符号

简单来说,Unicode想做一个全世界字符都包含的编码和字符对应的`约定`

但是Unicode没有规定应该如何存储,Unicode只声明了一种关系.

## Unicode-8

Unicode-8是Unicode的一种实现

UTF-8最大的一个特点，就是它是一种变长的编码方式。它可以使用1~4个字节表示一个符号，根据不同的符号而变化字节长度。

UTF-8的编码规则很简单，只有二条：

1. 对于单字节的符号，字节的第一位设为0，后面7位为这个符号的unicode码。因此对于英语字母，UTF-8编码和ASCII码是相同的。
2. 对于n字节的符号（n>1），第一个字节的前n位都设为1，第n+1位设为0，后面字节的前两位一律设为10。剩下的没有提及的二进制位，全部为这个符号的unicode码。

```javascript
Unicode符号范围 | UTF-8编码方式
(十六进制) | （二进制）
--------------------+---------------------------------------------
0000 0000-0000 007F | 0xxxxxxx
0000 0080-0000 07FF | 110xxxxx 10xxxxxx
0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx
0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
```

# 参考链接

1. http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html
