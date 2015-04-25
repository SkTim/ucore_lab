# Lab 4 Report

## Exercise 0

使用 meld 完成半自动移植。

## Exercise 1

### 实验步骤

这个练习的主要任务是初始化 PCB 内部的各数据段。

### 思考题

> 请说明 struct context 和 struct trapframe 成员变量的含义与作用。

context 储存的是进程的上下文，即各个寄存器的值：

```
struct context {
    uint32_t eip;
    uint32_t esp;
    uint32_t ebx;
    uint32_t ecx;
    uint32_t edx;
    uint32_t esi;
    uint32_t edi;
    uint32_t ebp;
};
```

trapframe 储存的是中断发生前栈帧的信息：

```
struct trapframe {
    struct pushregs tf_regs;
    uint16_t tf_gs;
    uint16_t tf_padding0;
    uint16_t tf_fs;
    uint16_t tf_padding1;
    uint16_t tf_es;
    uint16_t tf_padding2;
    uint16_t tf_ds;
    uint16_t tf_padding3;
    uint32_t tf_trapno;
    /* below here defined by x86 hardware */
    uint32_t tf_err;
    uintptr_t tf_eip;
    uint16_t tf_cs;
    uint16_t tf_padding4;
    uint32_t tf_eflags;
    /* below here only when crossing rings, such as from user to kernel */
    uintptr_t tf_esp;
    uint16_t tf_ss;
    uint16_t tf_padding5;
} __attribute__((packed));
```

## Exercise 2

### 实验步骤

填写do\_fork()函数。首先使用alloc\_proc()分配以及初始化一个新进程，并将其parent设为当前进程。然后为进程分配栈，然后复制原有进程的执行状态。然后将PCB加入hash表和proc\_list，最后将新进程设置为runable 。

### 思考题

> 是否能做到给每个新fork的线程一个唯一的id？说明理由。

get_pid()函数可以保证每次分配不同的PID。get\_pid将pid 每次加一直到找到可用的PID返回。如果进程数等于MAX\_PROCESS，从而没有PID可以分配，会不断进入repeat直到其中某个进程退出。从而fork出来的每个进程的PID都是唯一的。

## Exercise 3

### 思考题

> 分析proc_run()并回答以下问题：  

> 创建运行了几个内核线程？

两个，分别是idle和init。

> local_intr_save和local_intr_restore在这里有何作用？

local\_intr\_save用于关闭interrupt requestlocal\_intr\_restore用于恢复interrupt request。
