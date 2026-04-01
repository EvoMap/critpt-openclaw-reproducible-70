# CritPt × OpenClaw：失败题补跑 / 合并正式评测 / Accuracy 与 Token 分析

> 数据截止：**2026-03-26**  
> **本次正式评测 run**：`merged_eval_20260326_091812`（由 `manual_gen_20260325_081355` 与 `rerun_failed_20260325_081355` 合并而成）  
> **全量生成基线**：`manual_gen_20260325_081355`  
> **失败题补跑 run**：`rerun_failed_20260325_081355`

---

## 1. 总体：这次评测链路实际发生了什么

| 阶段 | run_id | 结果 | 说明 |
|------|--------|------|------|
| 全量 generation | `manual_gen_20260325_081355` | `60 succeeded / 6 skipped / 4 failed` | 70 题全量跑完，但有 4 题最终失败 |
| 失败题单独补跑 | `rerun_failed_20260325_081355` | `4 succeeded / 0 failed` | 只重跑 `34 / 47 / 54 / 62` 四题，全部成功 |
| 合并后正式 evaluation | `merged_eval_20260326_091812` | **Accuracy = 27.14%** | 将 66 个已有 submission 与 4 个补跑成功 submission 合并成完整 70 题后提交官方评测 |

**一句话结论**：

- 这次不是直接拿 4 题去评测，而是先把补跑结果并回完整 70 题集合。
- 最终官方 batch 评测成功完成，准确率 **27.14%**，`server timeout = 0`。
- 从历史分数看，这次结果**追平当前历史最好成绩**，但**没有刷新新高**。

---

## 2. 分数结果：本次在历史里处于什么位置

来自本次 `aggregate_report.json`：

| 指标 | 数值 |
|------|------|
| **Total submissions** | `70` |
| **Accuracy** | **27.14%** |
| **Server timeout rate** | `0.00%` |
| **Server timeout count** | `0` |
| **Judge error count** | `0` |

结合现有 `analysis/scoring/scoring_report.md`：

| run_id | Accuracy | 备注 |
|--------|----------|------|
| `critpt_20260319_174644` | **27.14%** | 当前历史最佳（已有 scoring report 中记录） |
| `full_20260322_031628` | `25.71%` | 次优 |
| `merged_eval_20260326_091812` | **27.14%** | **追平最佳**，未超过 |

**解释**：

- 这次补跑 + 合并评测的结果，已经达到当前仓库里能看到的最高准确率水平。
- 但由于 CritPt 本地落盘的仍然是 **aggregate report**，没有逐题判对 JSON，因此**无法从本地文件直接证明这 4 个补跑题里哪些题真正把官方判分拉高了**。
- 可以确认的是：工程层面已经从“70 题里有 4 题失败”变成了“70 题 submission 全覆盖并完成正式评测”。

---

## 3. 四道失败题：从失败到补齐

原始全量 run `manual_gen_20260325_081355` 的 4 个失败题：

| Challenge | 原始 run 终态 | 原始失败特征 | 补跑 run 终态 |
|-----------|---------------|--------------|---------------|
| `34` | `failed` | 三轮都未完成，自检未到达（长题高复杂度超时链路） | `succeeded` |
| `47` | `failed` | 三轮都未完成，自检未到达（长题高复杂度超时链路） | `succeeded` |
| `54` | `failed` | `placeholder_ellipsis_detected` + timeout | `succeeded` |
| `62` | `failed` | `syntax_error: unmatched '}'` + `placeholder_ellipsis_detected` | `succeeded` |

补跑 run `rerun_failed_20260325_081355` 的结果：

- `4 / 4` 全部成功
- `fallback_count = 0`
- 说明这 4 题并不是“题目本身完全不可解”，而更像是**原始全量 run 下的局部超时/结构错误/占位输出问题**

---

## 4. 这 4 题补跑时用了什么基因

### 4.1 `Challenge_34_main`

- 主题：`hamiltonian_inverse_design` / `many_body_spin_chain` / `matrix_numerical_method`
- 补跑选中基因：
  - `gene_topic_hamiltonian_inverse_design`
  - `gene_topic_symbolic_derivation`
  - `gene_arxiv_ebe24f551e12`
- 结果：从原始 run 的三轮失败，变成补跑一次成功并生成完整 `def answer(a, b)`

### 4.2 `Challenge_47_main`

- 主题：`hamiltonian_inverse_design` / `many_body_spin_chain` / `matrix_numerical_method`
- 补跑选中基因：
  - `gene_topic_hamiltonian_inverse_design`
  - `gene_topic_symbolic_derivation`
  - `gene_arxiv_ebe24f551e12`
- 结果：原始 run 三轮都没走到自检；补跑时成功输出了完整数值谱方法代码

### 4.3 `Challenge_54_main`

- 主题：`general_quantum_reasoning`
- 补跑选中基因：
  - `gene_arxiv_e140337158b8`
  - `gene_arxiv_5aa4671d631f`
  - `gene_arxiv_d1aec630df87`
- 结果：原始 run 从占位输出 + timeout，转为补跑成功并通过自检

### 4.4 `Challenge_62_main`

- 主题：`general_quantum_reasoning`
- 补跑选中基因：
  - `gene_arxiv_5aa4671d631f`
  - `gene_arxiv_603f12bd6f87`
  - `gene_arxiv_c63dac094279`
