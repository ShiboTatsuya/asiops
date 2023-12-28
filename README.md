AIOps(Artificial Intelligence for IT Operations)，即智能运维，是将人工智能的能力与运维相结合，通过机器学习的方法来提升运维效率。

一、异常检测算法介绍
异常检测算法种类繁多，包括聚类，树，统计分布，机器学习，深度学习等多种形式，下面对一些常见问题进行了自己的总结：

1.如何选型？

主要看算法原理和数据分布：

如下图所示，第一二张图的异常点容易成一个团，形成局部离群点，而图三则是全局离群点，不同的离群方式应当采用不同的算法，比如图1，2用聚类，图3用孤立森林。
![Image text](https://github.com/ShiboTatsuya/asiops/blob/main/image/2021081614362597.png)

2.算法集成。

算法种类多，有时候单一算法并不能满足要求，需要对多算法做测试甚至进行集成，以提高性能。

如下图所示的多种集成方式，并不能简单的做一个投票或者均值就进行集成学习，因为全局均值可能会对某些模型输出的异常平均掉。

甚至复杂的还有动态分类器选择，在进行预测的时候，选择训练集中相似的近似空间，并选择在这个相似的近似空间中最好的算法或者是集成算法。

集成工具介绍：https://github.com/yzhao062/combo

动态集成：https://github.com/yzhao062/LSCP
![Image text](https://github.com/ShiboTatsuya/asiops/blob/main/image/2.jpg)

3.异常检测集成加速。

异常检测的集成在很多情况下能对结果有很大的帮助，但是带了的事时间复杂度的提升，如何在有限的时间内尽可能快的进行集成的异常检测，suod很好的解决了这个问题。

suod在3个纬度进行了异常检测集成的加速：

1.数据维度：用了Johnson-Lindenstrauss (JL) projection进行数据降维。

2.模型维度 ：在预测的时候，如果耗时过高，就用有监督学习进行训练数据异常分数的拟合，然后用监督学习替代非监督学习进行预测。

3.系统维度：用了耗时预测对算法进行排列，将新的排列发到不同的worker，以防止出现单一worker的拖后腿
![Image text](https://github.com/ShiboTatsuya/asiops/blob/main/image/3.jpg)

二、AIOps指标异常检测之无监督算法
大量应用场景对快速有效检测异常的需求以及异常检测场景相较于其他场景的特殊性，使得异常检测问题的研究具有极大的应用和研究价值,促使很多异常检测算法被提出和实践.智能化的异常检测算法主要分为三大类,基于统计的异常检测算法、无监督异常检测算法和有监督异常检测算法.下面对三种检测算法作简要介绍和对比分析

1.基于统计的异常检测算法
基于统计的异常检测算法, 利用统计学思想, 通过对历史数据计算统计特征, 然后判断待检测的数据是否符合历史数据的统计特征, 如果是则认为是正常, 如果不是则认为是异常.

常用的基于统计的异常检测算法有3-sigma, 四分位数, 同比, 环比等等.

基于统计的异常检测算法的特点是简单易用, 可解释性强, 但无法处理复杂的场景.

2.无监督异常检测算法
无监督异常检测算法，通过对数据进行计算分析, 识别出那些相对孤立的点, 把这些孤立点就看作为异常点. 由于无监督异常检测算法的应用场景往往只需要得到异常的排名，认为最为异常的一部分数据是异常数据，所以其输出结果大多是样本点对应的异常分数，异常分数对应到样本点的异常程度。在所有无监督异常检测方法中，基于样本点之间的距离或者样本点所在的密度来判别样本是否为异常，是无监督异常检测常用的经典方法。

常用的无监督异常检测算法有孤立森林, LOF, One-Class-SVM等等.

无监督异常检测算法的特点是无需打标数据, 在特征选取合理情况下准确性高, 能处理大规模复杂场景等, 但也存在特征选取难, 可解释性差等问题.

3.有监督异常检测算法
对于有监督异常检测，首先需要一大批带有标签的数据作为训练数据, 将训练数据作为有监督算法的输入, 对算法模型进行训练后得到模型, 然后将待检测的数据作为模型的输入, 最终模型给出待检测是正常还是异常类别.

常用的有监督异常检测算法有SVM, ANN, 决策树, CNN, RNN等等.

通过有监督学习方法，在实验数据中可以得到比较好的异常分类效果。但是在实际异常检测过程中，异常数据往往是少量的，多变的，并且之前可能没有出现过，所以要获得足量准确、适用于有监督算法的数据是很困难的。因此，有监督异常检测不适用于大规模指标异常检测的落地。

三、智能告警学习资料参考
(1) 关联分析：
 1.《HotSpot: Anomaly Localization for Additive KPIs With Multi-Dimensional Attributes》
 2.《CoFlux: Robustly Correlating KPIs by Fluctuations for Service Troubleshooting》
 3.《Correlating Events with Time Series for Incident Diagnosis》
 4.《Detecting Leaders from Correlated Time Series》
 5. Deep reconstruction of strange attractors from time series
 6. High-recall causal discovery for autocorrelated time series with latent confounders
 7. Statistical control for spatio-temporal MEG/EEG source imaging with desparsified mutli-task Lasso
 8. Normalizing Kalman Filters for Multivariate Time Series Analysis
(2) 告警收敛:
  1.《Clustering Intrusion Detection Alarms to Support Root Cause Analysis》
  2.《CMining Alarm Clusters to Improve Alarm Handling Efficiency》
  3.《Automatically and Adaptively Identifying Severe Alerts for Online Service Systems》
  4.《Understanding and Handling Alert Storm for Online Service Systems》
  5.《CoFlux: Robustly Correlating KPIs by Fluctuations for Service Troubleshooting》
  6.《Aggregation and Correlation of Intrusion-Detection Alerts》
  7.《Generic and Robust Localization of Multi-Dimensional Root Causes》
  8.《Probabilistic Alert Correlation》
(3) 根因分析:
  1.《Clustering Intrusion Detection Alarms to Support Root Cause Analysis》
  2.《FluxRank: A Widely-Deployable Framework to Automatically Localizing Root Cause Machines for Software Service Failure Mitigation》
  3.《Adtributor: Revenue debugging in advertising systems》
  4.《HotSpot: Anomaly Localization for Additive KPIs With Multi-Dimensional Attributes》
