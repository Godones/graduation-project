# Sqlite移植



数据库的操作系统接口部分实现了一个操作系统抽象层，为兼容多种操作系统平台而设计。其主要职责是负责实现操作系统对数据库的文件管理，包括对文件的存取、共享、保护和一致性控制等功能[19]。SQLite 的操作系统接口由 Mutex（互斥信号量）子系统、内存分配子系统和 VFS（虚拟文件系统）子系统三部分组成[



## Mutex子系统

1. 存在多个线程访问sqlite的应用程序才需要互斥子系统
2. 单线程应用可以直接禁用互斥子系统

```
关闭互斥子系统: -DSQLITE_THREADSAFE=0
```

在新的操作系统中，需要按照下列步骤完成:

1. 设置编译选项

   ```
   -DSQLITE_MUTEX_APPDEF=1
   ```

2. 完成下列函数的重写

   ```c
   sqlite3_mutex *sqlite3_mutex_alloc(int);
   void sqlite3_mutex_free(sqlite3_mutex*);
   void sqlite3_mutex_enter(sqlite3_mutex*);
   int sqlite3_mutex_try(sqlite3_mutex*);
   void sqlite3_mutex_leave(sqlite3_mutex*);
   ```



## 内存分配子系统

默认情况下使用标准库的`malloc`/`free`

需要完成下列函数的替换:

```
typedef struct sqlite3_mem_methods sqlite3_mem_methods;
struct sqlite3_mem_methods {
  void *(*xMalloc)(int);         /* Memory allocation function */
  void (*xFree)(void*);          /* Free a prior allocation */
  void *(*xRealloc)(void*,int);  /* Resize an allocation */
  int (*xSize)(void*);           /* Return the size of an allocation */
  int (*xRoundup)(int);          /* Round up request size to allocation size */
  int (*xInit)(void*);           /* Initialize the memory allocator */
  void (*xShutdown)(void*);      /* Deinitialize the memory allocator */
  void *pAppData;                /* Argument to xInit() and xShutdown() */
};

```



## 虚拟文件系统

























## 参考连接

[SQLite OS接口或“ VFS” 本文介绍了跨操作系统提供的实现堆栈底部的 SQLite OS 可移植层“VFS”模块。 (runebook.dev)](https://runebook.dev/zh-CN/docs/sqlite/vfs)

[SQLite下载、编译和使用-3 - 简书 (jianshu.com)](https://www.jianshu.com/p/2531a83f8ec0)

https://www.jianshu.com/p/2531a83f8ec0

[(78条消息) STM32F429移植SQLITE记录_oMurphyo的博客-CSDN博客_sqlite freertos](https://blog.csdn.net/weixin_38728721/article/details/106635464)

