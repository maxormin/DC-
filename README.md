DC小分子性质预测竞赛rank4方案 
=======
数据处理部分：
------
    将小分子矢量列表Features中的特征提取出来，这一部分代码参考了比赛初期选手开源的代码；  
    偏度较大特征正态化；
    对含有缺失值的特征利用均值进行填充；
    剔除方差为0的特征；
    
简单的特征工程：
------
    将小分子矢量中的0，1矢量特征提取出来，计算该矢量的和(Features_sum)，均值(Features_mean)，0的数量(Features_0_count)，1的数量(Features_1_count)；
    对分子相位特征进行one_hot处理；
    删除非二值特征中的冗余特征（利用相关性，将强相关的特征进行剔除）;
    
Magic：
------
    通过绘制label分布图，呈现明显的双峰分布，猜测数据可能为两个物质的数据的拼接。针对这个问题，个人使用了两个方法进行处理：
    方法1：根据label分布图，选择阈值，将label转换为0与1，利用lgb进行二分类训练；将得到的二分类的概率值作为特征加入到上述处理好的数据中，
    进行最终的回归预测；
    方法2：根据label分布图，选择阈值，将label转换为0与1，利用lgb进行二分类训练，对test数据进行预测，并打上0，1标签。
    利用得到的label为0与1的两个数据集，单独对每一个数据集进行回归预测；
效果：
----
    方法1在A榜上得分略高于方法2。但是在线下训练时方法2的rmse<1.6，原因主要是，虽然在训练集上分为两个数据集训练的效果很好，
    但是我们训练的预分类的模型在测试集的效果无法评估，最终导致方法2线下线上差异较大；
    最终选择方法1进行训练，利用单模lgb即可在b榜得到2.036的成绩;
