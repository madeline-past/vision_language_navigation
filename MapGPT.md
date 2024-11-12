# MapGPT

#### Visual Observation:

只使用包含navigable point的图像，处理图像有两种办法，一种是和navgpt一样的two-stage system，既抽特征又送入bert生成描述，还有一种是one-stage system，直接把原始图片送到gpt里面。

#### Map：

```
“Map: 
Place {en0} is connected with Places {an00}, ...  
Place {en1} is connected with Places {an01}, ... 
...  
Place {ent} is connected with Places {ant0}, ...”,
```

使用如上语言描述图