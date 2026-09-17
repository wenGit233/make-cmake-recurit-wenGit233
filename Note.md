# 笔记

## 是什么

* **构建系统**：将源代码转化为机器可执行文件（目标），编译器仅操作单个文件，构建系统自动化处理大量编译任务
* **CMake**是构建系统生成工具，读取 Makefile 文件，根据文件时间戳(改动时间)判断哪些文件需要重新编译。
*
## 记录makefile语法

* 给机器的指令前要加tab
* CC=gcc（定义变量，取别名？）使用 $(CC) 使用or读取变量
* 目标：比如 calculator、clean
* 依赖：`目标：依赖文件`目标文件比依赖旧，则执行下面命令
* `$<`:第一个依赖文件
* `$@ `：自动变量，代表当前目标
* `%.o: %.c`:任意.c 文件生成同名.o 文件
* `.PHONY`:伪目标。clean不是真实文件,`.PHONY`防止目录下有文件名叫clean。


```bash
# 给编译器起别名
CC = gcc
#-Wall：基础警告；-Wextra：额外警告；-Iinclude：告诉编译器去include文件夹找头文件
CFLAGS = -Wall -Wextra -Iinclude
#目标：calculator（要生成的可执行程序
#依赖：main.o calculator.o logger.o，这三个中有比calculator新就执行，好聪明的办法
calculator: main.o calculator.o logger.o
	$(CC) $(CFLAGS) main.o calculator.o logger.o -o calculator
# 目标：main.o
# 依赖：src/main.c,include/calculator.h头文件、include/logger.h头文件
# 要写从makefile到该文件的完整路径
# 含义：如果 src/main.c 或者它依赖的.h头文件被修改（时间戳更新），就重新生成 main.o
# -c 参数：只做预处理+编译+汇编，不做链接*，输出目标文件 .o
main.o: src/main.c include/calculator.h include/logger.h #-c只编译不链接
	$(CC) $(CFLAGS) -c src/main.c -o main.o
# 同上，如果.c或者.h改动，就重新编译生成 calculator.o
calculator.o: src/calculator.c include/calculator.h
	$(CC) $(CFLAGS) -c src/calculator.c -o calculator.o

logger.o: src/logger.c include/logger.h
	$(CC) $(CFLAGS) -c  src/logger.c -o logger.o

.PHONY: clean
clean:
	rm main.o calculator.o logger.o
```
>从根部至分支，检查时间戳再执行的好处：遇到新的->检查其底下有没有新的->一路检查再从下往上执行,这样不需要改的就会被跳过提高了效率