# spiking-neural-networks-vision-language-models
只是记录一下最简单的思路

参考的项目是MBQ，MBA run_mbq()结束之后获得scale就是eval了。在eval阶段直接替换WALinear类为SNN。
复现结果

Qwen2-VL-7B-Instruct (transformers 4.45.0, float32, H20)

| 方法 | ScienceQA | vs fp32 | Scale 文件 |
|------|-----------|---------|-----------|
| fp32 (无量化) | 85.38% | — | — |
| MBQ W4G128 | 84.44% | -0.94% | `scale_cache/mbq/qwen2_vl_7b_w4g128.pt` |
| MBQ W4A8 | 81.47% | -3.91% | `scale_cache/mbq/qwen2_vl_7b_w4a8.pt` |
| MBQ W4A8 SNN | 82.69% | -- | -- |


但是注意：
完全是伪量化:不仅膜电位阈值是根据当前输入确定的，神经元输出后还调整回浮点，spike化计算的真是只有手动spikes和权重的计算。
MBQ项目给的代码里面没有vision encoder的量化（可以看issues，有人提了这个问题，作者回复有简单的smoothquant实现但是没有集成）；qvlm里面也是把vision encoder量化的内容注释掉了。

不过spikingLLM是真的全SNN化，非线性层的处理也是用残差表达法，尽管非线性层是躲不开浮点计算的。但是也存在online quarot旋转计算。

目前来看，VLM量化离不开online数据处理的流程。也可能是我看的不够多。
