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
 > 先感受一下两种风格的效果
 <p>传统上的约定应该是__cdecl，这个约定是函数传递的参数，应该由调用方去清理，从汇编角度看，类似于：</p>

```
/* example of __cdecl */
push arg1
push arg2
push arg3
call function
add esp,12    ; effectively "pop; pop; pop"
```

 <p>__stdcall的约定风格是在Win32 API函数的标准约定，做法如下:</p>

```x86asm
/* example of __stdcall */
push arg1 
push arg2 
push arg3 
call function // no stack cleanup - callee does this
```
> 这样就有比较只管的感受了，从汇编码的角度来说，__stdcall把清理的职责归属到了callee，这样在大型项目中，可能会编译出更小的可执行程序。 另一方面，如果使用过C风格的变长参数便可以，虽然我们在语义层面上可以用类似于`argc`,`argv`的模式去明确参数的数量规则，但编译器层面去统一的约束这点是很困难的，所以__stdcall是不支持这种风格的边长参数的。

<br>这里给出win32流行的一些调用约定（实际跨平台项目我们一般会在非win环境定义对应的空白宏
- __stdcall, 以相反的顺序（从右到左）将参数压入堆栈
- __cdecl, 以相反的顺序（从右到左）将参数压入堆栈
- __clrcall, 按顺序（从左到右）将参数加载到 CLR 表达式堆栈中。
- __fastcall, 存储在寄存器中，然后压入堆栈
- __thiscall, 入栈；此指针存储在 ECX 中


#### __attribute__

1. visibility(default)
<p>可以用来定义一些符号是不是可见，可以用来发布api</p>