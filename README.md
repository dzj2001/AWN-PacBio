### AWN: 基于深度学习的小麦基因组结构变异检测方法（适用于三代测序技术PacBio）
####  一、概述
AWN 是一个基于深度学习的结构变异（SV）检测与基因分型框架，专为六倍体面包小麦基因组设计。

本项目是 Cue 框架（针对二倍体人类基因组开发）的增强版，成功实现了从二倍体到六倍体的跨倍性迁移。

主要流程：将序列比对信息转换为二维图像，捕捉跨两个基因组区间的多个比对信号；利用深度学习网络为每张图像生成高斯响应置信图，编码 SV 的位置、类型与基因型；最后将高置信度预测结果细化并映射回基因组坐标。

针对小麦基因组庞大、重复序列多、亚基因组间信号混杂等挑战，AWN 对 Cue 原有的四阶堆叠沙漏网络进行了三项核心改进，提出 RF-HGNet（Refined Fusion Hourglass Network）：

1. Dropout 正则化增强：在沙漏模块的跳跃连接与输出层前系统性引入 Dropout 层，有效缓解过拟合，提升模型泛化能力。

2. 沙漏模块特征融合重构：将最近邻上采样替换为双线性上采样，保留更完整的局部特征连续性；提出 RF-FFM 融合模块，以通道拼接替代逐元素相加，并引入轻量级融合层进行自适应特征整合，增强多尺度表征能力。

3. 余弦退火学习率调度器：替换原有动态学习率策略，配合热重启，实现更稳健的收敛。

AWN 支持检测以下 SV 类型：DEL、DUP、INV、INVDEL、INVDUP。

####  二、将序列比对信息转换为二维图像
1. 准备配置文件

复制 config/data1.yaml或者data2.yaml 并按实际情况修改以下关键参数：

bam: "/path/to/your/alignments.sorted.bam"   # 比对文件

fai: "/path/to/reference.fa.fai"             # 参考基因组索引

bed: "/path/to/annotations.vcf"              # SV标注文件

chr_names: ["Chr1A"]                         # 待处理染色体

2. 运行命令
nohup python /path/to/cue/engine/generate.py \

  --config /path/to/config/data1.yaml \
  
  2> generate.log &
  
3. 查看输出

生成的图像保存在 ./images/ 目录下，按染色体分类存放。
