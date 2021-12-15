[本文[github更新地址](https://github.com/liangyimingcom/Blender-s-experience-performance-on-different-AWS-EC2-instances)] tag: Blender在不同AWS-EC2实例的使用体验表现



# 本文探索的内容：

1. Blender如何运行在没有GPU的EC2中；
2. Blender在没有GPU的EC2中的使用体验；
3. Blender在有无GPU的EC2下的性能对比；
4. 其他细节；



# 一、渲染性能对比结论：

初步结论：

- 同CPU主频下，是否使用GPU，有超过50倍的速度差异；
- 无GPU的机器，渲染结果有异常噪点，影响正常使用；

| NO | AWS EC2型号 | 配置 | 操作系统和版本 | 渲染素材 | 渲染资源 | 消耗时间 | 消耗内存 | 备注 |
| :--- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
| 1    | AWS EC2 g4dn.xlarge | 4c/16g/200Gssd | Windows Win10/Blender30 | Blender 2.93 LTS | GPU      | 1m15s    | 1554M    |      |
| 2    | AWS EC2 g4dn.xlarge | 4c/16g/200Gssd | Windows Win10/Blender30 | Blender 2.93 LTS | CPU      | 47m11s   | 1168M    |      |
| 3    | AWS EC2 m5.xlarge   | 4c/16g/200Gssd | Windows Win10/Blender30 | Blender 2.93 LTS | GPU      | N/A      | N/A      | 没有GPU显卡导致无法运行 |
| 4    | AWS EC2 m5.xlarge   | 4c/16g/200Gssd | Windows Win10/Blender30 | Blender 2.93 LTS | CPU      | 57m02s   | 480M     | mesa库导致渲染结果有异常噪点 |


渲染文件：https://cloud.blender.org/p/gallery/60b7acb93e926b374fd09308



AWS EC2 g4dn.xlarge  4c/16g/200Gssd  Windows Win10/Blender30  Blender 2.93 LTS  GPU  1m15s  1554M

![image-20211211223458631](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211211223458631.png)



AWS EC2 g4dn.xlarge  4c/16g/200Gssd  Windows Win10/Blender30  Blender 2.93 LTS  CPU  47m11s  1168M

![image-20211211222551686](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211211222551686.png)



AWS EC2 m5.xlarge  4c/16g/200Gssd  Windows Win10/Blender30  Blender 2.93 LTS  CPU  57m02s  480M

![image-20211211223037499](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211211223037499.png)







# 二、实时渲染预览对比结论：

- 初步结论：有GPU的机器可以实时渲染成功，无GPU的机器运行缓慢基本不可用；


| NO   | AWS EC2型号         | 配置           | 操作系统和版本          | 渲染素材                | 渲染资源 | 消耗时间 | 备注                     |
| :--- | ------------------- | -------------- | ----------------------- | ----------------------- | -------- | -------- | ------------------------ |
| 1    | AWS EC2 g4dn.xlarge | 4c/16g/200Gssd | Windows Win10/Blender30 | Blender 2.93 LTS/玻璃柜 | GPU      | 秒级     |                          |
| 2    | AWS EC2 m5.xlarge   | 4c/16g/200Gssd | Windows Win10/Blender30 | Blender 2.93 LTS/玻璃柜 | CPU      | N/A      | mesa库导致缓慢或无法运行 |

渲染文件：https://cloud.blender.org/p/gallery/60b7acb93e926b374fd09308

渲染文件：玻璃柜

![image-20211211234258348](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211211234258348.png)

![image-20211215115110214](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211215115110214.png)





# 三、文件打开设计图预览对比结论：

- 初步结论：有GPU的机器可以正常显示建模文件，无GPU的机器建模显示异常；

| NO   | AWS EC2型号         | 配置           | 操作系统和版本          | 渲染素材         | 渲染资源 | 显示是否正常 | 备注               |
| :--- | ------------------- | -------------- | ----------------------- | ---------------- | -------- | ------------ | ------------------ |
| 1    | AWS EC2 g4dn.xlarge | 4c/16g/200Gssd | Windows Win10/Blender30 | Blender 2.93 LTS | GPU      | 显示正常     |                    |
| 2    | AWS EC2 m5.xlarge   | 4c/16g/200Gssd | Windows Win10/Blender30 | Blender 2.93 LTS | CPU      | 显示不正常   | mesa库导致显示异常 |

渲染文件：https://cloud.blender.org/p/gallery/60b7acb93e926b374fd09308/玻璃柜



![image-20211211231919389](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211211231919389.png)





# 四：Blender如何运行在没有GPU的EC2中

[**Installing Mesa3D on Windows**](https://www.khronos.org/opengl/wiki/Platform_specifics:_Windows#Installing_Mesa3D_on_Windows) 

If your system does not contain a GPU, or the GPU vendor delivers graphics drivers providing OpenGL support that's so old as to be useless to you, you might want to consider installing the [Mesa3D](https://mesa3d.org/) OpenGL library on your system to provide OpenGL support.

[Mesa3D](https://mesa3d.org/) is a graphics library that provides an OpenGL implementation for multiple platforms. GPU hardware acceleration is supported on some GPUs, with a software (CPU) rendering pipeline generally available as a fallback or alternative rendering method (see [Mesa3D Support Matrix](https://mesamatrix.net/)).

For beginning developers and others which don't have the time or desire to build Mesa3D libraries from source, here are some pre-built Windows installer (EXE) images. Options are provided to install the Mesa3D OpenGL libraries either system-wide or per-application:

- [mesa-dist-win - Mesa3D Windows Installers](https://github.com/pal1000/mesa-dist-win/releases) (pal1000)
- [mesa-dist-win      - Installation and Usage Instructions](https://github.com/pal1000/mesa-dist-win/) (pal1000)





# 五、EC2配置对比：

### G4DN EC2 配置介绍

![image-20211215113233686](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211215113233686.png)

详情：https://aws.amazon.com/cn/ec2/instance-types/g4/



### M5/C5/R5 EC2 配置介绍

![image-20211215113004515](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211215113004515.png)



![image-20211215113039475](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211215113039475.png)

![image-20211215113059223](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211215113059223.png)

详情：https://aws.amazon.com/cn/ec2/instance-types/?nc1=h_ls


