# K-RPA+VBS

## 基本概念

​	VBS（VBScript）是一种**脚本语言**，可以结合**KRPA**来对以下系统场景进行操作。

- Excel
- Word
- PPT
- SAP
- Notes邮箱
- Outlook邮箱

总结来说就是扩展KRPA中的可用函数，使得让我们操作以上系统场景更加灵活。

可以在Excel里面录制宏，然后打开录制的VBA脚本，转化为中文脚本，让我们在RPA中使用宏的操作。

### VBA脚本语言转中文脚本注意事项

1.双引号转单引号

2.空格转括号。

3.出现with,代表给该对象下面每个属性赋值

4.无视下划线

5.有:=情况下左弃右留

6.Table,cell用中括号

### 参考示例：

#### 转化前 

```vbscript
Columns("F:F").Select
    Selection.Delete Shift:=xlToLeft
```

#### 转化后

```java
//先拿到对象
activeSheet = App.ActiveSheet
//双引号转单引号
activeSheet.Columns('F:F').Select
//有:=情况下左弃右留-空格转括号-xlToLeft转化为常量值（可在文档中查看常量值和具体作用）
App.Selection.Delete(-4159)
```

## 官方文档

## Office Visual Basic for Applications (VBA) 参考 | Microsoft Docs](https://docs.microsoft.com/zh-cn/office/vba/api/overview/)