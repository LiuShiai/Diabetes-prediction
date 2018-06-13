README
==============
## 1 问题描述<br>
 这个比赛的任务是预测妊娠期妇女是否患有妊娠期糖尿病，label只有一列，0表示未患病，1表示患病。一共有1000条训练样本，85维特征。线上测试样本为200条，采用F1值来评价结果好坏。由于样本较少所以很容易出现过拟合问题。在85维特征中，有30个是身体指标特征，诸如年龄、身高、体重、BMI、胆固醇指标等等，其他55个是基因特征，基因特征有3中取值0,1,2代表生物学中的AA、Aa、aa。下面介绍赛题思路。
[赛题地址](https://tianchi.aliyun.com/competition/introduction.htm?spm=5176.11165261.5678.1.66977190O9P200&raceId=231638)
<br>
<br>
### 1 连续特征类<br>
  查看数据的分布，采用不同的填充办法，比如平均值、中值、众数等 <br>
  以平均值为标准值，添加和平均值的差值，以及差值的绝对值 <br>
  对连续特征做归一化处理，由于后面需要对特征之间做运算，所以需要把0替换成极小值 <br>
  归一化处理后做加减乘除和反除，以得到组合特征。 <br>
<br>
### 2 离散特征类<br>
  离散特征采用one-ht编码
  编码后的特征做与、或、异或、同或处理
<br>
### 3 特征筛选<br>
  线性回归筛选特征+非线性XGBoost筛选
<br>
<br>
## 3 模型<br>
  这里采用的模型主要有3种，LR、XGB和LGB，其中LR采用线性筛选得到的特征，XGB和LGB采用非线性筛选得到的特征
<br>
<br>
## 4 融合<br>
  这里采用简单的加权融合的办法，最终结果线上F1值0.6429，复赛排名57。总体上来说思路比较简单，传统，和排名靠前的选手有很大的差距。下面根据答辩的情况，对每位选手的方案作出总结
<br>
<br>
## 5 对答辩选手的总结<br>
  在数据填充中，选手不是采用简单的平均值和中位数的填充办法，而是Nuclear和范数填充的办法。对于特征处理，选手先去掉了相关度非常小的几个特征以剔除噪声。然后分析所有特征对于label的单调性，实际上也类似于相关度，筛选出强特征和弱特征，在对强特征之间做组合，又一次通过单调性筛选出强特征。一般迭代4,5次可以得到不错的结果。这个的筛选指标不仅仅可以使用单调性、相关度等，还可以使用[IV值](https://blog.csdn.net/kevin7658/article/details/50780391)，[REFCV](http://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.RFECV.html)的办法来筛选。这里面ACEID也是一个非常不错的特征，但是缺失值很多，容易发生过拟合，选手们的办法是不填充缺失值或者干脆这个特征不用。
  在模型选择上大家都大同小异，LR、SVM、RF、GBDT、XGB、LGB都试一遍，看看效果，有一个选手用了[Catboost](https://github.com/catboost/catboost)的模型。在调参上面，有选手采用了遗传算法来调试出最佳的参数，思路不错。
  后处理，，对于不确定的结果，正例和负例相接近的(患病概率为0.49~0.50之间的也预测为患病)，可以在一定程度上提高成绩。
