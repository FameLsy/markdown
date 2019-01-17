# 判断一个特定的值是否等于Double.NaN
```java
if(Double.isNaN(x))
```

# Unicode与char类型，如何解决无法满足Unicode字符的需要
Unicode字符超过了65536个，16位char类型无法满足  
**java如何解决**  
*码点（code point）*：编码表中的某个字符对应的代码值。unicode码点采用十六进制书写，并加上前缀 *U+*,如 *U+ 0041*,代表 *A*  
Unicode码点可以分成17个代码级别(code plane),第一个代码级别为基本多语言级别(basic multilingual plane)，码点从 *U+ 0000*到 *U+ FFFF*，包括经典的Unicode代码；其余16个级别码点从 *U+ 10000* 到 *U+ 10FFFF*,其中包括一些辅助字符。  

