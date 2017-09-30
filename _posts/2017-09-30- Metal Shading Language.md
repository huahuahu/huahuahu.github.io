---
layout: post
title: Metal shading language
categories: GPU
keywords: Metal
---

学习时的笔记，仅供自己看。

## C++ 14  
- based on the C++14 Specification  
- specific extensions and restrictions  
- graphics and compute functions cannot be overloaded  
-  不支持  
    - recursive function calls  
    - new and delete operators  
    - virtual function qualifier  
    - exception handling
    - C++ standard library  
    - Function pointers

## Data Types  
### Scalar Data Types  
***不支持 double, long, unsigned long, long long, unsigned long long、long double***  
### Vector and Matrix Data Types  
- Vector   
    charn、shortn、floatn、ucharn...
- Matrix  
    halfnxm、floatnxm 

        pos = float4(1.0f, 2.0f, 3.0f, 4.0f);
     
        float x = pos[0];    // x = 1.0
        float z = pos[2];    // z = 3.0  

    > Matrix components are constructed and consumed in column-major order  

### Atomic Data Types  
包括 `atomic_int`、`atomic_uint`，只能被 atomic functions 使用。

### Buffers  
执行内置或用户定义的数据类型的指针，这些数据类型必须在 `device` 或 `constant` 地址空间。  

    device float4    *device_buffer;
    struct my_user_data {
        float4 a;
        float  b;
        int2   c;
    };
    constant my_user_data *user_data;  

### Textures    
指向一维、二维、三维的纹理数据的句柄。  

    enum class access { sample, read, write, read_write };
     
    texture1d<T, access a = access::sample>
    texture2d<T, access a = access::sample>
    texture3d<T, access a = access::sample>
    texture2d_ms<T, access a = access::read>
    depth2d<T, access a = access::sample>
    depth2d_ms<T, access a = access::read

`T` 指定了纹理中颜色的类型。对于除了深度纹理类型 (depth texture types) 来说， `T` 可以是 `half`, `float`, `short`, `ushort`, `int`或`uint`，对于深度纹理类型来说，`T` 必须是 `float` 类型。  
`access` 修饰符描述了纹理被访问的方式。  

- sample  纹理可以被采样。
- read  没有采样器，graphics 或者 kernel 函数只能读纹理对象。
- write  graphics 或者 kernel 函数可以写纹理对象。
- read_write  graphics 或者 kernel 函数可以读写纹理对象。  

对于多采样的纹理，只支持 `read` 修饰符。对于深度纹理，只支持 `read` 和 `sample` 修饰符。  

### Samplers  
定义了如何对纹理进行采样。  
可以创建一个 sampler object，然后传递给 graphics 或 kernel 函数。  
也可以在 shader 源文件中描述，这种情况下只支持 sampler state 的一个子集。  
### Arrays and Structs
- Arrays of Textures
    
        array<typename T, size_t N>
        const array<typename T, size_t N>
        
    例子如下  
        
        #include <metal_stdlib>
        using namespace metal;
         
        kernel void
        my_kernel(
            const array<texture2d<float>, 10> src [[ texture(0) ]],
            texture2d<float, access::write> dst [[ texture(20) ]],
            ... )
        {
            for (int i=0; i<src.size(); i++)
            {
                if (is_null_texture(src[i]))
                    break;
         
                process_image(src[i], dst);
            }
        }
        
### Alignment and Size of Types  
分为两种类型     

1.  普通类型   

    |Type|Alignment (in bytes)|Size (in bytes)|
    |:-: |:-: |:-: |
    |float2|8|8|
    |float3|16|16
    
2. packed 类型 
        
    |Type|Alignment (in bytes)|Size (in bytes)|
    |:-: |:-: |:-: |
    |packed_float2|8|8|
    |packed_float3|16|16
    
