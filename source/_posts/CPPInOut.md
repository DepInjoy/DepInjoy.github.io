---
layout: post
title: C++输入输出和模板
date: 2018-3-22 18:04
category: CPP
tags: [CPP]

description:  。

---

首先，将标准的输入和输出重定向至文件示例：

```C++
#include <iostream>
#include <string>
int main()
{
	std::string name;
	FILE *stream;
	//Standard input redirected to file
	freopen_s(&stream, "in.txt", "r", stdin);		//Mary
	std::cin >> name;

	//Standard output redirected to file
	freopen_s(&stream, "out.txt", "w", stdout);
	if (name == "q" || name == "Q")
		std::cerr << "Incorrect input" << std::endl;
	else
		std::cout << name << std::endl;				//Mary
	std::cout << "The application will be exited !" << std::endl;
	
	return 0;
}
```

## 流操纵算子

- 整数流操纵算子：十进制、八进制、16进制(setbase)
- 浮点数精度(precision,setprecision)
- 设置域宽(setw,width)
- 自定义流操作算子

```C++
#include <iostream>
#include <iomanip>
//自定义tab流操作算子
std::ostream &tab(std::ostream& out){
	return out << "\t";
}
int main()
{
	int a = 10;
	std::cout << std::oct << a << std::endl;
	std::cout << std::dec << a << std::endl;
	std::cout << std::hex << a << std::endl;

	double b = 1234567.89, c = 12.34567;
	//1.23457e+006 12.3457 a 默认非定点方式
	std::cout << std::setprecision(6) << b << std::endl << c << std::endl << a << std::endl << std::endl;
	//1234567.890000 12.345670 定点方式
	std::cout << std::setiosflags(std::ios::fixed) << std::setprecision(6) << b << std::endl << c << std::endl << a << std::endl << std::endl;
	
	std::cout << a << tab << b << std::endl;
	system("pause");
	return 0;
}
```

##文本文件读写

下面的这个示例将inTest.txt中的数字按照从小到大的顺序排列并重定向到outTest.txt文件。

```C++
#include <iostream>
#include <fstream>
#include <vector>
#include <algorithm>
using namespace std;
int main(int argc, char* argv[])
{
	vector<int> v;
	ifstream srcFile("inTest.txt", ios::in);	//1 89 32 25 72 45 2 9 82
	ofstream dstFile("outTest.txt", ios::out);
	if (!srcFile){
		cout << "File for input is not exist!";
		return -1;
	}
	int x = -1;
	while (srcFile >> x)
		v.push_back(x);
	sort(v.begin(), v.end());
	for (int i = 0; i < v.size(); i++)
		dstFile << v[i] << " ";
	srcFile.close();
	dstFile.close();						//1 2 9 25 32 45 72 82 89 
	return 0;
}
```

获取输入文件的长度

```C++
	srcFile.open("inTest.txt", ios::in);
	srcFile.seekg(0, ios::end);							//seek file's pointer to end
	int inCurPos = srcFile.tellg();
	cout << "Input file's size is " << inCurPos << endl;
```

获取输出文件的长度

```C++
	srcFile.open("inTest.txt", ios::in);
	dstFile.seekp(0, ios::end);							//seek file's pointer to end
	int outCurPos = dstFile.tellp();
	cout << "OutPut file's size is " << outCurPos << endl;
```

获取尾部的后4个字符

```C++
srcFile.seekg(-4, ios::end);
srcFile.read(readData, 4);
cout << readData[0] << readData[1] << readData[2] << readData[3] << endl;	//9 82
```

获取文件头部的4个字符

```C++
	char readData[4];
	srcFile.seekg(2, ios::beg);
	srcFile.read(readData, 4);
	cout << readData[0] << readData[1] << readData[2] << readData[3] << endl;	//89 3
```
## 二进制文件读写和复制
写入二进制文件
```C++
	int data = 8;
	ofstream fout("Test.bin", ios::out | ios::binary);
	fout.write((const char *)&data, sizeof(int));
	fout.close()
```
读取二进制文件
```C++
	int rData;
	ifstream fin("Test.bin", ios::in | ios::binary);//数字8
	fin.read((char*)&rData, sizeof(int));
	int rNum = fin.gcount();						//读入的字节数
	fin.close();
	cout << "read num: " << rNum << endl;			//4
	cout << rData << endl;						    //8
```
实现二进制文件的复制
```C++
	ifstream in("copyIn.bin", ios::in | ios::binary);
	if (!in) {
		cout << "open binary file failed !" << endl;
		in.close();
		return -1;
	}

	ofstream out("copyout.dat", ios::out | ios::binary);
	if (!out) {
		cout << "open binary for input failed !" << endl;
		out.close();
		return -1;
	}

	char c;
	while (in.get(c)){
		out.put(c);
	}
	in.close();
	out.close();
```






