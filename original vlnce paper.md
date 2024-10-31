render v.渲染

Beyond the Nav-Graph: Vision-and-Language  Navigation in Continuous Environments论文精读

#### 1.重新构建VLN-CE数据集

“MP3D also provides corresponding mesh-based 3D environment reconstructions” (Krantz 等, 2020, p. 6)

重建了一个基于habitat simulator的连续空间“reconstructed Matterport3D (MP3D) environments” (Krantz 等, 2020, p. 7)，然后把离散空间的waypoint映射到mesh上。然后，又把能移植的路径全移植了。最终，形成了VLN-CE Dataset。

#### 2.agent

2.1 seq2seq

最简单的一集，连注意力机制都没加

![9196314a3b936a50c07b592207ee51bd](assets/9196314a3b936a50c07b592207ee51bd.png)

![b30be4dac00e1a02f58a2fb2bdd74473](C:\Users\B3ethoven\Documents\Tencent Files\1265934866\nt_qq\nt_data\Pic\2024-10\Ori\b30be4dac00e1a02f58a2fb2bdd74473.png)

2.2 Cross-Modal Attention Model

这个就复杂不少

![ab78d761ec6a1eb3d32c72497078cfb2](assets/ab78d761ec6a1eb3d32c72497078cfb2.png)



This model consists of two recurrent networks – one tracking

![b3f5811154198c18dd8e03aba6a8e76a](assets/b3f5811154198c18dd8e03aba6a8e76a.png)

总的来说，感觉变化不大，只是从high-level action改为了low-level action

#### 3.一些优化的trick

感觉不重要就没看

DAgger，inflection weighting....（不懂）

