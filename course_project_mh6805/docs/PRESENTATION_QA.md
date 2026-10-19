# 展示逻辑与双语答辩 Presentation Storyline and Bilingual Q&A

课程名称：**MH6805 Machine Learning in Finance**。以下是答辩准备笔记，不替代既有逐页讲稿。请自然表达，不逐字背诵，也不要把推测说成已完成实验。

Course: **Machine Learning in Finance**. These notes complement the slide-specific speaker notes. Answer from evidence and acknowledge untested questions.

## 1 建议叙事 Presentation Storyline

| 部分 Part | 核心问题 Key question | 建议证据 Evidence |
|---|---|---|
| 动机 Motivation | 为什么将ML用于选股 Why ML for selection? | 线性基准与非线性交互假设 Linear baseline and nonlinear interactions |
| 数据与任务 Data and task | 当时知道什么，预测什么 What is known and predicted? | 历史成分、公告时间、极端标签 Historical membership, release dates and labels |
| 公平比较 Fair comparison | 为什么信任评价 Why trust evaluation? | 同日期股票池、折内预处理、purging和时间CV Shared samples and temporal controls |
| 学习结果 ML evidence | 是否有增量信息 Is there predictive value? | AUC、log loss、Rank IC及训练测试差异 |
| 金融结果 Financial evidence | 是否转化为收益 Does prediction pay? | 扣成本净值、回撤、十分组及成本敏感性 |
| 解释与边界 Interpretation and limits | 为什么以及哪里不确定 Why and what remains uncertain? | 家族消融、置换重要性、时期差异和局限 |

时间分配应按老师给定展示时长调整，本资料不假设固定时限。Allocate time to the actual course presentation limit rather than assuming a duration.

## 2 需要记住的结果 Numbers to Remember

- RF测试正确率54.56%、AUC0.555；LGBM为54.00%、0.550。Test metrics, not training accuracy.
- 2020-10-20至2025-04-30，Top 10扣成本累计收益：RF60.04%、LGBM80.21%、XGB58.47%、LR-1.50%，指数-7.32%。Same-period cost-adjusted outcomes.
- RF最大回撤17.34%，LGBM19.80%；这不证明RF在所有时期更安全。Recorded drawdowns, not universal risk rankings.
- 25个因子、182个信号日期、4个模型、每模型3个候选、训练期3折CV。Protocol facts.

## 3 双语问答 Bilingual Q&A

### Q1 What is your actual machine learning task 你们究竟预测什么

**中文答**：我们训练分类器区分未来相对收益最高与最低的两组，然后对完整合格股票池打分排序。不是精确股价预测，也不是把中间组提前排除后再选股。

**English answer**: We classify the upper and lower future relative-return groups, then rank every eligible stock using the scores. We do not forecast exact prices or use future labels to exclude contemporaneous candidates.

### Q2 Why Logistic Regression 为什么加入逻辑回归

**中文答**：它提供带正则化的线性log-odds基准，帮助判断树模型的额外复杂度是否带来增量。它在本样本较弱不代表线性模型普遍无效。

**English answer**: It is a regularized linear log-odds baseline. It tests whether nonlinear ensembles add value beyond a simpler specification; its weaker result here does not invalidate linear models generally.

### Q3 Why not a neural network 为什么不用神经网络

**中文答**：本研究是结构化25因子的有限时期面板，重点在可审核的比较和时间约束。神经网络是可研究的扩展，但我们没有做实验，因此不能说它一定不如树模型。

**English answer**: We prioritize an auditable comparison on a modest tabular feature set with limited temporal observations. Neural networks are a possible extension, but we did not test them and cannot claim they would be inferior.

### Q4 Why are RF and boosting different 随机森林与提升树的区别

**中文答**：RF随机化训练多树并平均，主要降低方差；Boosting顺序添加树减少当前损失，更容易学习复杂结构，也可能追随噪声。二者都需要复杂度控制。

**English answer**: RF averages randomized trees, mainly reducing variance. Boosting sequentially adds trees to improve the objective, potentially learning richer structure or noise. Neither avoids overfitting automatically.

### Q5 Why does boosting not always win 为什么Boosting不一定最好

**中文答**：表达能力强不等于未来泛化强，金融信号弱且随时期变化。当前RF测试指标略优，LGBM投资收益更高，不能归纳为某一范式普遍获胜。

**English answer**: Greater flexibility does not guarantee future generalization in noisy, changing markets. RF leads our test metrics, while LGBM leads later portfolio returns; this is not universal algorithmic dominance.

### Q6 Where did you tune hyperparameters 参数在哪里调的

