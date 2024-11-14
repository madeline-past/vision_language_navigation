# MapGPT

#### Visual Observation:

只使用包含navigable point的图像，处理图像有两种办法，一种是和navgpt一样的two-stage system，既抽特征又送入bert生成描述，和navgpt一样，还有一种是one-stage system，直接把原始图片送到gpt里面。

这里只使用包含navigable point的图像就意味着，输给gpt的信息就只有包含navigable point的图像，其他无关图像直接丢弃不用。

#### action space：

stop+跳转到所有邻近的可观测到的navigable point

#### point category：

(1) explored nodes {enj }t  j=0 (including starting node en0 and current  node ent),

(2) accessible nodes {ant0, ant1, ...}, and 

(3) unexplored inaccessible nodes {un0, un1, ...}.

#### Map：

```
“Map: 
Place {en0} is connected with Places {an00}, ...  
Place {en1} is connected with Places {an01}, ... 
...  
Place {ent} is connected with Places {ant0}, ...”,
```

使用如上语言描述图

#### supplementary info:

记录目前不可到达且之前没探索过的地方的图像信息

#### code：

matterport simulator

```
By default, only camera viewpoints that are within the agent's current field of view are considered navigable, unless restricted navigation is turned off (i.e., the agent can't move backwards, for example).
```

![e7b1df8c1da65fe4d5172d4c55385834](assets/e7b1df8c1da65fe4d5172d4c55385834.png)

![031b74b936e611f5e1c9bcfa80604c36](assets/031b74b936e611f5e1c9bcfa80604c36.png)

#### 问题

1. in [MapGPT/GPT/one_stage_prompt_manager.py at main · chen-judge/MapGPT](https://github.com/chen-judge/MapGPT/blob/main/GPT/one_stage_prompt_manager.py) line67

```
direction = self.get_action_concept(cc['absolute_heading'] - previous_angle[i]['heading'],cc['absolute_elevation'] - 0)
```

2. 用别人视角里包含路径点的一张图概括路径点的特征，是否有些片面。个人感觉可以在后续采到同一路径点不同视角下的图像时，也可以保留之前图片，一起作为该路径点的特征。
3. ce中路径点会改变，如何构造一个和离散相似的map

#### 移植改动：

1. 
