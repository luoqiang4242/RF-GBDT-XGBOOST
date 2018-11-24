RF、GBDT、XGBoost、GBDT+LR（https://blog.csdn.net/qq_28031525/article/details/70207918）

1、集成学习包括：

	1）boosting：不同的分类器是通过串行训练而获得，每个新分类器都根据已训练的分类器的性能来进行训练，结果是每棵树累加  
	2）bagging：随机抽样样本，多数表决，并行生成（RF随机森林）

2、RF

	1)随机选择样本，放回抽样
	2)随机选择特征
	3)构建决策树（分类树）
	4)随机森林投票（平均）
	优点：速度快、预测准确率高、处理高纬数据、能够并行化每颗树独立训练、可以处理不平衡数据、对缺失值、异常值不敏感
	缺点：噪声过大容易过拟合、对有不同取值的属性的数据，取值划分较多的属性会对随机森林产生更大的影响。
	
3、决策树： 

	决策树学习：采用自顶向下的递归的方法，基本思想是以信息熵为度量构造一棵熵值下降最快的树，到叶子节点处熵值为0（叶节点中的实例都属于一类）  
	ID3算法使用信息增益、C4.5算法使用信息增益率、CART算法使用基尼系数。C4.5是ID3基础上解决ID3信息增益会偏向那些取值较多的特征)          		  https://blog.csdn.net/weixin_36586536/article/details/80468426
	构建决策树分为：
        	a.建树:
                      分类树：决策树方法是会把每个特征都试一遍，然后选取那个，能够使分类分的最好的特征，也就是说将A属性作为父节点，产生的纯度增益（GainA）要大于B属性作为父节点，则A作为优先选取的属性。
                      回归树：使用最小方差作为分裂规则（归树对输入空间的划分采用一种启发式的方法，会遍历所有输入变量，找到最优的切分变量j和最优的切分点s，即选择第j个特征xj和它的取值s将输入空间划分为两部分，然后重复这个操作）  
		b.剪枝:后剪枝或预剪枝，常用后者，即在训练过程中限制叶子节点个数，深度等方法。

4、GBDT https://blog.csdn.net/zpalyq110/article/details/79527653

        原理：gbdt通过多轮迭代,每轮迭代产生一个弱分类器，每个分类器在上一轮分类器的残差基础上进行训练（弱分类器一般选择CART回归树，满足boosting高偏差低方差即可）  
	在GBDT的迭代中，假设我们前一轮迭代得到的强学习器是ft−1(x)ft−1(x), 损失函数是L(y,ft−1(x))L(y,ft−1(x)), 我们本轮迭代的目标是找到一个CART回归树模型的弱学习器ht(x)ht(x)，让本轮的损失损失L(y,ft(x)=L(y,ft−1(x)+ht(x))L(y,ft(x)=L(y,ft−1(x)+ht(x))最小。也就是说，本轮迭代找到决策树，要让样本的损失尽量变得更小。 
	常用的损失函数：平方损失(GBDT)，交叉熵(LR)等。  
	手撕原理：A的预测值 + A的残差 = A的实际值。举例：A的真实年龄是18岁，但第一棵树的预测年龄是12岁，差了6岁，即残差为6岁。那么在第二棵树里我们把A的年龄设为6岁去学习，如果第二棵树真的能把A分到6岁的叶子节点，那累加两棵树的结论就是A的真实年龄；如果第二棵树的结论是5岁，则A仍然存在1岁的残差，第三棵树里A的年龄就变成1岁，继续学。（https://blog.csdn.net/aaa_aaa1sdf/article/details/81588382） 
	优点：可以灵活处理各种类型的数据，包括连续值和离散值。使用一些健壮的损失函数，对异常值的鲁棒性非常强。比如 Huber损失函数和Quantile损失函数。
	缺点：由于弱学习器之间存在依赖关系，难以并行训练数据。

5、Xgboost https://blog.csdn.net/u014472643/article/details/80658009

	XGBoost和GBDT的区别
	1）将树模型的复杂度加入到正则项中，来避免过拟合，因此泛化性能会由于GBDT
	2）损失函数是用泰勒展开式展开的，同时用到了一阶导和二阶导，可以加快优化速度
	3）和GBDT只支持CART作为基分类器之外，还支持线性分类器，在使用线性分类器的时候可以使用L1，L2正则化
	4）引进了特征子采样，像RandomForest那样，这种方法既能降低过拟合，还能减少计算
	5）在寻找最佳分割点时，考虑到传统的贪心算法效率较低，实现了一种近似贪心算法，用来加速和减小内存消耗，除此之外还考虑了稀疏数据集和缺失值的处理，对于特征的值有缺失的样本，XGBoost依然能自动找到其要分裂的方向
	6）XGBoost支持并行处理，XGBoost的并行不是在模型上的并行，而是在特征上的并行，将特征列排序后以block的形式存储在内存中，在后面的迭代中重复使用这个结构。这个block也使得并行化成为了可能，其次在进行节点分裂时，计算每个特征的增益，最终选择增益最大的那个特征去做分割，那么各个特征的增益计算就可以开多线程进行。

6、LR + GBDT
	
	提出的原因：
	1）逻辑回归：线性模型学习能力有限，需要大量特征工程预先分析出有效的特征、特征组合，从而去间接增强LR的非线性学习能力。
	2）GBDT的思想使其具有天然优势可以发现多种有区分性的特征以及特征组合，决策树的路径可以直接作为LR输入特征使用，省去了人工寻找特征、特征组合的步骤。
    