---
layout: post
title:  Swift Deinitialization
categories: swift
keywords: swift
---

用 `deinit` 来表示析构过程。只有类才有析构函数。  
## 析构函数工作原理  
- Swift 用 ARC 来管理类的实例变量。析构函数在实例变量销毁之前被调用。  
- 每一个类至多有一个析构函数。  
- 析构函数没有参数，也没有括号。    
        
        deinit {
            // perform the deinitialization
        }


- 不可以主动调用析构函数。  
- 父类的析构函数被子类继承。  
- 父类的析构函数在子类析构函数末尾被调用。  
- 由于析构函数结束后，实例变量才会被销毁，因此析构函数可以访问实例的所有属性。  


