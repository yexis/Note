```python
// Python replace() 方法把字符串中的 old（旧字符串） 替换成 new(新字符串)，如果指定第三个参数max，则替换不超过 max 次。
// 返回字符串中的 old（旧字符串） 替换成 new(新字符串)后生成的新字符串
str.replace(old, new[, max])

// eg.
str = "this is string example....wow!!! this is really string";
print str.replace("is", "was");
print str.replace("is", "was", 3);
```