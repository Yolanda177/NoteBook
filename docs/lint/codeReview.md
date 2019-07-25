# 错误列表

## 数据引用错误

## 数据声明错误

## 运算错误

## 比较错误

## 控制流程错误

## 接口错误

## 输入/输出错误

- 如果对文件明确声明过，其属性是否正确？
- 打开文件中的语句中各项属性的设置是否正确？
- 格式规范中是否与I/O语句中的信息相吻合？举例来说，在FORTRAN语言中，是否每个 FORMAT 语句都与相应的 READ 或 WRITE 语句相一致（就各项的数量和属性而言）？
- 是否有足够的可用内存空间，来保留程序将读取的文件？
- 是否所有的文件在使用之前都打开了？（undefined）
- 是否所有的文件在使用之后都关闭了？（内存泄漏）
- 是否判断文件结束的条件，并正确处理？
- 对I/O出错情况处理是否正确？
- 任何打印或显示的文本信息中是否存在拼写或语法错误？
- 程序是否正确处理类似于“File Not Found” 这样的错误？

## 其他检查