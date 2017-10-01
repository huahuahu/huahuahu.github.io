---
layout: post
title: Metal Programming Guide
categories: GPU
keywords: Metal
---

读 Metal Programming Guide 的笔记。

>  primary goal of Metal is to minimize the CPU overhead incurred by executing GPU workloads.  

用在两个方面：  

- graphics  
- data-parallel computation   

Metal App 不能在后台运行，否则会被终止。  
## Command Organization and Execution Model

![](http://oda58fqub.bkt.clouddn.com/15068248257375.png)
  

- MTLDevice 是对 GPU 的建模。  
- command queue，由若干 command buffer 组成的队列，并负责这些 command buffer 的执行顺序。  
- command buffer，包含 encoded commands （要在设备上执行的指令）。  
- command encoder，把命令加到 command buffer 中。  

### Transient and Non-transient Objects in Metal  
Command buffer 和 command encoder 是轻量级的，设计为单次使用。  
下面这些的创建是比较消耗资源的，应该复用。  

- Command queues
- Data buffers
- Textures
- Sampler states
- Libraries
- Compute states
- Render pipeline states
- Depth/stencil states  

### Registering Handler Blocks for Command Buffer Execution
block 中的方法可能在任意线程调用，并且不应该是耗时的。  

-  addScheduledHandler:  
-  waitUntilScheduled:
-   addCompletedHandler: 
-   waitUntilCompleted 

## Resource Objects: Buffers and Textures
`MTLBuffer` 有两个类型。  

- `MTLBuffer`:  无格式 (unformatted) 的内存，可以表示任意类型数据。
- `MTLTexture `： 有格式 (formatted) 的图像数据。  

### Buffers Are Typeless Allocations of Memory  
- `contents` 方法返回 buffer 的 CPU 内存地址。  
- `newTextureWithDescriptor:offset:bytesPerRow:` 方法创建 texture 对象，指向 buffer 的数据。  

### Textures Are Formatted Image Data  
可以有下面的结构  

- A 1D, 2D, or 3D image
- An array of 1D or 2D images  
- A cube of six 2D images  

#### Creating a Texture Object  
- newTextureWithDescriptor:     
    对`MTLDevice`调用，创建 `MTLTexture` 对象，分配了存储空间。使用 `MTLTextureDescriptor` 来描述纹理的性质。
-  newTextureViewWithPixelFormat:    
    对 `MTLTexture` 调用，返回 `MTLTexture` 对象，和原来的纹理共享内存地址。新的纹理用新的像素格式来解释原有的纹理数据。
-  newTextureWithDescriptor:offset:bytesPerRow:  
    对 `MTLBuffer` 调用，返回 `MTLTexture` 对象，和原来的 buffer 共享内存地址。  
    
#### 使用 Texture Descriptor 来创建纹理对象    
 `MTLTextureDescriptor` 定义了纹理的性质，只是用来创建纹理对象。在创建好纹理对象之后，对它的修改不会有任何影响。  
   
       MTLTextureDescriptor* txDesc = [[MTLTextureDescriptor alloc] init];
        txDesc.textureType = MTLTextureType3D;
        txDesc.height = 64;
        txDesc.width = 64;
        txDesc.depth = 64;
        txDesc.pixelFormat = MTLPixelFormatBGRA8Unorm;
        txDesc.arrayLength = 1;
        txDesc.mipmapLevelCount = 1;
        id <MTLTexture> aTexture = [device newTextureWithDescriptor:txDesc];
       
### Copying Image Data to and from a Texture  

1. 把数据写入纹理
    - replaceRegion:mipmapLevel:slice:withBytes:bytesPerRow:bytesPerImage:   
    - replaceRegion:mipmapLevel:withBytes:bytesPerRow: 
2. 把数据从纹理读出来
    - getBytes:bytesPerRow:bytesPerImage:fromRegion:mipmapLevel:slice:  
    - getBytes:bytesPerRow:fromRegion:mipmapLevel: 

例子如下：

    //  pixelSize is the size of one pixel, in bytes
    //  width, height - number of pixels in each dimension
    NSUInteger myRowBytes = width * pixelSize;
    NSUInteger myImageBytes = rowBytes * height;
    [tex replaceRegion:MTLRegionMake2D(0,0,width,height)
        mipmapLevel:0 slice:0 withBytes:textureData
        bytesPerRow:myRowBytes bytesPerImage:myImageBytes];
            

### Pixel Formats for Textures  
有三种像素类型： 

1. Ordinary formats  
2. Packed formats
3. Compressed formats

### Creating a Sampler States Object for Texture Lookup
1. 创建 ` MTLSamplerDescriptor` 对象 
2. 设置对象的性质  
3. 创建 `MTLSamplerState` 对象。  

`MTLSamplerState` 对象只是用来创建 `MTLSamplerState` 对象。创建完之后，对它的修改不会影响已经创建好的 `MTLSamplerState` 对象。  

    // create MTLSamplerDescriptor
    MTLSamplerDescriptor *desc = [[MTLSamplerDescriptor alloc] init];
    desc.minFilter = MTLSamplerMinMagFilterLinear;
    desc.magFilter = MTLSamplerMinMagFilterLinear;
    desc.sAddressMode = MTLSamplerAddressModeRepeat;
    desc.tAddressMode = MTLSamplerAddressModeRepeat;
    //  all properties below have default values
    desc.mipFilter        = MTLSamplerMipFilterNotMipmapped;
    desc.maxAnisotropy    = 1U;
    desc.normalizedCoords = YES;
    desc.lodMinClamp      = 0.0f;
    desc.lodMaxClamp      = FLT_MAX;
    // create MTLSamplerState
    id <MTLSamplerState> sampler = [device newSamplerStateWithDescriptor:desc];
    
### Maintaining Coherency Between CPU and GPU Memory
CPU 和 GPU 都可以访问 `MTLResource` 对象。  
GPU 只能记录在 `MTLCommandBuffer` 的 `commit` 方法调用之前，CPU 对 `MTLResource` 的改动。  
CPU 只有在 GPU 完成 `MTLCommandBuffer` 中的指令之后，才可以看到 GPU 对 `MTLResource` 的改动。  

## Functions and Libraries  
### MTLFunction Represents a Shader or Compute Function  
只有被 `vertex`、`fragment` 或 `kernel` 修饰的函数才可以表示为 `MTLFunction`。
### A Library Is a Repository of Functions  
Library 中的 `MTLFunction` 可以有两个来源：  

1. 在 build 阶段被编译好的二进制 Metal shading language code  
2. 包含 Metal shading language 源代码的字符串，在运行时被编译  

#### Creating a Library from Compiled Code
可以提高效率，不必在运行时编译源代码。  
可以通过以下方法来获取 `MTLLibrary`。  

1. newDefaultLibrary  
2. newLibraryWithFile:error:   
3. newLibraryWithData:error:  

#### Creating a Library from Source Code  
1. newLibraryWithSource:options:error:   同步方法
2. newLibraryWithSource:options:completionHandler:  异步方法  

## Graphics Rendering: Render Command Encoder

  ![Metal Graphics Rendering Pipeline](http://oda58fqub.bkt.clouddn.com/15068312954461.png)  

### Creating a Render Pass Descriptor
`MTLRenderPassDescriptor` 表示 encoded rendering commands 的目的地 (destination)。`MTLRenderPassDescriptor` 的属性可以包括至多 4 个颜色的 attachments，一个像素深度数据的 attachment，一个像素 stencil 数据的 attachment。  
### Load and Store Actions  
指定了在 rendering pass 的起始和结束时的动作。  
`loadAction` 包括：  

1. MTLLoadActionClear  为每一个像素写入指定的值。    
2. MTLLoadActionLoad 保存已有的值      
3. MTLLoadActionDontCare 在起始阶段可以有任意值，性能最高。     

storeAction 包括  

1. MTLStoreActionStore   保存最后的像素值，color attachments 的默认值
2. MTLStoreActionMultisampleResolve   
3. MTLStoreActionDontCare 会提高性能。depth and stencil attachments 的默认值。  

一个例子如下：

    MTLTextureDescriptor *colorTexDesc = [MTLTextureDescriptor
               texture2DDescriptorWithPixelFormat:MTLPixelFormatRGBA8Unorm
               width:IMAGE_WIDTH height:IMAGE_HEIGHT mipmapped:NO];
    id <MTLTexture> colorTex = [device newTextureWithDescriptor:colorTexDesc];
     
    MTLTextureDescriptor *depthTexDesc = [MTLTextureDescriptor
               texture2DDescriptorWithPixelFormat:MTLPixelFormatDepth32Float
               width:IMAGE_WIDTH height:IMAGE_HEIGHT mipmapped:NO];
    id <MTLTexture> depthTex = [device newTextureWithDescriptor:depthTexDesc];
     
    MTLRenderPassDescriptor *renderPassDesc = [MTLRenderPassDescriptor renderPassDescriptor];
    renderPassDesc.colorAttachments[0].texture = colorTex;
    renderPassDesc.colorAttachments[0].loadAction = MTLLoadActionClear;
    renderPassDesc.colorAttachments[0].storeAction = MTLStoreActionStore;
    renderPassDesc.colorAttachments[0].clearColor = MTLClearColorMake(0.0,1.0,0.0,1.0);
     
    renderPassDesc.depthAttachment.texture = depthTex;
    renderPassDesc.depthAttachment.loadAction = MTLLoadActionClear;
    renderPassDesc.depthAttachment.storeAction = MTLStoreActionStore;
    renderPassDesc.depthAttachment.clearDepth = 1.0;
    
### Using the Render Pass Descriptor to Create a Render Command Encoder    

    id <MTLRenderCommandEncoder> renderCE = [commandBuffer
                        renderCommandEncoderWithDescriptor:renderPassDesc];


### Displaying Rendered Content with Core Animation
利用 `CAMetalLayer`。  
### Creating a Render Pipeline State
`MTLRenderPipelineState` 对象生命周期很长，应该缓存起来复用。  
`MTLRenderPipelineState` 对象是不可变的。先创建一个 `MTLRenderPipelineDescriptor`，设置好性质，然后再创建 `MTLRenderPipelineState`。  
![-w366](http://oda58fqub.bkt.clouddn.com/15068331108057.png)    

例子如下：  

    MTLRenderPipelineDescriptor *renderPipelineDesc =
                                 [[MTLRenderPipelineDescriptor alloc] init];
    renderPipelineDesc.vertexFunction = vertFunc;
    renderPipelineDesc.fragmentFunction = fragFunc;
    renderPipelineDesc.colorAttachments[0].pixelFormat = MTLPixelFormatRGBA8Unorm;
     
    // Create MTLRenderPipelineState from MTLRenderPipelineDescriptor
    NSError *errors = nil;
    id <MTLRenderPipelineState> pipeline = [device
             newRenderPipelineStateWithDescriptor:renderPipelineDesc error:&errors];
    assert(pipeline && !errors);
     
    // Set the pipeline state for MTLRenderCommandEncoder
    [renderCE setRenderPipelineState:pipeline];
            
### Specifying Resources for a Render Command Encoder  
![-w501](http://oda58fqub.bkt.clouddn.com/15068333585942.png)    

调用 `setVertex*` 以及 `setVertex*` 方法。  
### Drawing Geometric Primitives
 调用 `draw*` 方法。    
###  Ending a Rendering Pass    
  调用 `endEncoding ` 方法
  






