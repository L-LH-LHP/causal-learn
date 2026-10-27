---
tags: []
parent: ""
collections:
    - 基础S0
$version: 3442
$libraryID: 1
$itemKey: JPJ2WM64

---
# DoWhy学习

## Dowhy简介

DoWhy是一个用于激发因果思考和分析的python库，其提供了诸如效应估计、预测、因果影响量化、因果结构学习、因果结构诊断、根本因果分析、因果干预和反事实等算法；

DoWhy的特点

*   反驳API，可用于测试任何评估算法的因果假设，增强推理的鲁棒性，可应用于非专家模型

DoWhy功能预览![\<img alt="功能" width="5774" height="2662" data-attachment-key="REL8WG4L" src="attachments/REL8WG4L.png" ztype="zimage"> | 5774](attachments/REL8WG4L.png)

*   采用反驳机制--对于因果分析的假设进行质疑或者否定 采用反驳机制的原因：预测机器学习的交叉验证方法无法使用，因为无法获得干预分布的样本

反驳机制可用在两个阶段：

1.  因果图建模后
2.  任务分析完成后

## DoWhy四步走

### Model

```
model = CausalModel(
    data=df,
    treatment='v0',
    outcome='y',
    graph='\n'.join(nx.generate_gml(causal_graph))
)
model.view_model()
```

### Identify

```
identified_estimand=model.identify_effect()
print(identified_estimand)
```

### Estimate

```
#•基于回归的方法
estimate=model.estimate_effect(identified_estimand,
        method_name="backdoor.linear_regression",
        test_significance=True
)
print(estimate)
#•基于倾向评分匹配的方法 该拟合算法后续用于匹配、分层或加权方法
#Ⅰ.Propensity-Based Matching
causal_estimate_match=model.estimate_effect(identified_estimand,
        method_name='backdoor.propensity_score_matching',
        target_units='atc',
)
print(causal_estimate_match)
print('causal estimate is'+str(causal_estimate_match.value))
#Ⅱ.Propensity-based Stratification
causal_estimate_strat = model.estimate_effect(identified_estimand,
                                             method_name="backdoor.propensity_score_stratification",
                                             target_units="att")
print(causal_estimate_strat)
print("Causal Estimate is " + str(causal_estimate_strat.value))
#Ⅲ.Inverse Propensity Weighting
causal_estimate_ipw = model.estimate_effect(identified_estimand,
                                           method_name="backdoor.propensity_score_weighting",
                                           target_units = "ate",
                                           method_params={"weighting_scheme":"ips_weight"})
print(causal_estimate_ipw)
print("Causal Estimate is " + str(causal_estimate_ipw.value))
```

### Refuter

```
#Ⅰ.Placebo Treatment refutation
res_placebo=model.refute_estimate(identified_estimand, estimate,
       method_name="placebo_treatment_refuter", show_progress_bar=True, placebo_type="permute")
print(res_placebo)
#Ⅱ.Random Common Cause Refuter
res_random=model.refute_estimate(identified_estimand, estimate, method_name="random_common_cause", show_progress_bar=True)
print(res_random)
#Ⅲ.Data Subsample Refuter
res_subset=model.refute_estimate(identified_estimand, estimate,
       method_name="data_subset_refuter", show_progress_bar=True, subset_fraction=0.9)
print(res_subset)
```
