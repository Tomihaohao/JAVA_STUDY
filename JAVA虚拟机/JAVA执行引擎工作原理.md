# 虚拟机

## 基本汇编基础
- 数据传送指令
    ```java
        //将自然数1传送到EAX寄存器
        movl 1,%eax
        //将栈顶数据弹出至EAX寄存器
        pop %eax
    ```
- 算术运算指令
    ```java
        //将自然数3与eax寄存器中的数据累加，并将结果存储进eax中
        add 3,%eax
        //对ebx 寄存器中的数增1
        inc %ebx
    ```
- 逻辑运算指令
    ```java
        //将eax中的数左移1个二进位
        shl %eax,1
        //对al寄存器中的数和操作数进行操作
        and al,00111011B
    ```
- 串指令
    连续空间分配，连续空间取值，传送等。
- 程序转移指令 if...else for while 函数调用等

## 理解基本汇编运算 步骤 汇编程序 1+2=3 具体过程
## 理解C语言 1+2=3 具体过程 

## JVM执行机器码
java 字节码指令直接对应一段特定逻辑的本地机器码，而JVM在解释执行JAVA字节码指令时，会直接调用字节码指令所对应的本地机器码，而JVM在解释执行JAVA字节码指令时，会直接调用字节码指令所对应的本地机器码。JVM要执行机器码就相当于用C C++ 语言直接执行机器码，这种技术的实现关键就是 指针

## 函数指针 与 指针函数
- 指针函数 就是一个一般的函数 返回类型是一个指针
- 函数指针 声明的是一个指针 指向一个函数的首地址

```c
//声明指针函数
int *add(int a,int b)
//声明函数指针
int (*addPointer)(int a,int b);
addPointer = add;
int c = (*addPointer)(a,b)
```
## CallStub 函数指针

### CallStub八个参数
    - link 连接器 在java函数的调用者与被调用者之间建立的联系
    - result_val_address 函数返回值地址
    - result_type 函数返回类型
    - method() JVM内部所表示的JAVA方法对象
    - entry_point
    - parameters() java方法入参集合
    - size_of_parameters() java方法的入参数量
    - CHECK 当前线程对象

JVM 首先调用 call_stub()函数，返回 _call_stub_entry,然后将这个_call_stub_entry 强制转换成CallStub这种自定义的函数指针类型，最终JVM调用这一函数指针，实现C程序到机器指令的过程

主要是要弄清楚 JVM 中 CallStub  call_stub例程 相关知识


