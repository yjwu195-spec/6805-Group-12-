# 双语术语速查 Bilingual Glossary

用于 **Machine Learning in Finance** 展示复习。简短定义以本项目语境为准。Short definitions use this project's context; consult the detailed notes for qualifications.

## 机器学习 Machine Learning

| English | 中文 | 展示时如何理解 Meaning in this project |
|---|---|---|
| Supervised learning | 监督学习 | 用特征和已实现标签学习 Learn from features and realized labels |
| Feature | 输入特征 | 信号时可用的股票属性 A characteristic available at signal time |
| Label | 标签 | 未来相对收益极端组 Future relative-return extreme group |
| Classification | 分类 | 区分高低两组 Separate high/low groups |
| Regression | 回归 | 预测连续数值 Predict a continuous value; not our implemented target |
| Ranking | 排序 | 比较同日股票分数 Order contemporaneous candidates |
| Log odds | 对数几率 | `log(p/(1-p))`，LR线性部分 LR's linear predictor |
| Decision tree | 决策树 | 用阈值递归分区 Recursive threshold partition |
| Leaf | 叶节点 | 树的终端区域 Terminal region of a tree |
| Ensemble | 集成学习 | 组合多个学习器 Combine multiple estimators |
| Bootstrap aggregation | 自助聚合 Bagging | 有放回抽样并聚合 Resample with replacement and aggregate |
| Random Forest | 随机森林 | 随机树概率平均 Average randomized-tree probabilities |
| Gradient boosting | 梯度提升 | 顺序添加树优化损失 Sequential objective improvement |
| Learning rate | 学习率 | 每轮提升步长 Shrinkage per boosting round |
| Regularization | 正则化 | 限制复杂度或参数幅度 Control complexity/parameter magnitude |
| L1 penalty | L1惩罚 | 绝对值惩罚 Absolute-value penalty |
| L2 penalty | L2惩罚 | 平方惩罚 Squared penalty |
| Hyperparameter | 超参数 | 训练前确定的复杂度等设定 Configuration selected outside fitting |
| Learned parameter | 学习参数 | 训练得到的系数或树规则 Fitted coefficient/tree rule |
| Bias | 偏差 | 简化模型遗漏规律的倾向 Systematic approximation error |
| Variance | 方差 | 拟合对样本扰动的敏感性 Sensitivity to sample changes |
| Underfitting | 欠拟合 | 复杂度不足 Missed structure from insufficient flexibility |
| Overfitting | 过拟合 | 学习样本特有噪声 Learning sample-specific noise |
| Generalization | 泛化 | 在未见数据上的表现 Performance on unseen observations |
| Distribution shift | 分布变化 | 未来分布或关系变化 Changed distributions/relationships |
| Cross-validation | 交叉验证 | 多个训练评价划分 Repeated fitting/evaluation splits |
| Expanding window | 扩展窗口 | 训练历史逐步增多 Growing historical training window |
| Purging | 清除跨界标签 | 删除未来收益终点越界的训练行 Remove boundary-crossing training labels |
| Embargo | 隔离缓冲 | 额外时段排除；未完整实现 Additional time exclusion; not fully implemented |
| Data leakage | 数据泄漏 | 不该可见的信息进入学习 Improper evaluation/future information use |
| Winsorization | 缩尾 | 将超阈值特征截断 Clip to fitted quantiles |
| Imputation | 缺失填充 | 模型输入阶段填补 Missing-feature transformation, not fabricated raw inputs |
| Standardization | 标准化 | 按训练均值标准差变换 Scale with training mean/standard deviation |
| Log loss | 对数损失 | 对自信错误惩罚更重 Probability-score loss |
| Accuracy | 正确率 | 阈值分类正确比例 Fraction of correct class predictions |
| Precision | 精确率 | 预测正类中真正类比例 True positives among predicted positives |
| Recall | 召回率 | 真正类中被识别比例 Detected positives among actual positives |
| F1 score | F1分数 | Precision与recall调和均值 Harmonic mean of precision and recall |
| ROC AUC | ROC曲线下面积 | 正负类分数排序区分 Discrimination of positive versus negative scores |
| Calibration | 概率校准 | 概率与实际频率一致性 Agreement between probabilities and frequencies |
| Permutation importance | 置换重要性 | 打乱特征后的评价恶化 Evaluation deterioration after shuffling |
| Ablation | 消融 | 移除信息后重新比较 Compare after removing information |
| SHAP | Shapley归因 | 可选解释方法；本项目未计算 Optional attribution method, not computed here |
| Statistical significance | 统计显著性 | 在明确假设下评估差异 Not established by a point-estimate ranking |
| Block bootstrap | 区块自助法 | 按时间块重抽样 Resample temporal blocks |
| Multiple testing | 多重检验 | 多次比较提高偶然赢家风险 Many comparisons create selection risk |

