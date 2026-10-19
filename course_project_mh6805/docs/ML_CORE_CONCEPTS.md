# 机器学习核心知识 Machine Learning Core Concepts

适用课程：**MH6805 Machine Learning in Finance**。本章按“原理—项目应用—展示表达”组织，重点解释现有代码，不把可选扩展说成已完成工作。数学符号为教学记号，不要求死记推导。

Course: **Machine Learning in Finance**. This note connects principles to the actual project. Proposed extensions are not implemented features. Symbols are teaching notation rather than an additional model specification.

## 1 监督学习与观测单位 Supervised Learning and the Observation Unit

**中文**：每行是一个“股票—信号日期”观测，输入向量是25个因子，标签来自之后一段相对指数收益。训练时用标签学习映射，实际打分只输入当时因子。同一天股票共享市场冲击，同一股票跨期也相关，77,034行不能视为77,034次独立试验。标签包含未来是正常监督学习；错误是让未来信息进入输入、选股资格或选参。

**English**: One observation is a stock at a signal date. The 25-feature vector predicts a later relative-return class. Future outcomes are valid training labels but cannot enter current features, eligibility or model selection. Stocks on the same date share shocks and observations repeat through time; the panel rows are not independent trials.

## 2 分类 回归与排序 Classification Regression and Ranking

**中文**：回归直接预测数值收益；分类学习离散组别；排序关心股票的相对先后。我们把每期未来超额收益前30%定义为1、后30%为0，训练二分类器，再按正类分数选Top 10。没有直接训练排序损失或预测精确收益。中间40%不训练，但必须留在当时合格打分股票池。分数0.6也不是“未来有60%概率跑赢”，因为训练样本被条件化到极端组，且未做概率校准。

**English**: Regression predicts a numerical target; classification predicts a class; ranking orders candidates. We train on the upper/lower 30% relative-return groups and rank the full eligible universe by the positive-class score. We do not optimize a learning-to-rank loss. A score of 0.6 is not a calibrated 60% probability of benchmark outperformance.

## 3 逻辑回归 Logistic Regression

$$p(y=1\mid x)=\sigma(b+w^\top x),\qquad \sigma(z)=\frac{1}{1+e^{-z}}.$$

**中文**：线性的是log-odds而不是概率。模型用加权因子总和经过sigmoid输出分数，搭配L2约束。`C`是正则化强度的倒数，越小通常收缩越强；本次选择`C=0.01`。它作为线性基准回答“更复杂模型是否真的有增量”，而非为了找一个故意较弱的对照。未显式加入交互项，因此不能像树那样自动形成条件组合；其系数也不是因果解释。

**English**: The log odds, not the probability, are linear in features. L2 regularization shrinks coefficients; smaller `C` means stronger regularization in this implementation. The selected `C` is 0.01. LR is an interpretable baseline for testing incremental nonlinearity, not a deliberately weak opponent. Coefficients do not establish causality. [Official reference](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression).

## 4 决策树 Decision Trees

**中文**：树连续提出阈值问题，把特征空间分成区域。例如“换手率是否较低，然后盈利增长是否较高”是教学上的条件组合，不是对已拟合树规则的引用。分类叶节点按训练样本类别构成给出分数。树能捕捉非线性、阈值和交互，但深树也可能记住偶然噪声。我们不是先手工指定所有因子必须方向单调。

