# encape、encodeURI和encodeURIComponent的总结

# 区别点

## 特殊符号是否编码

1. escape: 不编码ASCII字母、数字、标点符号"@ * _ + - . /"，对其他所有字符进行编码。
2. encodeURI: 不编码"; / ? : @ & = + $ , #",对其他所有字符进行编码。
3. encodeURIComponent: 对所有字符进行编码

## 转码结果

1. escape: 输出字符的Unicode编码值
2. encodeURI: 输出符号的utf-8形式，并且在每个字节前加上%
3. encodeURIComponent: 输出符号的utf-8形式，并且在每个字节前加上%

tips:

1. 但是我们知道，网页在提交表单的时候，如果有空格，则会被转化为+字符。服务器处理数据的时候，会把+号处理成空格。