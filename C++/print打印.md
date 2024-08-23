##### 一 printf用法

```cpp
#include <cstdio>

int main() {
    // 整数
    int i = 42;
    unsigned int u = 42;
    printf("有符号整数: %d\n", i);
    printf("无符号整数: %u\n", u);
    printf("八进制: %o\n", u);
    printf("十六进制（小写）: %x\n", u);
    printf("十六进制（大写）: %X\n", u);

    // 浮点数
    float f = 3.14159;
    double d = 2.71828;
    printf("浮点数: %f\n", f);
    printf("双精度浮点数: %lf\n", d);
    printf("科学计数法（小写）: %e\n", d);
    printf("科学计数法（大写）: %E\n", d);

    // 字符和字符串
    char c = 'A';
    const char* str = "Hello, World!";
    printf("字符: %c\n", c);
    printf("字符串: %s\n", str);

    // 指针
    printf("指针: %p\n", &i);

    // 百分号
    printf("百分号: %%\n");

    return 0;
}
```





##### 二 printf "%.15lf"和"%15lf的区别"

```cpp
%.15lf
- `.`: 小数点标志，指示后面跟随的是小数部分的精度。
- `15`: 小数部分的精度，表示要打印 15 位小数。
- `lf`: 表示以 `double` 类型输出浮点数。
  
%15lf
15: 输出的最小字段宽度，表示输出的总字符数至少为 15 个字符。如果数字的字符数少于 15，则在左边填充空格。
lf: 表示以 double 类型输出浮点数。
```



