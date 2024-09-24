---
title: "note"
draft: true
categories:
    - unarchives
tags:
    - draft
---

## working note


#### __stdcall

1.definition
> __stdcall 调用约定用于调用 Win32 API 函数。 被调用方将清理堆栈，以便让编译器生成 vararg 函数 __cdecl。 使用此调用约定的函数需要一个函数原型。 __stdcall 修饰符是 Microsoft 专用的。

2.why
 <p>传统上的约定应该是`__cdecl`，这个约定是函数传递的参数，应该由调用方去清理，从汇编角度看，类似于：</p>
```x86asm
/* example of __cdecl */
push arg1
push arg2
push arg3
call function
add esp,12    ; effectively "pop; pop; pop"
```
