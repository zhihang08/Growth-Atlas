# Growth-Atlas

本目录使用 GitHub Copilot 自定义 Agent 实现「新生儿养育总控规划」。

## 结构

- `.github/agents/growth-atlas.agent.md` — Growth-Atlas 总控 Agent，负责整体阶段规划、清单、花费预估，并调度子 Agent
- `.github/agents/claim-review-agent.agent.md` — 子 Agent：先归纳用户看到的观点，再拆分主张、分级证据和分派领域验证
- `.github/agents/family-policy-agent.agent.md` — 子 Agent：生育/医保/户籍政策与入园入学路径统一核验
- `.github/agents/nutrition-agent.agent.md` — 子 Agent：按年龄/身高/体型的饮食营养建议（0-12月逐月细化）
- `.github/agents/child-psychology-agent.agent.md` — 子 Agent：儿童心理发展理论与教养回应策略
- `.github/agents/education-resource-agent.agent.md` — 子 Agent：分阶段育儿书籍/课程/教育流派推荐与低成本资源规划
- `.github/agents/physical-development-agent.agent.md` — 子 Agent：分龄大运动、亲子锻炼、项目启蒙与运动安全规划
- `.github/agents/child-health-agent.agent.md` — 子 Agent：儿保、筛查、睡眠、伤害预防与就医红旗信号
- `profile/child-profile.md` — 孩子基本信息与生长记录（首次使用前建议先填写）
- `plans/INDEX.md` — 全部计划按当前执行、长期规划、运动专题、证书候选和证据状态分类的统一入口
- `plans/age-by-age-execution-plan-0-18.md` — 0-18岁逐月/半年/逐年龄综合执行计划（日常主入口）
- `plans/overview.md` — 各阶段计划总览表
- `plans/review-state.md` — 自检基线、变更影响和待官方/专业机构核验事项
- `.github/evidence-validation-protocol.md` — 全体 Agent 共用的观点摄入、证据分级和结论协议
- `references/CHS-Guide.docx` / `CHS-Guide-summary.md` — 国家卫生健康委0-3岁健康养育照护官方基线及项目摘要
- `references/CDC-child-development-summary.md` — 美国CDC 0-5岁积极教养、发展观察与家庭安全国际补充基线

具体阶段报告、长期参考、运动专题和观点审查文件不在此平铺，统一从 `plans/INDEX.md` 进入。

## 理论与调度架构

总控以“发展生态系统理论 + 生命历程/阶段发展任务 + 全人发展九维度”为骨架：孩子的发展同时受健康、家庭照护、学校、同伴、社区和公共政策影响，不能只用成绩、身高或课程数量衡量。

| 任务 | 调用 Agent | 理论/制度基线 |
|---|---|---|
| 外部观点、短视频、清单或效果主张初审 | `claim-review-agent` | 主张拆解、证据层级、适用性与风险评估 |
| 跨维度阶段复盘与优先级 | `growth-atlas` 总控 | 生态系统理论、全人发展、最近发展区 |
| 儿保、筛查、症状分流与伤害预防 | `child-health-agent` | 国家儿童健康管理、免疫规划、循证儿科风险分层 |
| 营养、生长与喂养 | `nutrition-agent` | WHO、中国膳食指南、儿童健康管理、生长曲线 |
| 大运动、体能、运动习惯 | `physical-development-agent` | WHO/中国身体活动指南、基本动作技能、LTAD |
| 情绪、依恋、行为与教养 | `child-psychology-agent` | 依恋、Piaget、Erikson、Vygotsky、执行功能 |
| 书籍、课程与家庭教育投入 | `education-resource-agent` | 发展适宜性教育、游戏化学习、自我决定理论、双减精神 |
| 生育、医保、户籍、托育与升学路径 | `family-policy-agent` | 国家制度 → 北京市文件 → 实际办理区/入学区执行口径 |

### 统一证据规则

1. 法律政策：优先使用国家 → 北京市 → 西城区主管部门的官方文件；当年政策、价格和招生细则必须披露是否已实时核验。
2. 健康与发展：优先使用权威指南、儿童保健标准和长期趋势；中位数、百分位、理论年龄范围均不是“达标线”。
3. 家庭建议：明确是根据本家庭信息做出的建议，不把估算、推断或教育流派包装成事实。
4. 医学、心理、发育异常：不由 Agent 诊断；明确建议儿保、儿科、发展行为儿科、心理专业人员等线下评估路径。

### 用户观点验证流程

当用户输入“我看到一个观点/方法/清单”时，不会直接抄入计划，而是执行：

1. 忠实归纳用户真正想表达的观点，指出错字、编号、歧义和缺失条件。
2. 拆成描述、因果、数字、规范、政策和安全等可验证主张。
3. 先由 `claim-review-agent` 初筛，再交给对应领域 Agent 二次验证。
4. 按 A强/B中/C弱或间接/D未核验/X不建议分级。
5. 每条结论标记为“采纳/有限试行/以后留档/待补材料/不采纳”，并说明年龄、中国/北京适用性、风险与复盘时间。
6. 只有“采纳/有限试行”进入当前计划；未核验内容保留在候选报告，不作为必须执行事项。

为了提高核验质量，最好同时提供原始链接/截图、作者或机构、发布时间、完整原话、收费和你希望解决的问题。缺少这些材料仍可做初审，但不会伪造来源或精确效果。

### 北京本地化边界

已知孩子现居朝阳、拟落户西城，因此幼儿园按朝阳实际居住地规划，小学起按西城区户籍/房产路径规划；公共服务机构则需结合实际居住和参保地判断。当前 Agent 环境没有独立实时互联网搜索能力；除非用户提供可访问的官方 URL 或已验证材料，Agent 会给出主管部门和核验问题清单，而不会声称已查到最新北京政策、学区、机构名额或报价。

## 自检与更新

Agent 不能在后台自动监听文件或政策变化，但每次被调用时都会读取 `profile/child-profile.md` 和 `plans/review-state.md`，识别变化并只更新受影响的计划。修改档案、获得官方政策/招生材料、或孩子出生后录入实际数据后，在对话中直接说“运行育儿规划自检”即可。

## 使用方式

在 Copilot Chat 中选择 **Growth Atlas** Agent，直接说明需求，例如：

> 帮我规划一下宝宝从出生到上幼儿园的养育计划

Agent 会先确认/补全孩子档案信息，然后按阶段生成需要做的事情、需要买的物品清单和预估花费，并在涉及当地政策、入学政策、营养建议时自动调用对应子 Agent。
