# vision_language_navigation
record of reading vln-related paper and progress of our work

2024.10.31update:CMA精读总结

***

2024.11.05update:

尝试在本地wsl搭环境，但是运行代码时又出现了经典的报错：

```
Platform::WindowlessEglApplication::tryCreateContext(): unable to find CUDA
device 0 among 1 EGL devices in total WindowlessContext: Unable to create
windowless context
```

之前在服务器上适用的方法在这里也失效了。可能是因为之前服务器上运行下面的指令会列出以下文件

```
(dcvln) root@autodl-container-e9c54199a5-969c76de:/usr/share/glvnd/egl_vendor.d# ldconfig -p | grep libEGL
        libEGL_nvidia.so.0 (libc6,x86-64) => /lib/x86_64-linux-gnu/libEGL_nvidia.so.0
        libEGL_mesa.so.0 (libc6,x86-64) => /lib/x86_64-linux-gnu/libEGL_mesa.so.0
        libEGL.so.1 (libc6,x86-64) => /lib/x86_64-linux-gnu/libEGL.so.1
        libEGL.so (libc6,x86-64) => /lib/x86_64-linux-gnu/libEGL.so
```

而在本地wsl上运行该指令什么也没有输出。
