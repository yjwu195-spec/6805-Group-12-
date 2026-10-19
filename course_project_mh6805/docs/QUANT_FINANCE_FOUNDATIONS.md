# 量化金融基础 Quantitative Finance Foundations

本章解释 **Machine Learning in Finance** 中“金融”部分如何约束机器学习实验。公式是本项目指标约定，不是投资建议。This note explains the financial constraints on the ML experiment. Formulas describe project conventions, not investment advice.

## 1 因子并不是模型系数 A Factor Is Not a Model Coefficient

**中文**：这里因子是可观察的股票特征，如估值、盈利增长、换手率。ML学习这些特征与未来标签之间的关系。资产定价模型中的风险因子、股票特征和ML输入特征不是完全同一个概念；我们没有估计完整的经济风险溢价模型，因此正收益不等于已识别独立alpha。

**English**: Factors here are stock characteristics used as ML features. They are not automatically traded risk factors or causal sources of premia. A profitable portfolio does not, by itself, establish risk-adjusted alpha under a complete asset-pricing model.

## 2 横截面选股与时间择时 Cross Sectional Selection versus Market Timing

**中文**：横截面问题是“同一时点哪些股票相对更好”，择时是“何时提高或降低市场仓位”。本项目主要是横截面Top 10选股，不会因所有分数偏低就自动空仓，也没有显式宏观择时模型。个股特征含历史时间窗口，但并不因此变成纯时间序列择时。

**English**: Cross-sectional selection compares stocks at the same date; timing changes market exposure through time. Our primary strategy selects Top 10 stocks rather than predicting whether the whole market should be held. Historical windows in features do not make it a pure market-timing strategy.

## 3 收益与超额收益 Return and Excess Return

$$r_{i,t\to t'}=\frac{P^{adj}_{i,t'}}{P^{adj}_{i,t}}-1,\qquad e_{i,t}=r_{i,t\to t'}-r_{CSI500,t\to t'}.$$

**中文**：标签用下一次执行开盘到再下一次执行开盘的个股复权收益减指数同期收益。“相对更好”不必是绝对上涨，例如股票跌2%、指数跌5%，相对收益为正3个百分点。我们用价格乘供应商复权因子构造调整价格单位，但没有逐笔还原真实持股股数和股息现金，因此不应直接称为完整实盘总回报核算。

**English**: Label returns run between execution opens and subtract matched CSI 500 returns. Relative outperformance can occur during an absolute loss. The adjusted-price-unit ledger is not a complete actual-share and dividend-cash accounting system.

## 4 历史成分与幸存者偏差 Historical Membership and Survivorship Bias

**中文**：用今天成分回看历史会遗漏过去被剔除或退市的成员，未来存续情况就进入了历史选择。课程版使用当时最近历史快照，并保留退市处理。但历史快照可能覆盖不完整，其日期也未必等于正式对外发布时间，所以只能说采取了控制措施，不能说无条件彻底消除所有股票池偏差。

**English**: Today's constituents can select future survivors into the past. Historical snapshots reduce that problem, but snapshot completeness and publication timing remain limitations. “Controlled” is more accurate than “all universe bias eliminated.”

## 5 财务时点和前视偏差 Accounting Timing and Look Ahead Bias

**中文**：12月31日的年度财务信息通常不是12月31日就能获知。我们使用公告可用日期并加保守延迟，再以信号日向后匹配。供应商可能把后来修订的历史财报留在缓存中，即使日期匹配正确，也未必还原了当时版本。这是“事件时间”和“数据版本”两个不同问题。

**English**: Reporting-period end and public availability are different clocks. Announcement-based matching addresses event timing, while historical restatements require vintage data. Correct date joins alone cannot certify original-vintage information.

## 6 换手率和成本 Turnover and Costs

$$TO_t=\frac{|\text{buy notional}_t|+|\text{sell notional}_t|}{\text{pretrade equity}_t},\qquad \text{cost}_t=c\times\text{traded notional}_t.$$

**中文**：这里换手按买卖两侧合计，完全卖出再买入另一组合时可接近200%，不能自动当成计算错误。10bps=0.1%=0.001，按每一侧实际成交金额扣。持仓价格变化使调仓前权重漂移，不能只看股票名单变化。成本参数是统一近似，不是逐项重建历史印花税、手续费、冲击和滑点；高换手和容量约束仍需进一步分析。

**English**: Turnover includes both buys and sells and can approach 200% for a complete replacement. Ten bps equals 0.1% of each side's traded notional. Weight drift matters. The cost parameter is an approximation, not a complete historical tax, fee, slippage and impact schedule.

## 7 净值 年化和回撤 NAV Annualization and Drawdown

$$NAV_T=\prod_{t=1}^{T}(1+r_t),\qquad R_{ann}=NAV_T^{252/T}-1.$$

