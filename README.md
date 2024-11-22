# vision_language_navigation
record of reading vln-related paper and progress of our work



2024.10.31update:CMA精读总结



2024.11.01update:vln最初的论文模型只使用了一个observation，也就是根据当前agent所处的位置以及摄像头的角度拍摄得到的一张图片。后续的工作，包括CMA和提出vlnce的模型，都使用全景图。



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

因此还是决定使用autodl服务器



***

在autodl服务器端安装habitat的时候报错

```
(base) root@autodl-container-78f345b3a1-1265f8ce:~# conda install habitat-sim -c conda-forge -c aihabitat
Collecting package metadata (current_repodata.json): done
Solving environment: - failed with initial frozen solve. Retrying with flexible solve.
```

解决办法：更新conda，更新会消耗不少时间(autodl提供的conda版本真垃圾)

```
conda update -n base conda
```

~~使用mamba~~(可以不用)

```
conda install mamba -c conda-forge
```

更新conda后，成功安装habitat-sim和habitat-lab。



***

使用指令

```
python -m habitat_sim.utils.datasets_download --uids habitat_test_scenes --data-path data/
```

下载3D场景时报错：

```
ImportError: libEGL.so.1: cannot open shared object file: No such file or directory
```

解决办法([ImportError: libEGL.so.1: cannot open shared object file: No such file or directory · Issue #375 · scottlawsonbc/audio-reactive-led-strip](https://github.com/scottlawsonbc/audio-reactive-led-strip/issues/375))：

```
sudo apt-get install freeglut3-dev
```

目前安装进度：habitat环境搭建完毕，habitat-lab测试数据下载完毕。

目前学习进度：habitat-sim tutorial除了navmesh部分已经看完



2024.11.18update:

初始化代理时遇到下面所示curl报错

```
[√] 已开启代理
正在测试网络连接...
curl: symbol lookup error: curl: undefined symbol: curl_easy_header
网络连接测试失败。请检查您的网络和 Clash 配置。
```

解决办法：[[BUG\] curl: undefined symbol: curl_easy_header · Issue #1159 · swizzin/swizzin](https://github.com/swizzin/swizzin/issues/1159)

```
sudo rm -rf /usr/local/bin/curl*
```



2024.11.21update：

将autodl一台机子原封不动克隆到另一台机子后报错：

```
Platform::WindowlessEglApplication::tryCreateContext(): cannot initialize EGL: EGL_NOT_INITIALIZED
WindowlessContext: Unable to create windowless context
```

原因：两台机子的nvidia driver version不同
