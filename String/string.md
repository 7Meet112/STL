##### 一、概述
string是C++ 标准库的一个重要的部分，主要用于字符串处理。可以使用输入输出流方式直接进行string操作，也可以通过文件等手段进行string操作。同时，C++的算法库对string类也有着很好的支持，并且string类还和c语言的字符串之间有着良好的接口。
##### 二、使用
###### 2.1 构造与创建
```
// string();                  // 创建一个空的字符串 
string str;  
// string(const string& str); // 使用一个string对象初始化另一个string对象
string s1("ss");
// string(const char* s);     // 使用字符串s初始化
const char* s = "ss";
string s3(s);
// string(int n, char c);    // 使用n个字符c初始化 
string s2(2,'s');
```
###### 2.2 基本赋值操作
```
string& operator=(const char* s); 		// char*类型字符串 赋值给当前的字符串
string& operator=(const string &s); 	// 把字符串s赋给当前的字符串
string& operator=(char c); 				    // 字符赋值给当前的字符串
string& assign(const char *s); 			  // 把字符串s赋给当前的字符串
string& assign(const char *s, int n); // 把字符串s的前n个字符赋给当前的字符串
string& assign(const string &s); 		  // 把字符串s赋给当前字符串
string& assign(int n, char c); 			  // 用n个字符c赋给当前字符串
string& assign(const string &s, int start, int n); // 将s从start开始n个字符赋值给字符串
string s1 = "ss";
string s2 = "abc";
s1.assign(s2,0,2);
cout << s1 << endl;
```
###### 2.3 存取字符操作
```
char& operator[](int n);	// 通过[]方式取字符
char& at(int n);			    // 通过at方法获取字符
```
###### 2.4 拼接操作
```
string& operator+=(const string& str);	    // 重载+=操作符
string& operator+=(const char* str);	      // 重载+=操作符
string& operator+=(const char c);		        // 重载+=操作符
string& append(const char *s);			        // 把字符串s连接到当前字符串结尾
string& append(const char *s, int n);	      // 把字符串s的前n个字符连接到当前字符串结尾
string& append(const string &s);		        // 同operator+=()
string& append(const string &s, int pos, int n); // 把字符串s中从pos开始的n个字符连接到当前字符串结尾
string& append(int n, char c);			        // 在当前字符串结尾添加n个字符c
```

###### 2.5 查找和替换
```
int find(const string& str, int pos = 0) const; 	// 查找str第一次出现位置,从pos开始查找
int find(const char* s, int pos = 0) const;  		// 查找s第一次出现位置,从pos开始查找
int find(const char* s, int pos, int n) const;  	// 从pos位置查找s的前n个字符第一次位置
int find(const char c, int pos = 0) const;  		// 查找字符c第一次出现位置
int rfind(const string& str, int pos = npos) const;	// 查找str最后一次位置,从pos开始查找
int rfind(const char* s, int pos = npos) const;		// 查找s最后一次出现位置,从pos开始查找
int rfind(const char* s, int pos, int n) const;		// 从pos查找s的前n个字符最后一次位置
int rfind(const char c, int pos = 0) const; 		// 查找字符c最后一次出现位置
string& replace(int pos, int n, const string& str); // 替换从pos开始n个字符为字符串str
string& replace(int pos, int n, const char* s); 	// 替换从pos开始的n个字符为字符串s
```
###### 2.6 比较操作
compare函数在>时返回 1，<时返回 -1，==时返回 0。
比较区分大小写，比较时参考字典顺序，排越前面的越小。
大写的A比小写的a小。
```
int compare(const string &s) const;	// 与字符串s比较
int compare(const char *s) const;	// 与字符串s比较
```
###### 2.7 子串
```
string substr(int pos = 0, int n = npos) const; // 返回由pos开始的n个字符组成的字符串
```
###### 2.8 插入和删除操作
```
string& insert(int pos, const char* s); 	// 插入字符串
string& insert(int pos, const string& str); // 插入字符串
string& insert(int pos, int n, char c);		// 在指定位置插入n个字符c
string& erase(int pos, int n = npos);		// 删除从Pos开始的n个字符 
```
###### 2.9 string和c-style字符串转换
```
//string 转 char*
string str = "it";
const char* cstr = str.c_str();
//char* 转 string 
char* s = "it";
string str(s);
```
###### 2.10 用getline读取一整行
getline的函数格式：getline(cin,string对象)

getline的作用是读取一整行，直到遇到换行符才停止读取，期间能读取像空格、Tab等的空白符。

```
string s1;
getline(cin, s1);
cout << s1 << endl;

```
