# 1.【第一步】XML\YAML文件的读写操作

（1） 准备文件写操作

FileStorageshi opencv中XML和UYAML文件的存储类，有连个构造函数。

```c++
FileStorage::FileStorage();
FileStorage::FileStorage(const string& source, int flages, const string& encoding=string());

//对于第二种带参数的构造函数，进行写操作如下
FileStorage fs("abc.yaml",FileStorage::WRITE);

//对于第一种不带参数的构造函数，可以试用期成员函数 FileStorage::open 进行数据的而写操作，如下：
FileStorage fs;
fs.open("abc.yaml",FileStorage::WRITE);
```

(2) 准备文件读操作

用标识符 FileStorage::RADE 即可

```c++
FileStorage fs("abc.yaml",FileStorage::RADE);

//或者
FileStorage fs;
fs.open("abc.yaml",FileStorage::RADE);
```

# 2. 【第二步】进行文件读写操作

（1） 文本和数字的输入输出

```c++
//写入文件使用 “ << ” 运算符，如下：
fs << "iterationNr" << 100;

//读取文件使用 “ >> ” 运算符
int itNr;
fs["iterationNr"] >> itNr;
itNr = (int) fs["itarationNr"];
```

(2) opencv数据结构的输入输出

同上

```c++
Mat R,T;
//向Mat中写入数据
fs << "R" << R;
fs << "T" << T;

//从Mat中读取数据
fs["R"] >> R;
fs["T"] >> T;
```

(3) 【第三步】vector（arrays）和maps的输入输出

(4) 【第四步】文件关闭

```c++
fs.release();
```

