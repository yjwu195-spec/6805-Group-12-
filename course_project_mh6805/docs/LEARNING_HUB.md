# Machine Learning in Finance 学习与展示准备

[中文项目说明](../README.zh-CN.md) | [English project README](../README.md)

本资料服务于 **MH6805 Machine Learning in Finance** 的课堂展示与问答。它不是新增实证结果，也不代替报告；理论说明应与代码和结果表互相核对。所有数值均来自已发布课程实验，本次没有重新训练。

This learning pack prepares the group for the **Machine Learning in Finance** presentation and Q&A. It explains the existing experiment, not a new empirical run. Distinguish observed evidence, general theory and proposed future work.

## 阅读顺序 Reading Order

| 材料 Material | 重点 Focus | 建议时间 Suggested time |
|---|---|---|
| [机器学习核心知识 ML Core Concepts](ML_CORE_CONCEPTS.md) | 监督学习、四模型、损失、正则化、验证、解释 Supervised learning, models, loss, validation and interpretation | 45–60分钟 minutes |
| [量化金融基础 Quant Finance Foundations](QUANT_FINANCE_FOUNDATIONS.md) | 因子、超额收益、股票池、成本、回撤和对冲 Factors, returns, universes, costs and hedging | 25–35分钟 minutes |
| [展示与答辩 Presentation and Q&A](PRESENTATION_QA.md) | 讲述逻辑、英文答案、容易被追问的限制 Storyline, English answers and caveats | 30–45分钟 minutes |
| [术语速查 Glossary](BILINGUAL_GLOSSARY.md) | 展示前快速复习 Quick revision before presenting | 10分钟 minutes |
| [结果解读 Results Explained](RESULTS_EXPLAINED.md) | 把理论和本项目数字对应起来 Connect theory to actual evidence | 15分钟 minutes |

建议时间是学习安排，不是程序运行时间。The timings are study estimates, not computational benchmarks.

## 展示必须讲清楚的六句话 Six Essential Claims

1. **问题 Question**：我们比较非线性模型是否优于线性基准，以及预测优势能否转成扣成本后的投资表现。We compare nonlinear models against a linear baseline and ask whether predictive gains survive portfolio construction and trading costs.
2. **任务 Task**：这是极端相对收益组的监督分类，再把评分用于股票排序，不是预测精确股价。We train an extreme-group classifier and use its scores for cross-sectional ranking, not exact price forecasting.
3. **时间 Timing**：财报按公开时间使用，训练与评价按时间隔离，并清除跨界训练标签。We respect release timing, chronological evaluation and purging of training labels crossing evaluation boundaries.
4. **方法 Methods**：LR是线性对数几率基准，RF综合随机树，XGB/LGBM逐步优化提升树。LR is a linear log-odds baseline, RF aggregates randomized trees, and XGB/LGBM fit sequential boosted trees.
5. **证据 Evidence**：RF测试分类指标略优，LGBM后续Top 10收益最高；整体分类和集中组合是不同目标。RF leads test classification metrics, while LGBM leads later Top 10 returns; these objectives differ.
6. **边界 Limits**：结果受数据版本、成交约束和时期影响，不是实盘保证。Data vintages, execution assumptions and regimes limit the evidence; it is not a live-return guarantee.

## 三种不同的表述 Three Types of Statement

| 类型 Type | 合理例子 Appropriate example | 不能偷换成 Do not turn it into |
|---|---|---|
| 事实 Observation | LGBM本段扣成本累计收益80.21% LGBM returned 80.21% in the recorded cost-adjusted period | 未来也能获得同样收益 The same return is guaranteed in future |
| 解释 Interpretation | 非线性交互可能有增量信息 Nonlinear interactions may add useful information | 已证明某因子导致收益 A factor is proven to cause returns |
| 未来工作 Future work | 可以研究walk-forward或SHAP Walk-forward or SHAP could be studied next | 本项目已经实现两者 Both were implemented here |

## 从知识点定位代码 Code Map

| 知识点 Concept | 代码或证据 Code or evidence |
|---|---|
| 因子家族、财务可用时间 Factor families and release timing | `src/data.py` 的 `FAMILIES`、`financial_alignment` |
| 训练折预处理 Fold-fitted preprocessing | `src/modeling.py` 的 `TrainWinsorizer`、`make_model` |
| 模型与参数 Models and settings | `candidate_grid`、`results/selected_parameters.json` |
| 日期划分与清除跨界标签 Date splits and purging | `split_panel`、`run_models` |
| 准确率与排序指标 Classification and ranking | `classification`、`ranking`、`results/prediction_metrics.csv` |
| 解释与消融 Importance and ablations | `run_models`、`results/permutation_importance.csv`、`factor_ablations.csv` |
| 投资账本与指标 Portfolio accounting | `src/backtest.py`、`results/portfolio_metrics.csv` |

以上路径相对于课程目录。代码是具体实现的依据；官方参考资料用于解释一般原理。Paths are relative to the course directory. Source code determines what we implemented; official references explain general principles.

## 排练检查 Rehearsal Checklist

- 每位成员能解释标签、时间切分、为什么不能随机分割。Every member can explain labels, time splits and why random row splits are inappropriate.
- 记住RF与LGBM的主要数字，但不把正确率当收益率。Know the RF/LGBM headline numbers without confusing accuracy with return.
- 能指出训练期CV主选参、验证集只平局判定，而不是简单说“只在验证集调参”。State the actual training-CV selection rule and validation tie-break.
- 不声称用了early stopping、滚动重训、SHAP、行业中性化或完整融券。Do not claim early stopping, rolling refits, SHAP, industry neutrality or executable shorting.
- 回答问题时先给结论，再给方法依据，最后说限制。Answer with the conclusion, implementation evidence and limitation.
- 对未做实验的问题说“尚未验证”，不要编造数字。Say “not tested” rather than inventing evidence.
