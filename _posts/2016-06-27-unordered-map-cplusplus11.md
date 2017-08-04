---
layout: post
title:  "unordered_map & C++11配置"
date:   2016-06-27 22:30:00
categories: CPP
---

# unordered_map

- map是基于红黑树实现的，查找时间O(logn);
- hash_map是基于hash表实现的，查找时间O(1).

**hash_map是STL的一部分，但不属于C++ Standard Library.**

Quoting Wikipedia (emphasis added):
From the [STL](http://en.wikipedia.org/wiki/Standard_Template_Library) page:

> The Standard Template Library (STL) is a software library **partially** included in the C++ Standard Library.

…and then from the [hash_map](http://en.wikipedia.org/wiki/Hash_map_%28C++%29) page:

> In the C++ programming language, `hash_map` **is the name of a hashed associative container in the Standard Template Library**. It is provided by several implementors, such as the GNU C++ compiler and Microsoft’s Visual C++. **It is not part of the C++ Standard Library**, but the C++ Technical Report 1 contains the very similar container `unordered_map`, which will be included in the upcoming C++0x standard.

参考：[stackoverflow](http://stackoverflow.com/questions/16136142/c11-functionality-with-mingw)

由于之前的#include <hash_map>已过时，被#include <unordered_map>代替。<!-- more -->

正确用法：

```cpp
#include <unordered_map>
 
int main(){
    unordered_map<string, int> hashMap;
    hashMap["One"] = 1;
    hashMap["Two"] = 2;
    hashMap["Three"] = 3;
 
    unordered_map<string, int>::iterator it = hashMap.find("Two");
    if(it != hashMap.end()){
        cout << it->first << " = " << it->second << endl;
    }
    return 0;
}
```

编译：
Error：This file requires compiler and library support for the ISO C++ 2011 standard. This support is currently experimental, and must be enabled with the -std=c++11 or -std=gnu++11 compiler options.

这是由于该函数需要C++11的支持.
编译时加上参数 **– std=c++11**。如：

g++ -std=c++11 xxx.cpp

# 编辑器的配置

【codeblocks】
Settings->Compiler…->勾选”Have g++ follow the C++11 ISO language standard [-std=c++11]”.

【sublime】
Tools->Build System->New Build System…

(下面配置内容可参考sublime text安装路径/Packages目录下的 C++.sublime-package 配置文件来修改)

```
{
    "shell_cmd": "g++ -std=c++11 \"${file}\" -o \"${file_path}/${file_base_name}\"",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "source.c, source.c++",
    
    "variants":
    [
        {
            "name": "Run",
            // for Linux
            // "cmd": ["bash", "-c", "g++ -std=c++11 '${file}' -o '${file_path}/${file_base_name}' && '${file_path}/${file_base_name}'"]
            // for Windows
            "shell_cmd": "g++ -std=c++11 \"${file}\" -o \"${file_path}/${file_base_name}\" && \"${file_path}/${file_base_name}\""
        }
    ]
}
```

最后保存为：C++11.sublime-build。并在编译时选择C++11。