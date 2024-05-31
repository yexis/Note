##### string 转 int

```cpp
// C语言风格
std::string str;
int i = atoi(str.c_str());

// C++风格
std::string str;
int i = std::stoi(str);
// 转其他类型
// stol(long), stof(float), stod(double)
```



##### split

```cpp
// 代码1 : 使用stringstream + getline
class Solution {
public:
    vector<string> isCircularSentence(string s) {
        stringstream ss(s);
        string t;
        vector<string> strs;
        while(getline(ss, t, ' ')) {
            strs.push_back(t);
        }
        return strs;
    }
};
// 代码2 : stringstream
class Solution {
public:
    bool isCircularSentence(string s) {
        stringstream ssin(s);
        vector<string> strs;
        string str;
        while(ssin >> str) {
            strs.push_back(str);
        }
        return true;
    }
};
```



##### 判断字符是否是数字 

```cpp
// 注意是字符 char
bool check(char s) {
  return is_digit(c);
}
// 等价于
bool check(char c) {
  return c >= '0' && c <= '9';
}
```

