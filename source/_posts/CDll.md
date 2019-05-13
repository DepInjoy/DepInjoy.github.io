---
layout: post
title: 动态链接库
date: 2017-4-17 18:32
category: C
tags: [C]
description: 动态链接库的生成和链接方式。
---



### 共享库

- 减少每个可执行文件的长度，但增加了运行的时间开销

- 使得可执行文件不在需要包含公用的库函数，只需在所有的进程中都引用存储区域保存库例程的一个副本。

- 可以用库函数的新版本代替旧版本而无需对该库的函数重新进行编译。

- 动态库文件小，库在编时没有被编进目标代码，程序执行到相关函数时才调用函数库里的相应函数；静态库文件大，函数库所有数据都被整合进目标代码中。


#### 调用方式

##### 动态链接

``` c
//stime.c
#include <stdio.h>
#include <time.h>
#include <stdint.h>

/* get system time without considering user's change*/                                 uint64_t micro64(void)
{
    struct timespec t;
    clock_gettime(CLOCK_MONOTONIC,&t);
    return t.tv_sec * 1e+3 + t.tv_nsec / 1000;
}
```
``` c
#ifndef __STIME_H__
#define __STIME_H__
#include <stdint.h>

uint64_t micro64(void);                                                                                     

#endif
```

```c
#include <stdio.h>
#include "stime.h"

int main(int argc,char *argv[])                                                                                                                                                                              
{                    
    fprintf(stdout,"time %lu\n",micro64());
    return 0;        
}        
```

编译动态库：```gcc stime.c -o libstime.so -shared -fPIC``

编译可执行文件：```gcc link.c -L./ -lstime -o link```



###### 动态链接到动态库的方式

- 将库函数copy到/usr/lib或者/lib目录下

  ```shell
  sudo cp libstime.so  /usr/lib
  ./link
  ```

- 手动添加环境变量

  ```shell
  export LD_LIBRARY_PATH=./:$LD_LIBRARY_PATH  #--- 配环境变量  
  ./link
  ```

- 动态在安装在其他目录下

  ```shell
  cd /etc/ld.so.conf.d/
  sudo touch mylib.conf
  sudo vi mylib.conf
  cat mylib.conf
  sudo ldconfig
  cd 可执行文件所在路径
  ./link
  ```



##### 动态调用

```C
#include <unistd.h>
#include <stdint.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <dlfcn.h>
#include <sys/stat.h>
#include <setjmp.h>
#include "log/log.h"
#include "file/file.h"
#include "math/stime.h"
#include "math/ch_parity.h"

#define SHARE_PATH "./lib/libshare.so"

int main(int argc,char *argv[])
{
    //set compile time as version information
    fprintf(stdout,"vesion info %s\n",STR_COM_TIME);
    /* creat a file in specific path */
    s_mkdir("file",W_OK,S_IRWXU);

    /* denamic call .so */
    typedef uint64_t (*SHARE_FUN)(void);
    void * handle;
    SHARE_FUN func = NULL;

    if((handle = dlopen(SHARE_PATH,RTLD_LAZY)) == NULL){
        fprintf(stderr,"dlopen %s failed %s\n",SHARE_PATH,dlerror());
        return -1;
    }
    dlerror();//clear exsited error
    
    if((func = dlsym(handle,"micro64")) ==NULL){
        fprintf(stderr,"dlsym failed %s\n",dlerror());
        return -1;
    }
    fprintf(stdout,"time %llu\n",func());
    dlclose(handle);
    
    return 0;
}
```



#### 静态库的编译和链接

```shell
gcc -c stime.c  -o stime.o
ar -cr libstime.a stime.o
gcc -static link.c -L. -lstime -o link  // -static 强制链接静态库
./link
```



#### 查看动态库的相关命令

#####  nm：查看动/静态链接库中涉及到的符号

```shell
nm
其中，常见的三种符号:
	U - 库中被调用，但没有定义(表明需要其他库支持)
	T - 库中定义的函数
	W - “弱态”符号，在库中被定义，但可能被其他库中同名的符号覆盖
file - 识别文件的类型
```