### Type Conversions and Reinterpreting Data  
用 `static_cast` 来进行类型转换，用 `as_type<type-id>` 来用另一种数据类型来解释若干比特值。    
例如 `as_type<float>(0x3f800000)` 返回 `1.0f`。因为在 IEEE-754 标准中，`0x3f800000` 就是 `1.0f`。  

    // Legal. Contains:
    // (int4)(0x3f800000, 0x40000000,
    //        0x40400000, 0x40800000)
    float4 f = float4(1.0f, 2.0f, 3.0f, 4.0f);
    int4 i = as_type<int4>(f);
    
    half4 f;
    // Error. Result and operand have different sizes
    float4 g = as_type<float4>(f);  

## Operators  
### Operators on Scalars and Vectors  
### Operators on Matrices
> A right vector operand is treated as a column vector, and a left vector operand as a row vector  

## Functions, Variables, and Qualifiers
### Function Qualifiers    
限制了函数的使用方式。  

- kernel    
    >  A data-parallel function that is executed over a 1-, 2- or 3-dimensional grid.
    
    并行的函数，在一维、二维、三维的数据上并行。    
- vertex  
    每一个顶点执行一次，为每一个顶点产生一份输出。
- fragment  
    为每一个 fragment 执行一次，产生一份输出。  

函数的修饰符写在函数返回值之前。    

    kernel void
    foo(...)
    {
        ...
    }

`kernel` 修饰符的函数，返回值必须是 `Void`。  
使用这些修饰符的函数，不可以调用使用这些修饰符的函数。  
#### Attribute Qualifiers for Fragment Functions
`[[early_fragment_tests]]` 修饰符在 fragment 函数中，表明 fragment test 必须在片段着色器之前进行。  

    fragment [[ early_fragment_tests ]] float4
    my_frag_shader( ... )
    {
        ...
    }  
    
### Address Space Qualifiers for Variables and Arguments
address space qualifiers 用来指定函数变量或参数创建时分配地址的区域。  
所有图像及计算函数的参数，如果类型是指针，那么必须指定地址空间修饰符。  
对于图像函数，参数类型的地址空间修饰符必须是 `device` 或 `constant`。  
对于 kernel 函数，参数类型的地址空间修饰符必须是 `device`、`threadgroup` 或 `constant`。    

1. device Address Space  
    它修饰的对象可读可写，从设备的内存池中分配。可以是指向标量、向量、自定义结构体的指针。在分配时，已经确定了对象的内存大小。  
    
        // an array of a float vector with 4 components
        device float4 *color;
         
        struct Foo {
            float a[3];
            int b[2];
        };
         
        // an array of Foo elements
        device Foo *my_info;
对于纹理对象，总是从设备地址空间分配，因此不需要 `device` 来修饰。  

1. threadgroup Address Space  
    修饰被 kernel 函数使用的变量，被多个线程或线程组共享。这些变量不能被图像函数使用。  
    
        kernel void
        my_func(threadgroup float *a [[ threadgroup(0) ]], ...)
        {
            // A float allocated in threadgroup address space
            threadgroup float x;
        
            // An array of 10 floats allocated in 
            // threadgroup address space
            threadgroup float b[10];
            ...
        }
2. constant Address Space  
    在设备内存池中分配的只读变量。  
    
        constant float samples[] = { 1.0f, 2.0f, 3.0f, 4.0f };  
3. thread Address Space  
    每一个线程都分配一个变量。一个线程的变量对其他线程不可见。graphics 或 kernel 函数中定义的变量是用 `kernel` 修饰的。  

        kernel void my_func(...)
        {
            // A float allocated in the per-thread address space
            float x;
            // A pointer to variable x in per-thread address space
            thread float p = &x;
            ...
        }

