---
layout: post
title: Objective C 枚举字符串
categories: characters
keywords: characters, iOS
---


        [str enumerateSubstringsInRange:range options:NSStringEnumerationByComposedCharacterSequences usingBlock:^(NSString * _Nullable substring, NSRange substringRange, NSRange enclosingRange, BOOL * _Nonnull stop) {
        NSLog(@"substring %@, substringRange %@, enclosingrange %@",substring,[NSValue valueWithRange:substringRange], [NSValue valueWithRange:enclosingRange]);
    }];



输入是`faajs`，输出如下：
> substring  f, substringRange NSRange: {0, 1}, enclosingrange NSRange: {0, 1}  
substring  a, substringRange NSRange: {1, 1}, enclosingrange NSRange: {1, 1}  
substring  a, substringRange NSRange: {2, 1}, enclosingrange NSRange: {2, 1}  
substring  j, substringRange NSRange: {3, 1}, enclosingrange NSRange: {3, 1}  
substring  s, substringRange NSRange: {4, 1}, enclosingrange NSRange: {4, 1}   


输入是`减（）dfa()😄🇺🇸🇬🇧`  ，输出如下：  
>  substring  减, substringRange NSRange: {0, 1}, enclosingrange NSRange: {0, 1}  
substring  （, substringRange NSRange: {1, 1}, enclosingrange NSRange: {1, 1}  
substring  ）, substringRange NSRange: {2, 1}, enclosingrange NSRange: {2, 1}  
substring  d, substringRange NSRange: {3, 1}, enclosingrange NSRange: {3, 1}  
substring  f, substringRange NSRange: {4, 1}, enclosingrange NSRange: {4, 1}  
substring  a, substringRange NSRange: {5, 1}, enclosingrange NSRange: {5, 1}  
substring  (, substringRange NSRange: {6, 1}, enclosingrange NSRange: {6, 1}  
substring  ), substringRange NSRange: {7, 1}, enclosingrange NSRange: {7, 1}  
substring  😄, substringRange NSRange: {8, 2}, enclosingrange NSRange: {8, 2}  
substring  🇺🇸, substringRange NSRange: {10, 4}, enclosingrange NSRange: {10, 4}  
substring  🇬🇧, substringRange NSRange: {14, 4}, enclosingrange NSRange: {14, 4}  

