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

## 0x03 一个简单的Makefile文件

```makefile
helloworld: helloworld.c
	gcc -o helloworld helloworld.o
```

运行`make`命令得到结果：

![image-20230421084134500](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230421084134500.png)

在这里，我们可以发现这个makefile不就是shell脚本，看起来和简单的shell脚本没有什么区别，确实，简单的makefile文件确实可以当成shell脚本执行，但是我们还是看不懂大神写的makefile文件，下面下面我们深入分析一下Makefile文件的其他写法

## 0x04 makefile的结构说明

Makefile里主要包含了五个东西：变量定义、显式规则、隐晦规则、文件指示和注释。

1、变量的定义。在Makefile中我们要定义一系列的变量，变量一般都是字符串，这个有点像C语言中的宏，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上。

2、显式规则。显式规则说明了，如何生成一个或多的的目标文件。这是由Makefile的书写者明显指出，要生成的文件，文件的依赖文件，生成的命令。 刚才写的疑似shell脚本的Makefile全部都是显示规则。

3、隐晦规则。由于我们的make有自动推导的功能，所以隐晦的规则可以让我们比较粗糙地简略地书写Makefile，这是由make所支持的。

4、文件指示。其包括了三个部分，一个是在一个Makefile中引用另一个Makefile，就像C语言中的include一样。

5、注释。Makefile中只有行注释，和UNIX的Shell脚本一样，其注释是用“#”字符

当我们重新编译一个工程的时候，make决定哪个文件需要重新编译，哪个文件不需要重新编译，Makefile文件来告诉make该如何运行这些杂乱无章的命令。当Make重新编译这个编辑器时，所有改动的C语言源文件必须重新编译。如果一个头文件改变，每一个包含该头文件的C语言源文件必须重新编译，这样才能保证生成的编辑器是所有源文件更新后的编辑器。每一个C语言源文件编译后产生一个对应的OBJ文件，如果一个源文件重新编译，所有的OBJ文件无论是刚刚编译得到的或原来编译得到的必须从新连接，形成一个新的可执行文件。

具体规则请查看[gnu make 官方文档](https://www.gnu.org/software/make/manual/make.html)

## 0x05 makefile例子

使用makefile编译如下工程，工程结构如下图：

![image-20230421092454282](https://tuchuang-e682.obs.cn-north-1.myhuaweicloud.com/image-20230421092454282.png)



## 参考文档

1. [GCC 概述：C 语言编译过程详解](https://iamazing.cn/page/GCC-%E6%A6%82%E8%BF%B0%EF%BC%9AC-%E8%AF%AD%E8%A8%80%E7%BC%96%E8%AF%91%E8%BF%87%E7%A8%8B%E8%AF%A6%E8%A7%A3)
2. [一个简单的 make & makefile 教程](https://zhuanlan.zhihu.com/p/92010728)
3. [Makefile由浅入深--教程、干货](https://zhuanlan.zhihu.com/p/47390641)
3. 