## 量化金融 Quantitative Finance

| English | 中文 | 展示时如何理解 Meaning in this project |
| Cross-section | 横截面 | 同一日期的多只股票 Stocks on a common date |
| Panel data | 面板数据 | 多股票跨时间 Stock-by-time observations |
| Unbalanced panel | 非平衡面板 | 各期合格股票数不同 Unequal coverage through time |
| Stock universe | 股票池 | 当时可入选候选集合 Contemporaneously eligible candidates |
| Historical constituents | 历史成分股 | 当期快照成员 Dated index membership |
| Survivorship bias | 幸存者偏差 | 按未来存续筛选历史样本 Future survival influences historical selection |
| Look-ahead bias | 前视偏差 | 决策使用未来才可知信息 Decision uses later information |
| Announcement date | 公告日期 | 财报可用时间依据 Release-timing input |
| Report period end | 报告期末 | 财务覆盖截止日，不是公开日 Accounting period end, not publication |
| Data vintage | 数据历史版本 | 当时实际可见的版本 Version actually available at the time |
| Excess return | 相对/超额收益 | 个股收益减基准 Stock return minus benchmark |
| Alpha | 风险调整后增量收益 | 需明确风险模型，非自动等于超额收益 Requires a specified risk model |
| Beta | 市场敏感度 | 对市场收益变化的暴露 Sensitivity to market return |
| Rebalance | 调仓 | 按新目标重新分配资产 Trade toward updated targets |
| Turnover | 换手 | 买卖两侧成交额除权益 Both-side notional divided by equity |
| Basis point | 基点 | 1bps=0.01%=0.0001 |
| Slippage | 滑点 | 实际成交与假定价格差 Execution deviation from assumed price |
| Market impact | 市场冲击 | 自己订单改变成交价格 Own trading affects prices |
| NAV | 净值 | 初始资金归一化后的价值 Normalized portfolio value |
| Drawdown | 回撤 | 相对历史峰值的下降 Decline from running peak |
| Sharpe ratio | 夏普比率 | 平均超额日收益相对波动 Mean excess return relative to volatility |
| Rank IC | 秩相关信息系数 | 分数与未来收益的横截面秩相关 Cross-sectional rank correlation |
| Decile portfolio | 十分组组合 | 分数排序分成10组 Ten score-sorted groups |
| Gross exposure | 总敞口 | 多头绝对敞口加空头绝对敞口 Sum of absolute long/short exposures |
| Net exposure | 净敞口 | 多头减空头 Long exposure minus short exposure |
| Hedging | 对冲 | 降低特定暴露，不是消除全部风险 Reduce a specified exposure |
| Borrow availability | 可融券性 | 空头股票是否能借到 Ability to borrow a security |
| Capacity | 策略容量 | 在可接受冲击下能运行的规模 Feasible capital before excessive impact |

## 容易混淆的配对 Common Confusions

- **Training versus testing**：记忆历史不等于泛化。Fitting history is not future generalization.
- **Accuracy versus return**：分类正确数量不衡量盈亏大小。Correct counts ignore payoff magnitude.
- **Prediction versus causality**：有用关联不证明因果。Predictive association is not causal identification.
- **Backtest versus live execution**：模拟账本不等于真实成交。Simulated accounting is not actual execution.
- **Reproducibility versus validity**：可以重复同一个偏差结果。A biased result can be reproducible.
- **Validation versus untouched evaluation**：被反复用于决策的数据就不再未见。Repeatedly used evaluation data become selection data.
