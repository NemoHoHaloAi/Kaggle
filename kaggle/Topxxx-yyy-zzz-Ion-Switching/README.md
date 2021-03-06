# [利物浦大学离子通道识别竞赛](https://www.kaggle.com/c/liverpool-ion-switching/overview)

离子通道是存在于动植物中的成孔蛋白。它们编码学习和记忆，帮助抵抗感染，产生疼痛信号，并刺激肌肉收缩。如果科学家们能够更好地研究离子通道，这在机器学习的帮助下是可能的，那么它将产生深远的影响。

竞赛目的是通过原始数据对离子通道进行自动识别，这将取代旧的手工监督分析方法，提高效率，降低偏差。

类型：多分类预测，评估方法为macro-F1，即对每个类别求F1后计算平均值。

数据特点：首先，这是个时序数据，时间间隔为0.0001，训练数据总长度为500s，测试数据为500s后的数据，其次这里人工填了了噪声数据以模拟实际情况。列非常少，除了目标特征open_channel外，只有一个signal，因此对于列在各个维度上的处理空间几乎没有，那么应该就是全部交给时序特征工程了。

注意：虽然在time上整体是连续的，但是实际上数据来源于不同的batch，每个batch时间长度为50s，行数为500000行，因此50.0000与50.0001是不连续的，要注意。因此这里与标准的时序数据有差异，可以考虑引入特征batch用于标识不同的batch，同时lag时是分别lag，还是如何处理也要考虑。

start with：https://www.kaggle.com/pestipeti/eda-ion-switching

notebook：https://www.kaggle.com/holoong9291/eda-ion-switching

## 想法

0. 考虑使用ARIMA模型进行预测：
    1. 计算数据自相关与偏自相关；
1. 不同的batch应该分开考虑设计特征：
    1. batch内部的signal统计信息丰富化，目前baseline中主要是window相关，这里我们可以做lag相关、batch平均值中位数等等；
2. window操作同样放到每个batch中进行，目前的做法是统一进行的，我认为这会为这一系列特征引入噪声，因为计算的基础中有来自不同的batch的原始数据；

## 版本

- baseline：保持不变。
- B：增加signal的统计特征(signal_mean 	signal_median 	signal_std 	signal_var 	signal_min 	signal_max)。
- C：window处理分batch进行。
- D：调整window size，考虑对nan的填充方式进行完善，之前是0，感觉填充0在业务意义上不合理，可以考虑mean。
