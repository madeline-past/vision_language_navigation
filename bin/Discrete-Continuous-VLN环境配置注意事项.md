### Discrete-Continuous-VLN环境配置注意事项

1. 跑之前需要删除logs/eval_results/相关文件

2. 下载完The pre-trained [weights used in our paper (FoV 90 RGB-D)](https://drive.google.com/file/d/1hRdecKWHnKjidIT_i5H5lEGRS53ewWEs/view?usp=drive_link).之后需要改名为check_val_best_avg_wayscore；其他两个The pre-trained无需下载

3. 遇到报错Platform::WindowlessEglApplication::tryCreateContext(): unable to find CUDA

   device 0 among 1 EGL devices in total WindowlessContext: Unable to create

   windowless context时，in the /usr/share/glvnd/egl_vendor.d/ directory, create a file named 10_nvidia.json with the following content:

   ```json
   {
   "file_format_version" : "1.0.0",
   "ICD" : {
   "library_path" : "libEGL_nvidia.so.0"
   }
   }
   ```

   then do

   ```bash
   sudo ldconfig
   ```

   