**中文答**：训练期内部3折扩展时间CV，以平均log loss为主，外部验证log loss只用于平局判定。每模型3个预设配置。测试和后续回测不选参数。

**English answer**: We select among three settings per model using mean log loss over three expanding training-period folds. Separate validation loss breaks ties. Test metrics and later backtest profit do not select parameters.

### Q7 What does purging remove Purging删掉什么

**中文答**：如果训练行的未来收益标签终点触及下一评估段起点，就删掉这条训练标签。只看信号日期先后还不够，因为标签覆盖未来一段时间。我们没有实现额外完整embargo。

**English answer**: We remove training labels whose realization ends at or after the next evaluation start. Signal-date separation alone is insufficient when labels span time. A separate full embargo scheme is not implemented.

### Q8 Why no random train test split 为什么不能随机划分

**中文答**：部署面对未来，不是历史中随机遗漏的股票行。同日和跨期相关性、标签重叠使随机划分过于乐观。我们按日期分段，但也不因此声称所有相关性消失。

**English answer**: Deployment faces future dates, not randomly withheld historical rows. Shared shocks and overlapping outcomes can make random splits optimistic. Chronological grouping helps, but does not make the observations independent.

### Q9 Do you use future information 是否用了未来信息

**中文答**：未来收益用于标签是正常的，但输入只能用信号时已可用信息。我们控制公告时间、预处理、候选池与跨界标签；财务修订历史版本仍不完全可验证，所以不说彻底无泄漏。

**English answer**: Future returns are legitimate labels, not admissible current inputs. We control release timing, preprocessing, eligibility and label boundaries, while acknowledging unresolved vendor-vintage uncertainty.

### Q10 Why is accuracy only about 54 percent 为什么正确率只有54%左右

**中文答**：金融数据噪声大，优势可能很小；但54%本身不证明有经济价值，还要看损失、排序、日期不确定性及扣成本收益。它不是全部股票涨跌正确率。

**English answer**: Financial signals can be weak, but 54% alone proves neither usefulness nor significance. We also evaluate loss, ranking, date-level uncertainty and cost-adjusted portfolios. This accuracy concerns extreme relative-return classes.

### Q11 Why RF accuracy but LGBM returns RF正确率更好为何LGBM收益更高

**中文答**：分类指标看极端标签整体；Top 10只看评分最高少数股票，受收益幅度、集中风险和成本影响。训练目标不是组合收益最大化。

**English answer**: Classification averages over labeled extremes, whereas Top 10 returns depend on a few selected names, payoff magnitudes, concentration and costs. The training loss does not directly maximize portfolio return.

### Q12 Which model is best 哪个模型最好

**中文答**：RF是较稳健的预测和回撤参照，LGBM是这段回测收益领先者，没有全维度赢家。若现在据此选择未来部署模型，应另留未见数据，而不是再使用这段回测证明。

**English answer**: RF is the stronger predictive and lower-drawdown reference, while LGBM leads realized return here. There is no all-purpose winner. Selecting now requires another untouched evaluation before making deployment claims.

### Q13 What prevents overfitting 如何防止过拟合

**中文答**：浅树、叶子约束、低学习率、采样、正则化和时间验证共同限制。不是所有模型参数完全一样，也没有用early stopping。当前证据不能证明过拟合完全消失。

**English answer**: We combine depth/leaf constraints, shrinkage, sampling, regularization and temporal validation. We do not use early stopping, and the controls do not certify that all overfitting has disappeared.

### Q14 Are your scores probabilities 分数是概率吗

**中文答**：接口给出正类分数，但训练只来自极端组，且没有另做校准，不能当作全股票池跑赢概率。Top 10使用排序，不依赖绝对阈值。

**English answer**: The classifier returns positive-class scores, but extreme-group selection and absent calibration prevent interpreting them as unconditional outperformance probabilities. Portfolio construction uses ranks.

### Q15 Why these factors 为什么选择这些因子

**中文答**：估值、质量、成长、风险交易等家族覆盖不同经济假设，并能从当前缓存构造。不是每个因子都被证实有效。消融显示信息组合及冗余，不能据测试结果反复筛选仍叫独立验证。

**English answer**: The families represent different economic hypotheses and available fields. Not every factor is proven useful. Ablations describe complementarity and redundancy; they are not permission to repeatedly select features on the same held-out data.

### Q16 What does importance mean 重要性意味着什么

**中文答**：它衡量打乱一列后模型测试损失增加多少，说明预测依赖，不是因果、经济风险溢价或SHAP归因。相关特征会互相替代。