$$DD_t=\frac{NAV_t}{\max(1,NAV_1,\ldots,NAV_t)}-1,\qquad MDD=-\min_t DD_t.$$

**中文**：累计收益是期末净值减1，不是每期收益简单相加。年化依赖持有区间，所以跨不同年份不能只比较累计收益。回撤必须包含初始资金，否则第一天的亏损可能被漏掉。课程版按每日开盘估值，停牌期间沿用最近真实开盘标记，不等于持仓经济风险没有变化，日内风险也可能更大。

**English**: Compound, rather than add, period returns. Include initial capital in the running peak. Our daily-open drawdown omits intraday extrema; stale marks during suspensions can understate temporarily unobserved risk.

## 8 Sharpe和基准 Sharpe and the Benchmark

$$Sharpe=\sqrt{252}\frac{\bar r_d}{s(r_d)}.$$

**中文**：本项目将无风险收益设为0。Sharpe分子是平均日收益，不是CAGR，因此累计收益为负时Sharpe也可能小幅为正，两者不必同号；不能据此直接断言公式错误。平方根年化是常见约定，对收益自相关等情况需要额外谨慎。基准是中证500价格指数，不是股息再投资指数；相对收益并不能自动排除行业、规模或其他风险暴露。

**English**: We use zero risk-free return and arithmetic mean daily returns. Sharpe and compounded total return need not have the same sign. The conventional square-root annualization and price-index benchmark have limitations; outperformance is not proof of factor-neutral alpha.

## 9 Rank IC与十分组 Rank IC and Deciles

$$RankIC_t=\operatorname{corr}_{Spearman}(score_{i,t}, e_{i,t}).$$

**中文**：Rank IC看同一天评分顺序与未来相对收益顺序是否一致，不要求数值收益预测准确。`rank_ic_ir`在代码中是日期级IC均值除标准差，未额外年化，不能和Sharpe混用。十分组按分数从低到高分组，理想证据是总体方向一致，而不是要求所有组永远单调。它帮助判断Top 10收益是否来自广泛排序关系，还是少数尾部股票。

**English**: Rank IC evaluates ordering, not exact return forecasts. The saved IC IR is mean divided by standard deviation without an annualization multiplier. Deciles inspect the breadth and direction of the ranking relation; perfect monotonicity is not a necessary condition.

## 10 分散与集中 Diversification and Concentration

**中文**：Top 10集中组合可能放大个股事件和行业暴露。十只等权不意味着十个独立风险来源；高度相关股票仍可能同时下跌。本项目没有行业/市值中性化、容量优化或按预测风险配置，因此收益提升可能部分关联风格暴露。将这些控制加入模型是未来研究，不是当前已验证结论。

**English**: Equal-weighting ten correlated names does not create ten independent bets. Sector/style neutrality, capacity and optimized risk budgeting are not implemented. Some outcomes may reflect exposures rather than isolated stock-selection skill.

## 11 对冲不等于无风险 Hedging Is Not Risk Elimination

**中文**：课程附加图采用50%多头、50%空指数或尾部股票收益差，总名义敞口100%。不是100%多头再100%空头。指数对冲减少市场方向暴露的意图不代表精确beta中性，个股组合beta会变化。尾部股票融券可能不可得，保证金、借券、基差和展期未完整计算，所以诊断性对冲的回撤更小也不代表可直接交易。

**English**: A 50/50 spread has 100% gross exposure and is not automatically beta neutral. Borrow constraints, margin, financing, basis and rolling remain incomplete. Lower diagnostic drawdown is not proof of implementable risk-free profit.

## 12 经济理由不是因果证明 An Economic Story Is Not Causal Proof

**中文**：估值、质量、成长和交易拥挤度提供合理的经济假设；但同一个指标可能反映风险补偿、行为偏差、数据代理或偶然关系。置换重要性只能说明模型依赖了什么，不证明为什么市场回报发生。展示应说“与某种解释一致”，而非“已经证明因果机制”。

**English**: Economic hypotheses motivate features, but predictive importance does not identify a causal channel or distinguish risk compensation from mispricing. Say “consistent with an interpretation,” not “proves the mechanism.”

## 项目证据与补充阅读 Project Evidence and Further Reading

- [本项目严格定义 Project methods](METHODOLOGY.md)：字段、交易时点与指标实现。
- [已知局限 Known limitations](KNOWN_LIMITATIONS.md)：不可把简化账本当完整实盘。
- [实际汇总结果 Recorded evidence](../results/portfolio_metrics.csv)：按模型与策略筛选后比较。
- [Tushare官方字段说明 Official provider documentation](https://tushare.pro/document/2?doc_id=27)：理解价格、日期与数据来源；接口说明不能替代历史版本验证。