**English**: A tree recursively partitions feature space using threshold questions. A hypothetical “low turnover, then strong growth” rule illustrates interaction; it is not an extracted fitted rule. Deep trees can represent complex patterns but also memorize noise. [Official reference](https://scikit-learn.org/stable/modules/tree.html).

## 5 随机森林与Bagging Random Forest and Bagging

$$\hat p(x)=\frac{1}{B}\sum_{b=1}^{B}\hat p_b(x).$$

**中文**：多棵树使用bootstrap抽样和节点随机特征子集学习，最后平均各树类别概率；不是简单平均每棵树的硬分类标签。随机性降低树之间相关性，平均有助于降低方差，但不会自动消除数据泄漏或市场变化。若树高度相关，增加树数的收益会递减。本次RF选160棵树、最大深度4、叶节点最少100条训练观测，`max_features='sqrt'`。叶节点观测数是股票日期行，不是100只互相独立的股票。

**English**: RF averages class probabilities from randomized trees. Bootstrap samples and feature subsets promote diversity; averaging can reduce variance, but it cannot repair leakage or regime changes. Our RF uses 160 trees, depth 4 and a minimum leaf size of 100 panel observations. [Ensemble reference](https://scikit-learn.org/stable/modules/ensemble.html#forests-of-randomized-trees).

## 6 梯度提升 Gradient Boosting

$$F_m(x)=F_{m-1}(x)+\eta f_m(x).$$

**中文**：新的树沿着减少损失的方向修正当前模型；二分类logistic损失下工作在分数/log-odds空间，不是简单把最终概率相加。对log loss，原始分数的梯度为`p-y`，负梯度为`y-p`；XGBoost还利用二阶信息及树复杂度惩罚。Boosting不是“每次只学习错误样本”，也不是“只能串行、完全不能并行”：轮次有顺序依赖，但每轮内部可以并行。

**English**: New trees improve the current objective in score space; probabilities are obtained after the link function. For logistic loss the score gradient is `p-y`. Boosting uses gradient information, not only a list of misclassified samples. Rounds are sequentially dependent, while substantial work within a round can be parallelized. [Boosted-tree reference](https://xgboost.readthedocs.io/en/stable/tutorials/model.html).

## 7 XGBoost与LightGBM XGBoost versus LightGBM

**中文**：二者都属于梯度提升树，不是两个完全不同的学习范式。LightGBM常用leaf-wise增益扩展，需要限制叶子数和深度；本项目XGB采用`hist`算法及默认的depth-wise增长政策。不能把所有XGB配置都说成固定层级生长，也不能说只有LGBM才用直方图。本次都用学习率0.03和较强正则化；没有启用early stopping，迭代数来自预设候选。

**English**: Both are gradient-boosted tree systems. Our XGB uses histogram training with its default depth-wise growth policy; LGBM uses leaf-wise growth with explicit leaf/depth limits. Histogram methods are not unique to LGBM. This project uses fixed candidate iteration counts, not early stopping. [LightGBM tuning](https://lightgbm.readthedocs.io/en/stable/Parameters-Tuning.html).

## 8 偏差 方差与过拟合 Bias Variance and Overfitting

**中文**：欠拟合可能遗漏真实结构，过拟合把历史噪声当成规律。更深的树通常提高表达能力，也可能增加不稳定性；浅树和大叶子约束可能更稳但遗漏细节。经典平方误差的bias-variance分解是直觉工具，不能原样当作准确率的数值分解。本项目金融信噪比低，保守参数是控制风险的设计，不是“参数越小一定越好”。训练和测试差距只能提供线索，不能单独证明全部过拟合已消除。

**English**: Underfitting misses structure; overfitting learns sample-specific noise. Complexity controls trade flexibility against stability. The classic squared-error bias–variance decomposition is intuition, not an exact decomposition of classification accuracy. A smaller train–test gap does not certify that all overfitting has disappeared.

## 9 超参数与学习参数 Hyperparameters and Learned Parameters

**中文**：LR系数、树的分裂阈值和叶子值是训练学到的参数；树深、学习率、候选树数和正则化系数是超参数。本次每模型只有3种预设配置，不是无限搜索或全局最优。RF候选的深度与叶子数约束成对变化，因此单凭搜索结果不能严格分离每一个超参数的独立贡献。

**English**: Coefficients, split thresholds and leaf values are learned parameters. Depth, learning rate, iteration budget and regularization are hyperparameters. Three predefined configurations per model provide a finite comparison, not global optimization or an isolated causal experiment on each hyperparameter.

| 超参数 Hyperparameter | 作用 Meaning | 本项目提醒 Project caveat |
|---|---|---|
| `max_depth` | 限制路径深度 Limits tree depth | 小深度也可跨树组合非线性 Shallow ensembles can still be nonlinear |
| `n_estimators` | 树数/提升轮数 Tree/round budget | RF多树与Boosting多轮含义不同 Different implications for RF and boosting |
| `learning_rate` | 每次提升步长 Shrinkage per round | 本次0.03，需与轮数联合理解 Jointly interpret with round count |
| `min_samples_leaf` | RF叶节点最少样本 Minimum RF leaf observations | 股票日期行而非独立试验 Panel rows, not independent trials |
| `min_child_weight` | XGB叶节点最少Hessian权重和 Minimum Hessian-weight sum | **不是最少样本条数 Not a minimum row count** |
| `min_child_samples` | LGBM叶样本约束 Leaf data constraint | 不等同XGB的Hessian阈值 Not equivalent to XGB's threshold |
| `num_leaves` | LGBM叶子数量约束 Leaf budget | 与深度共同控制 Joint control with depth |
| `subsample` | 行采样比例 Row sampling | LGBM还需启用`subsample_freq`；这里为1 LGBM frequency is enabled |
| `colsample_bytree` | 每树列采样 Column sampling | 降低依赖不等于移除相关性 Not elimination of feature correlation |
| `reg_alpha`, `reg_lambda` | XGB/LGBM叶权重L1/L2惩罚 Leaf-weight penalties | 控制复杂度，不保证泛化 No guarantee of generalization |

参数语义参考 [XGBoost官方参数](https://xgboost.readthedocs.io/en/stable/parameter.html) 与 [LightGBM官方参数](https://lightgbm.readthedocs.io/en/stable/Parameters.html)。运行以锁定依赖版本为准，在线文档可能更新。The locked project versions govern execution; online references may be newer.

## 10 损失函数与log loss Loss and Log Loss

$$L=-\frac{1}{n}\sum_i\left[y_i\log p_i+(1-y_i)\log(1-p_i)\right].$$

**中文**：log loss同时关注预测方向与置信程度，错误且自信的预测受罚更大。准确率只看分数跨不跨0.5，难以区分0.51和0.99。本项目用平均时间CV log loss选配置，能避免为了回测收益直接选参，但不会自动把组合收益最大化。教学例子：真实标签为1时，预测0.6损失约0.511，预测0.1损失约2.303；这是算术示例，不是额外测试结果。

**English**: Log loss penalizes confident mistakes and evaluates probability scores beyond a fixed classification threshold. It is our CV selection objective, not a direct portfolio-return objective. The numerical example is illustrative arithmetic, not an experiment. [Metric reference](https://scikit-learn.org/stable/modules/model_evaluation.html#log-loss).

## 11 训练折预处理 Fold Fitted Preprocessing

**中文**：每折依次执行训练分位数1%/99%缩尾、训练中位数填充、训练均值与标准差标准化。测试使用这些已拟合参数，不重新利用全样本统计量。缩尾降低极端数值影响，不能消除所有异常或经济危机。标准化对LR的重要性高于按阈值分裂的树；统一流水线为了实验一致，不代表树必须标准化。原始数据不被填充后的数覆盖；训练列完全缺失时以常数零转换是实现规则，不是发明财务观测。

**English**: Winsorization, imputation and scaling are fitted within each training fold and reused on evaluation data. Standardization particularly matters for regularized LR; threshold trees are generally much less scale-sensitive. A shared pipeline improves consistency, not proof that trees require scaling. [Leakage and preprocessing reference](https://scikit-learn.org/stable/common_pitfalls.html).

## 12 时间验证与Purging Temporal Validation and Purging

**中文**：随机分股票日期行会让同一天市场状态甚至相邻标签跨入两侧，不符合未来应用。先按唯一日期分段，训练期内部再做3折扩展窗口。对下一评估段首日`t`，训练样本收益标签终点必须严格早于`t`。这叫清除跨界标签，区别于简单随机CV；我们没有额外实现完整双侧embargo。外部验证集损失只作为CV均值完全相同时的下一排序键，不是在所有指标中主观择优。

**English**: Splits use unique chronological dates. Three expanding folds operate inside training; training label exits must precede the next evaluation start. Purging addresses label-horizon overlap; a separate full embargo scheme is not implemented. External validation loss is a lexicographic tie-break, not a subjective second objective. [TimeSeriesSplit reference](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TimeSeriesSplit.html).

## 13 评价指标与阈值 Metrics and Thresholds

**中文**：准确率是正确类别比例；balanced accuracy对两类召回率平均；F1综合正类precision与recall；AUC看正类分数是否排在负类前面，对整体单调分数转换不变；log loss看概率损失。AUC不是“正确率”，也不是“每天赚钱概率”。阈值0.5仅用于分类表，Top 10按相对分数选，不要求分数都大于0.5。F1不能反映所有真负类表现，也不包含交易成本。

**English**: Accuracy, balanced accuracy and F1 use thresholded predictions; ROC AUC evaluates positive–negative ordering across thresholds, while log loss evaluates probability scores. Top 10 selection uses relative scores, not a requirement that scores exceed 0.5. No classification metric directly accounts for portfolio costs or payoff magnitudes.

## 14 泛化与市场变化 Generalization and Distribution Shift

**中文**：未来特征分布、类别条件关系和经济制度都可能改变。固定随机种子只帮助重现实验，不解决制度变化；固定模型的后续表现也不等同滚动重训策略。本次最终模型在训练加验证标签上拟合后，对测试和后续回测保持固定；不是每月重新拟合。分年度分析帮助看差异，但不能据此保证跨制度稳定。

**English**: Feature distributions and feature–outcome relationships can change. A fixed seed supports reproducibility, not regime robustness. The course evaluates frozen final estimators, not monthly rolling refits. Annual slices describe instability without proving robustness across all regimes.

## 15 可解释性与消融 Interpretation and Ablation

**中文**：置换重要性在未训练的测试样本上打乱某一列，观察log loss恶化；我们抽取1,500条真实标签行、重复3次。相关替代变量会分摊或掩盖贡献；负重要性也可能反映噪声。家族消融是删除一类信息后重新拟合，研究信息集差异，不等同单列置换。消融参数保持全因子所选值，不能将其解释成每个因子子集都被充分调优。没有计算SHAP，不能在展示中引用不存在的SHAP图。

**English**: Held-out permutation importance and refitted family ablations answer different questions. Correlated substitutes complicate attribution. We use 1,500 real labeled test rows with three permutation repeats and fixed full-model hyperparameters for ablations. Importance is predictive, not causal. SHAP was not computed. [Importance reference](https://scikit-learn.org/stable/modules/permutation_importance.html).

## 16 统计显著性与多重比较 Uncertainty and Multiple Comparisons

**中文**：54.56%大于54.00%只是点估计，不能据此宣称RF显著优于LGBM。股票日期相关性让普通独立二项检验不合适。本次对日期级Rank IC做6期循环区块bootstrap、2,000次重抽样，帮助描述均值不确定性，不是对所有模型收益差做了显著性检验。多模型、多因子和多时期比较存在挑选赢家风险；这些结果不能反复用于优化后仍叫独立检验。

**English**: Point-estimate differences are not significance tests. Our six-period circular block bootstrap describes uncertainty in date-level mean Rank IC, not all pairwise portfolio-return differences. Multiple exploratory comparisons and limited temporal observations weaken winner claims. A new untouched evaluation is needed after model selection based on these results.

## 17 已实现与未实现 Implemented versus Not Implemented

| 已实现 Implemented | 尚未实现 Not implemented |
|---|---|
| 四个监督分类器 Four classifiers | 直接收益回归或learning-to-rank Direct return regression/ranking loss |
| 三候选和3折时间CV Three candidates and three temporal folds | 大规模贝叶斯优化或全局最优 Large Bayesian search/global optimum |
| 跨界标签清除和折内预处理 Purging and fold-fitted transforms | 完整embargo或全部历史版本还原 Full embargo/vintage reconstruction |
| 置换重要性和家族消融 Permutation importance and family ablations | SHAP或因果识别 SHAP/causal identification |
| 固定最终模型 Frozen final estimators | 滚动重训或在线学习 Rolling refits/online learning |
| 每日组合账本 Daily portfolio ledger | 完整撮合与可成交融券 Complete execution/borrow simulation |

## 18 展示时的一分钟英文总结 One Minute Summary

We formulate stock selection as supervised classification of extreme future relative-return groups and use the scores to rank the full eligible universe. Logistic Regression provides a linear baseline, Random Forest aggregates randomized trees, and XGBoost and LightGBM sequentially improve regularized tree ensembles. We control complexity and use purged chronological cross-validation rather than selecting parameters by backtest profit. Random Forest leads our independent test classification metrics, whereas LightGBM leads later concentrated-portfolio returns. This difference reflects the distinction between average prediction quality and the payoff of a small selected portfolio. We report interpretation and robustness analyses, while acknowledging correlated observations, regime changes and execution limitations.