### Attribute Qualifiers to Locate Buffers, Textures, and Samplers  
- device and constant buffers—`[[ buffer(index) ]]`
- texture and an array of textures—`[[ texture (index) ]]`
- sampler—`[[ sampler (index) ]]`
- threadgroup buffer—`[[ threadgroup (index) ]]`  

        kernel void
        add_vectors(const device float4 *inA [[ buffer(0) ]],
                    const device float4 *inB [[ buffer(1) ]],
                    device float4 *out [[ buffer(2) ]],
                    uint id [[ thread_position_in_grid ]])
        {
            out[id] = inA[id] + inB[id];
        }

对于 kernel、graphics 或自定义函数，参数类型可以是结构体。如果结构体的变量类型是 buffer、texture 或 sampler，那么这些结构体必须传递值，而不是传引用。  

    struct Foo {
        texture2d<float>  a [[ texture(0) ]];
        depth2d<float>    b [[ texture(1) ]];
    };
     
    kernel void
    my_kernel(device Foo& f) // illegal use
    {
        ...
    }
     
    struct MyResources {
        texture2d<float>  a [[ texture(0) ]];
        depth2d<float>    b [[ texture(1) ]];
        int c;
    };
     
    kernel void
    my_kernel(MyResources r) // illegal use
    {
        ....
    }
    
### Attribute Qualifiers to Locate Per-Vertex Inputs
用 `[[ stage_in ]]` 来修饰传递到顶点函数的参数，用顶点的index来索引。每一个顶点的输入的各个分量，要用 `[[ attribute(index) ]]` 修饰。  

    struct VertexInput {
        float4 position [[ attribute(0) ]];
        float3 normal   [[ attribute(1) ]];
        half4 color     [[ attribute(2) ]];
        half2 texcoord  [[ attribute(3) ]];
    };
    
    vertex VertexOutput
    render_vertex(VertexInput v_in [[ stage_in ]],
                  constant float4x4& mvp_matrix [[ buffer(1) ]],
                  constant LightDesc& lights [[ buffer(2) ]],
                  uint v_id [[ vertex_id ]])
    {
        VertexOutput v_out;
        ...
        return v_out;
    }

### Attribute Qualifiers for Built-in Variables  

|Attribute Qualifier|Description|
|:-:|:-:|
|[[ vertex_id]]|The per-vertex identifier (including the base vertex value, if one is specified).|
|[[ instance_id ]]|The per-instance identifier (including the base instance value, if one is specified).|
|[[ base_vertex ]]|The base vertex value added to each vertex identifier before reading per-vertex data.|
|[[ base_instance ]]|The base instance value added to each instance identifier before reading per-instance data.|

### Attribute Qualifiers for Vertex Function Output  
`[[ clip_distance ]]`、`[[ point_size ]]`、`[[ position ]]`、`[[ render_target
_array_index ]]`
### Attribute Qualifiers for Fragment Function Input
### Attribute Qualifiers for Fragment Function Output

    struct MyFragmentOutput {
        // color attachment 0
        float4 clr_f [[ color(0) ]];
     
        // color attachment 1
        int4 clr_i [[ color(1) ]];
     
        // color attachment 2
        uint4 clr_ui [[ color(2) ]];
    };
     
    fragment MyFragmentOutput
    my_frag_shader( ... )
    {
         MyFragmentOutput f;
         ....
         f.clr_f = ...;
         ...
         return f;
    }

### Attribute Qualifiers for Kernel Function Input
### Storage Class Specifiers  
支持 `extern` 和 `static`。
### Sampling and Interpolation Qualifiers
用于片段着色器的用 `stage_in` 修饰的输入，指定采样和插值方式，包括下面几种。默认是 `center_perspective`。

- center_perspective
- center_no_perspective
- centroid_perspective
- centroid_no_perspective
- sample_perspective
- sample_no_perspective
- flat  

对于 int 类型来说，唯一合法的是 `flat`。一个例子如下：

    struct FragmentInput {
            float4 pos [[ center_no_perspective ]];
            float4 color [[ center_perspective ]];
            float2 texcoord;
            int index [[ flat ]];
            float f [[ sample_perspective ]];
    };
    




