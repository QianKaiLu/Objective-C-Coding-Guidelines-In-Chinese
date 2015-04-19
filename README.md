# Objective-C-Coding-Guidelines-In-Chinese
Objective-C编码规范，内容来自苹果、谷歌的文档翻译，自己的编码经验和其它资料的总结。

##命名的基本原则
###清晰
命名应该尽可能的清晰和简洁，但在Objective-C中，清晰比简洁更重要。由于XCode强大的自动补全功能，我们不必担心名称过长的问题。
```objective-c
//清晰
insertObject:atIndex:

//不清晰，insert的对象类型和at的位置属性没有说明
insert:at:

//清晰
removeObjectAtIndex:

//不清晰，remove的对象类型没有说明，参数的作用没有说明
remove:
```

不要使用单词的简写，拼写出完整的单词：
```objective-c
//清晰
destinationSelection
setBackgroundColor:

//不清晰，不要使用简写
destSel
setBkgdColor:
```