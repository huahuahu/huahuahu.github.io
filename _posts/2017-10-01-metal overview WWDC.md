---
layout: post
title: WWDC Working with Metal—Overview
categories: GPU
keywords: Metal
---
# Working with Metal—Overview
看完这个 WWDC 之后的总结。  
  
  Metal 可以在单位时间内提供 10 倍的 draw call 调用。  
  
## Background  
### About Draw Call  

  每一次 draw call 调用都必须有自己的状态向量，比如着色器、纹理等。而改变状态向量对 CPU 来说是比较耗时的，因此单位时间内 draw call 的次数有限。  
  ![](http://oda58fqub.bkt.clouddn.com/15068543864754.jpg)  
  
  CPU 负责把状态向量的改变翻译为硬件命令 (hardware command)，然后告诉 GPU。  
    
### Metal 的优化点      
在 Metal 之前，如果使用了 GPU 的 API，每一帧耗在 CPU 的时间，可以分为应用中 API 时间和把 GPU 的 API 调用翻译为 GPU 指令的时间。而 Metal减少的就是这部分时间。
![-w600](http://oda58fqub.bkt.clouddn.com/15068551161474.jpg)  

下面就是使用了 Metal 后的时间对比。  
  ![](http://oda58fqub.bkt.clouddn.com/15068554517487.jpg)  
  
### 为何 GPU 编程代价昂贵  
  
1. State validation  
     - 需要验证 API 调用正确  
     - 需要把 API 的状态映射到硬件的状态
1. Shader compilation    
    - 需要运行时编译生成 GPU 对应的硬件代码  
    - 有时改变状态时，需要重新编译 shaders
2. Sending work to GPU  
    - 需要把数据组织成 GPU 易于理解的格式  
    - 经常需要批量调用，以提高单位时间的 draw call 次数，降低了灵活性  
    
### Metal 把部分工作放在了编译和加载时 
![](http://oda58fqub.bkt.clouddn.com/15068562265442.jpg)    

这里的频率是从用户的角度看到的，即用户不会经历 build 的过程。  

## API concepts
 写代码时用到的所有类型之间的关系。    
 ![](http://oda58fqub.bkt.clouddn.com/15068567900223.jpg)  
 
 在编译之后，模型如下所示，不需要进一步的验证之类的事情。  
 ![](http://oda58fqub.bkt.clouddn.com/15068568254859.jpg)  
 
 Command encoders generate commands immediately，没有 state validation 的过程，可以理解为直接调用硬件驱动。  
 
### Resource Update Model    
A7处理器之后，  

- CPU and GPU share same storage  
- 没有隐含的数据拷贝  
- CPU 和 GPU 之间的数据是自动同步的，不需要显式的缓存管理、flush。  

Metal 提供了两种资源类型：  
- Textures (formatted images)  
- Data buffers (unformatted memory)  

资源的结构（size、level、format）不可变，这样子就避免了昂贵的  resource validation 操作。  

### Command Encoder Types
#### Render command encoder  
关于 Graphics rendering，为一次 rendering “pass” 产生硬件指令。  
不会在 draw 时候进行编译，避免了昂贵的编译和  state validation。  
有些状态的改变会导致重写编译，因此这些状态被设置为不可变的。    
![](http://oda58fqub.bkt.clouddn.com/15068580937402.jpg)  

A7 是一个 Tile-based deferred-mode renderer，具体啥意思我也不知道。  
在每一个 render pass 的起始和结束，都会有一次 load 和 store 操作。  
使用 Metal，可以指定 load 和 store 操作的类型。  
load 时的可选类型是 Don’t care, load, clear。  
Store 时的可选类型是  Don’t care, store, multisample resolve。    
![](http://oda58fqub.bkt.clouddn.com/15068586327038.jpg)    
假设一次 frame 有两次 render pass，处理了 color 和 depth 的数据，那么 color 和 depth 的 framebuffer，都需要两次读和写操作。  
![](http://oda58fqub.bkt.clouddn.com/15068587403306.jpg)  

使用 Metal 指定了相应的 load 和 store 操作时候，只需要 color framebuffer 的一次读操作和两次写操作。  
## Shading Language  
> Unified shading language for graphics and compute processing  

既用于图像处理，又用于并行数据处理。  

## Developer tools  
### Metal Shader Compiler Process
![](http://oda58fqub.bkt.clouddn.com/15068596222961.jpg)    

大致分两步。  

- 在 build 时，编译为 metal Library，并打包进应用安装包中。  
- 在创建管线对象时，先看缓存中有没有，如果找不到，就编译一下，加入缓存。然后把编译好的代码（和具体设备有关）告诉 GPU。  






  



