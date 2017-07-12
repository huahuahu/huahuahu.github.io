---
layout: wiki  
title: LLDB 和Chisel 使用例子  
categories: tools  
keywords: lldb  
---
## 打印变量  
1. 打印数字  
		
		(lldb) p/d 16 
		16
2. 16 进制格式

		(lldb) p/x 16
		0x10
3. 2 进制格式

		(lldb) p/t 16
		0b00000000000000000000000000010000
		(lldb) p/t (char)16
		0b00010000

## 声明变量  

	(lldb) e int $a = 2
	(lldb) p $a * 19
	38
	(lldb) e NSArray *$array = @[ @"Saturday", @"Sunday", @"Monday" ]
	(lldb) p [$array count]
	2
		
## 流程控制  
1. continue  
	`continue` 或 `c`
2.  step over，执行一行代码  
	`next` 或 `n`  
3. step into   
	`step` 或 `s`  
4. step out  
	`thread step-out` 或 `finish`  
5. 从当前函数返回  
	`thread return`

## 管理断点
1. 查看所有断点    

		(lldb) br list
		Current breakpoints:
		1: file = '/testStringIndex/testStringIndex/ViewController.m', line = 73, exact_match = 0, locations = 1, resolved = 1, hit count = 1

		  1.1: where = testStringIndex`-[ViewController configureSections] + 261 at ViewController.m:73, address = 0x000000010de75515, resolved, hit count = 1 

		2: file = '/testStringIndex/testStringIndex/ViewController.m', line = 31, exact_match = 0, locations = 1, resolved = 1, hit count = 1

		  2.1: where = testStringIndex`-[ViewController viewDidLoad] + 701 at ViewController.m:31, address = 0x000000010de74f3d, resolved, hit count = 1 
2. disable 断点  
	
		(lldb) br disable 2.1
		1 breakpoints disabled.
3. 删除断点  

		(lldb) br delete 1.1
		0 breakpoints deleted; 1 breakpoint locations disabled.
	
## 打断点    
1. 指定文件指定行数打断点  
	
		(lldb) br set  -f ViewController.m -l 76
		Breakpoint 2: where = testStringIndex`-[ViewController configureSections] + 451 at ViewController.m:77, address = 0x000000010eff15d3
		(lldb) b ViewController.m:79
		Breakpoint 3: where = testStringIndex`-[ViewController configureSections] + 526 at ViewController.m:79, address = 0x000000010eff161e
1. 在符号（C语言函数）上打断点  
	
		(lldb) b isEven
		Breakpoint 3: where = DebuggerDance`isEven + 16 at main.m:4, address = 0x000000010a3f6d00
		(lldb) br s -F isEven
		Breakpoint 4: where = DebuggerDance`isEven + 16 at main.m:4, address = 0x000000010a3f6d00
1. 在 OC 函数上打断点  
		
		(lldb) breakpoint set -F "-[NSArray objectAtIndex:]"

		Breakpoint 4: where = CoreFoundation`-[NSArray objectAtIndex:], address = 0x000000010fbb7570
		Breakpoint 5: where = CoreFoundation`-[NSArray objectAtIndex:], address = 0x000000010fbb7570
		(lldb) b -[NSArray objectAtIndex:]

		Breakpoint 6: where = CoreFoundation`-[NSArray objectAtIndex:], address = 0x000000010fbb7570
		Breakpoint 7: where = CoreFoundation`-[NSArray objectAtIndex:], address = 0x000000010fbb7570
		(lldb) breakpoint set -F "+[NSSet setWithObject:]"

		Breakpoint 8: where = CoreFoundation`+[NSSet setWithObject:], address = 0x000000010fadd5d0
		Breakpoint 9: where = CoreFoundation`+[NSSet setWithObject:], address = 0x000000010fadd5d0
		(lldb) b +[NSSet setWithObject:]

		Breakpoint 10: where = CoreFoundation`+[NSSet setWithObject:], address = 0x000000010fadd5d0
		Breakpoint 11: where = CoreFoundation`+[NSSet setWithObject:], address = 0x000000010fadd5d0

## 更新界面  
在 `Chisel` 中有一个命令，叫做 `caflush`，来渲染。

	
	(lldb) e (void)[$myView setBackgroundColor:[UIColor blueColor]]
	(lldb) e (void)[CATransaction flush] //渲染
## Chisel 的命令  
1. 非重写方法的符号断点

		(lldb) bmessage -[MyViewController viewDidAppear:]
		Setting a breakpoint at -[UIViewController viewDidAppear:] with condition (void*)object_getClass((id)$rdi) == 0x000000010e2f4d28
		Breakpoint 1: where = UIKit`-[UIViewController viewDidAppear:], address = 0x000000010e11533c
 
 `MyViewController` 并没有实现这个方法。
 
 
	> (lldb) help bmessage
  For more information run 'help bmessage'  Expects 'raw' input (see 'help
     raw-input'.)

	> Syntax: bmessage
Set a breakpoint for a selector on a class, even if the class itself doesn't
override that selector. It walks the hierarchy until it finds a class that does
implement the selector and sets a conditional breakpoint there.

	> Arguments:
  <expression>; Type: string; Expression to set a breakpoint on, e.g. "-[MyView
  setFrame:]", "+[MyView awesomeClassMethod]" or "-[0xabcd1234 setFrame:]"

 
## 其他  
1. 当前行数和源码  
	
		(lldb) frame info
		frame #0: 0x000000010704e515 testStringIndex`-[ViewController configureSections](self=0x00007fb3c8006160, _cmd="configureSections") at ViewController.m:73
2.  查看内存  

		(lldb) x/4c $str
		0x7fd04a900040: monk

			