**English answer**: Permutation importance measures the change in held-out loss after shuffling a feature. It is predictive reliance, not causality, a risk premium or SHAP attribution. Correlated substitutes complicate interpretation.

### Q17 Are the differences significant 差异显著吗

**中文答**：没有对所有模型收益差作正式显著性结论。我们对日期级Rank IC使用区块bootstrap描述不确定性，不能把股票行数当独立样本数。

**English answer**: We do not claim formal significance for all model-return differences. Block bootstrap intervals describe date-level Rank IC uncertainty; panel rows are not independent observations.

### Q18 Why add transaction costs 为什么必须扣成本

**中文答**：每期重新排序会引起真实买卖，预测信号可能被成本吃掉。我们按双侧实际交易金额扣成本并做0/5/10/20bps敏感性，但没有完整冲击和容量模型。

**English answer**: Rebalancing turns rankings into costly trades. We charge traded notional on both sides and test 0/5/10/20 bps, but do not fully model impact or capacity.

### Q19 Does the hedge solve drawdown 对冲能解决回撤吗

**中文答**：可能降低市场暴露，但不保证beta中性或无风险。本项目是50/50收益差诊断，借券与期货执行不完整，不能直接实盘。

**English answer**: Hedging may reduce directional exposure, not guarantee beta neutrality or eliminate risk. Our 50/50 return spreads are diagnostic rather than complete borrow/futures execution books.

### Q20 Is this alpha 这是alpha吗

**中文答**：目前只能说样本中相对指数表现较好，未完成完整风险模型调整。行业、规模和其他暴露可能贡献收益，不能直接叫纯alpha。

**English answer**: We observe benchmark-relative performance, not fully risk-adjusted alpha. Sector, size and other exposures may contribute; a complete attribution model is not included.

### Q21 Why are current returns lower than the archive 为什么不再有665%的收益

**中文答**：旧探索存在回测选参与候选池标签筛选问题，且窗口与账本不同。课程版重新规范实验，旧665.32%不是课程结论。不能只说是因为参数更保守。

**English answer**: The archive used backtest-based selection and label-filtered candidates, with a different window and ledger. Corrected eligibility, timing and evaluation change the experiment; the old 665.32% is not a course result.

### Q22 Could classmates reproduce it 同学能复现吗

**中文答**：报告、汇总表、代码和无行情测试已公开。完整重跑还需要授权缓存；设置Token不会自动下载数据。公共结果检查不等于重新训练。

**English answer**: Reports, aggregate evidence, source and deterministic tests are public. Full reproduction requires authorized caches; setting a token does not download them. Saved-result validation is not fresh fitting.

### Q23 What is the most important next step 最重要的下一步

**中文答**：先提高历史版本和可成交性验证，再冻结新增方案，用新时间段检验。滚动重训、行业约束、校准或SHAP都可研究，但不是现在已完成的证据。

**English answer**: Improve vintage and execution validation, then freeze extensions and test them on a new period. Walk-forward refits, exposure controls, calibration and SHAP are possible studies, not completed evidence.

## 4 不要说与建议说 Avoid and Prefer

| 不要说 Avoid | 建议说 Prefer |
|---|---|
| Boosting一定比RF好 Boosting always beats RF | 不同指标与时期排名不同 Rankings depend on objective and period |
| 54%说明每天赚钱概率54% 54% chance of daily profit | 极端相对收益组的分类正确率 Accuracy on extreme relative-return groups |
| 所有未来信息已经消除 All leakage eliminated | 多项时点控制，但历史版本仍有限 Controls with remaining vintage uncertainty |
| 验证集找到了全局最优 Global optimum on validation | 有限候选、训练时间CV主选参 Finite training-CV selection |
| SHAP证明换手率导致收益 SHAP proves turnover causes returns | 实际做的是置换重要性 Predictive permutation importance |
| 对冲图就是能成交的市场中性产品 Executable market-neutral fund | 简化收益差诊断 Stylized diagnostic return spread |

## 5 最后30秒 Closing Statement

**中文**：我们的主要贡献不是报告最高的历史收益，而是把信息时间、模型验证和投资评价串成可审核流程。树模型在本样本中显示一定增量价值，但最佳分类模型和最佳投资收益模型不同。下一步应在更真实的数据版本和执行条件下，用未见时期验证。

**English**: Our contribution is an auditable link between information timing, model validation and investment evaluation, not the largest historical profit number. Tree models show incremental value in this sample, but predictive and portfolio rankings differ. Further claims require better vintage and execution data and a new untouched evaluation period.
