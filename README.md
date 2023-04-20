# Linux下C项目组织与编译食用指南

## 0x00 本文环境及工具链版本

**系统环境**：ubuntu 22.10 Desktop 

![image-20230420234329403](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230420234329403.png)

gcc版本：12.2.0

![image-20230420234418395](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230420234418395.png)

make版本：4.3

![image-20230420234501736](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230420234501907.png)

cmake版本：3.24.2

![image-20230420234536331](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230420234536331.png)

## 0x01 前置知识GCC编译

### gcc编译程序的过程：

![image.png](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/Ci8uNg6fv9cOR3x.png)

关于gcc的安装：`sudo apt install gcc -y`

linux下可以使用`man gcc`来查看帮助手册



![image-20230420235610903](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230420235610903.png)

### gcc编译流程

1. 预处理（pre-processing）：`cpp hello.c > hello.i`，将引入的头文件（#include）和定义的宏（#define）扩展到代码中

   ![image-20230421000103177](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230421000103177.png)

   ![image-20230421000118514](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230421000118514.png)

2. 编译（compilation）：`gcc -S hello.i`，将预处理过的文件编译成汇编程序

   ![image-20230421000234895](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230421000234895.png)

3. 汇编（assembly）：`as -o hello.o hello.s`，将汇编程序转换成目标文件，二进制格式，每一个源文件都需要产生一个目标文件

4. 链接（linker）：`ld -o hello.exe hello.o ...libraries...`，将一个或多个目标文件连接成最终的可执行文件

   ```bash
   ➜  helloworld git:(master) ✗ ld -dynamic-linker /lib64/ld-linux-x86-64.so.2 /usr/lib/x86_64-linux-gnu/crt1.o /usr/lib/x86_64-linux-gnu/crti.o -lc helloworld.o /usr/lib/x86_64-linux-gnu/crtn.o -o helloworld
   ➜  helloworld git:(master) ✗ ./helloworld 
   ➜  helloworld git:(master) ✗ 
   
   ```

   这个指令将目标文件`helloworld.o`与启动代码文件`crt1.o`、C运行时库文件`libc.so.6`以及结束代码文件`crtn.o`链接成可执行文件`helloworld`。`-dynamic-linker`选项指定了动态链接器的路径，`-lc`选项指定了需要链接的C运行时库文件。

5. 运行程序：`./helloworld`

   ![image-20230421003523614](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230421003523614.png)

   ![image-20230421003607245](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230421003607245.png)

   

## 0x02 什么是makefile

`Makefile`是一个文本文件，其中包含一组指令，用于自动化构建和编译程序。`Makefile`通过定义程序的依赖关系，可以自动化构建程序的目标文件和可执行文件，而不需要手动执行一系列编译指令。

在`Makefile`中，你可以定义编译器的选项、编译器的指令、源文件、目标文件等内容。`Makefile`可以根据文件的修改时间判断是否需要重新编译文件，并自动执行所需的编译指令。

## 0x03 引入一个简单的工程



## 参考文档

1. [GCC 概述：C 语言编译过程详解](https://iamazing.cn/page/GCC-%E6%A6%82%E8%BF%B0%EF%BC%9AC-%E8%AF%AD%E8%A8%80%E7%BC%96%E8%AF%91%E8%BF%87%E7%A8%8B%E8%AF%A6%E8%A7%A3)
2. [一个简单的 make & makefile 教程](https://zhuanlan.zhihu.com/p/92010728)
3. 