- 结果：原始 run 是 `syntax_error` 与占位输出混合失败；补跑时首轮即成功并通过自检

**归纳**：

- `34` / `47` 这两题的成功更像是**高复杂度长题在单独补跑时获得了更稳定的完整推理窗口**。
- `54` / `62` 则更像是**结构性输出问题**被补跑清掉了。
- 从基因类型看，补跑成功并不意味着“新增了很多基因”，而更像是**已有基因库 + 更干净的单题执行环境**提高了落盘成功率。

---

## 5. 为什么不能直接对这 4 题做 evaluation

这次实际踩到了一个重要限制：

- 直接对 `rerun_failed_20260325_081355`（仅 4 题）执行 `run_grading.sh`
- 服务端返回：`400 Bad Request`

这说明当前 CritPt PUBLIC 评测接口在这个工作流下要求的是：

- **完整 hosted problem set**
- 而不是“只交 4 道题的局部 batch”

因此这次采用的正确做法是：

1. 保留原始全量 run 中已有的 `66` 个 submission
2. 用补跑成功的 `4` 个 submission 覆盖失败题
3. 合并为新的完整 run：`merged_eval_20260326_091812`
4. 对完整 `70` 题集合执行正式评测

---

## 6. Token 统计：这次补跑额外花了多少体量

### 6.1 统计口径

- 来源：各 run 的 `attempts/*_token_stats.json`
- 口径：仅统计**最终成功落盘那一轮**的 `prompt_tokens` 与 `response_tokens`
- **不包含**：
  - 失败 attempt 的完整上下文
  - 隐式 reasoning / hidden chain-of-thought
  - agent 内部多轮消息与工具往返

因此下表只适合比较**最终提交体量**，**不能**当作真实账单。

### 6.2 全量 run 与补跑 run

| run | token_stats 文件数 | Prompt tokens | Response tokens | Total |
|-----|--------------------|---------------|-----------------|-------|
| `manual_gen_20260325_081355` | `66` | `77,167` | `22,660` | `99,827` |
| `rerun_failed_20260325_081355` | `4` | `4,255` | `1,515` | `5,770` |
| **合并后唯一 70 题集合** | **70** | **81,422** | **24,175** | **105,597** |

字符数口径（同样只看最终成功落盘）：

| run | Prompt chars | Response chars |
|-----|--------------|----------------|
| `manual_gen_20260325_081355` | `257,615` | `73,814` |
| `rerun_failed_20260325_081355` | `15,538` | `5,306` |
| **合并后唯一 70 题集合** | **273,153** | **79,120** |

### 6.3 单看 4 题补跑成本

| Challenge | Prompt tokens | Response tokens | Total |
|-----------|---------------|-----------------|-------|
| `34` | `953` | `155` | `1,108` |
| `47` | `1,202` | `663` | `1,865` |
| `54` | `722` | `225` | `947` |
| `62` | `1,378` | `472` | `1,850` |

**观察**：

- 这次补跑额外带来的“最终提交 token 体量”大约是 **5,770 tokens**
- 占合并后 70 题最终提交总量 `105,597` 的约 **5.5%**
- 对应的收益是：
  - 4 个失败题全部补齐
  - 评测从“不完整不可正式提交”变成“可提交完整 70 题 batch”

从工程性价比看，这次补跑是**值得的**。

---

## 7. 这次结果该如何解读

### 7.1 可以确定的

- 原始 70 题 generation 里有 4 个失败洞
- 这 4 个失败题单独补跑后全部成功
- 合并后的完整 70 题正式评测成功完成
- 最终准确率 **27.14%**
- 且 `server timeout = 0`

### 7.2 不能过度解读的

- 不能从本地文件直接断言“4 个补跑题里哪几题官方判对了”
- 不能把这次 27.14% 简单解释成“因为多了 4 个 submission 所以分一定涨了”
- 也不能仅凭这次文档就得出“基因库规模变大导致更强”；当前更合理的解释仍是：
  - **失败题被补齐**
  - **完整 batch 得以正式提交**
  - **局部结构/超时问题在单题环境下被修复**

### 7.3 最实际的结论

- 这次流程最重要的收益，不只是最后分数，而是把工作流验证成了：
  - 全量生成
  - 失败题提取
  - 单独补跑
  - 合并完整 submission
  - 正式 evaluation

这个闭环以后可以复用。

---

## 8. 参考文件路径

| 内容 | 路径 |
|------|------|
| 本次正式评测汇总 | `benchmarks/critpt-openclaw/results/evaluations/merged_eval_20260326_091812/aggregate_report.json` |
| 合并 run 摘要 | `benchmarks/critpt-openclaw/results/generations/merged_eval_20260326_091812/run_summary.json` |
| 原始全量 generation 摘要 | `benchmarks/critpt-openclaw/results/generations/manual_gen_20260325_081355/run_summary.json` |
| 失败题补跑摘要 | `benchmarks/critpt-openclaw/results/generations/rerun_failed_20260325_081355/run_summary.json` |
| 历史评分对比 | `benchmarks/critpt-openclaw/analysis/scoring/scoring_report.md` |
| 四题 challenge 列表 | `benchmarks/critpt-openclaw/evolver/state/rerun_failed_manual_gen_20260325_081355.txt` |

---

*文档依据仓库内现有 generation / evaluation 产物生成；逐题官方判对明细仍需 CritPt 服务端提供更细粒度落盘。*
