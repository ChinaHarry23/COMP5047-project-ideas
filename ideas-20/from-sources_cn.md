---
course: COMP4447 / COMP5047
title: "紧急议题 — 来自在线来源，不是记忆（中文）"
updated: 2026-09-12
lang: zh-CN
aliases:
  - 从来源出发 中文
---

# 先读官方记录，再设计

> **English**：[[from-sources_en]]
> 方法：2026-09-12 现场检索。挑战来自 DCCEEW、EPBC 关键威胁过程、受威胁物种行动计划、TERN/ANU 2025 报告、CSIRO/气象局《气候状况 2024》、以及 2025–26 新闻。**不是**「怎样做 MCU 演示好看」。
> 此前 20 条构想把真实议题和实验室里编出来的混在一起（灌丛火鸡孵丘、噪钟矿鸟挡板、把玻璃撞鸟当成澳大利亚的核心危机）。除非来源把它放进国家紧急清单，否则拿掉。

**本课的硬边界。** 13 周三块 ESP32 挡不住气候变化、H5N1 或 Appin Road。它**可以**把一条有文献的压力，用被点名的非人类的语言做成校园模型，并写清它**不是**什么。

---

## 1. 记录里什么才叫紧急

### 国家级压力（不是物种名单）

| 压力 | 来源实际在说什么 | 出处 |
|---|---|---|
| 入侵种 | 影响 **82%** 受威胁物种。21 项 EPBC 关键威胁过程里 13 项是入侵。 | [SoE](https://soe.dcceew.gov.au/biodiversity/pressures/threats-and-key-threatening-processes) |
| 栖息地丧失/改造 | 约 **70%** 受威胁类群的主要机制；农业、城市化、交通。2024 年再次点名东海岸开发。 | SoE；[TERN 2024](https://www.tern.org.au/wp-content/uploads/2024_Aus-Env-Report_FINAL-1.pdf) |
| 火况 + 气候极端 | 火况影响约 **74%** / 约三分之二 EPBC 物种。气候现已是 **每 10 个新列入物种里 9 个** 的主要威胁。 | SoE；[TERN 2025（2026-03 发布）](https://www.tern.org.au/news/australias-environment-report-2025/) |
| 种群崩溃 | 受威胁鸟兽植物蛙爬 2000 年以来平均降 **59%**；蛙 **67%**，爬 **88%**。2025 年又列入 39 种，总数 **2175**。 | TERN 2025 |
| 海洋热浪 | 更长的海洋热浪；大堡礁/宁格罗白化；海带/海草；2024/25 海洋热浪 + 南澳 *Karenia* 藻华。 | [CSIRO/BoM](https://www.csiro.au/en/news/All/Articles/2024/November/State-of-the-Climate-2024)；[SA 藻华](https://www.algalbloom.sa.gov.au/) |

**不必自己发明问题的 EPBC 关键威胁过程**（全表 [SPRAT](https://environment.gov.au/cgi-bin/sprat/public/publicgetkeythreats.pl)）：

- **野猫**捕食（**2024** 年威胁减缓计划，与 NSW 等州联合制定）
- **赤狐**捕食
- 两栖类 **壶菌**
- **疫霉** *Phytophthora cinnamomi* 引起的枯死
- **新生物区系**（含 **桃金娘锈病**）
- 蔗蟾、野猪、火蚁、海洋垃圾等

**联邦 2022–2032 行动计划**（[110 优先种](https://www.tern.org.au/threatened-species-guidelines/)）：2026 年目标含猫/狐管理、锈病保险种群、气候韧性。**与悉尼相关、在 110 名里的：** 考拉（昆/新/首）、橙腹鹦鹉、摄政吸蜜鸟、大杓鹬、风头鸻（东部亚种）、怀特海马、瓦勒迈杉、眼镜狐蝠（昆士兰；灰头狐蝠是 EPBC 易危但 **不在** 110 里 — 仍用 EPBC，不要冒充 110）。

### 2025–26 正在发生的（这才叫「现在就急」）

| 事件 | 非人类 | 为什么不是工作室虚构 |
|---|---|---|
| **2026-01-10** 东海岸热浪 | 灰头/黑狐蝠 | [ABC 1月13日](https://www.abc.net.au/news/2026-01-13/hundreds-of-flying-foxes-die-in-east-coast-heatwave/106223198)：伊拉瓦拉两处营地 700 只；Windsor、Parramatta、Campbelltown、Wolli Creek 也有死亡。[卫报](https://www.theguardian.com/environment/2026/jan/12/flying-foxes-die-in-their-thousands-in-worst-mass-mortality-event-since-australias-black-summer)：自黑夏以来最严重；Welbergen：≥42 °C。Wolli：NPWS **封闭步道**。喷淋研究存在；WIRES 要求营地喷淋。 |
| **2026-08** H5N1 野生动物风险 | 约 400 种鸟兽高风险或更糟 | [卫报 8月5日](https://www.theguardian.com/world/2026/aug/05/penguins-dolphins-nearly-400-australian-species-threat-bird-flu-true-extent-disaster)。**不要做「流感小电器」。** |
| **2025-03 起** 南澳 *Karenia* 藻华 | 鱼、贝、约 550 类群 | 海洋热浪 + 洪水养分 + 上升流。不是 Camperdown 能做的。当气候-海洋引用，不要假装做海湾。 |
| **2026-02** 悉尼港海草 | *Posidonia australis*（NSW 六个河口濒危） | [ABC 2月13日](https://www.abc.net.au/news/2026-02-13/science-key-to-saving-endangered-seagrass-population/106337120)：锚链、船、污染；SIMS 在 Balmoral 做漂浮系泊 + 实验室苗。 |
| **2025-12 至 2026-09** 西南悉尼考拉 | 考拉 = **110 优先种** | [ABC 12月10日](https://www.abc.net.au/news/2025-12-10/koala-corridors-under-threat-in-sydney-conservationists-warn/106099774) 廊道；[ABC 9月3日](https://www.abc.net.au/news/2026-09-03/koalas-avoid-sydney-appin-road-underpass/107090362)：**1200 万澳元涵洞五个月零记录**；SBKN：2022-07 以来 Appin Rd 66 死。 |
| **2026-09-07** Concord 区划 | 橙腹鹦鹉 = **110**，联邦极危 | [ABC 9月7日](https://www.abc.net.au/news/2026-09-07/locals-call-for-rezoning-change-swift-parrot-concord/107111428)：栖息地 + 国家恢复计划里的 **噪声和光**；濒危 STIF 林 + 雨水。 |
| 猫（持续 + TAP 2024） | 200+ 受威胁种 | [DCCEEW](https://www.dcceew.gov.au/environment/invasive-species/feral-animals-australia/feral-cats)：每年 **15 亿** 本土脊椎动物。NSW 议会仍未给强制禁足（[ABC 2025-09-07](https://www.abc.net.au/news/2025-09-07/nsw-councils-call-for-mandatory-cat-containment/105721582)）；ISC：NSW 游走猫约 **9600 万**/年。 |

---

## 2. 我们不再假装这些是「国家紧急」

| 诱人的 MCU 点子 | 为什么在这份记录上排不上 |
|---|---|
| 灌丛火鸡孵丘窑 | 真实的郊区冲突；**不在** SoE/TAP/KTP。ACI 好看，Distinction 文献弱。 |
| 噪钟矿鸟致密叶片 | 真实城市鸟类生态；**不是** 2025–26 国家紧急。 |
| 玻璃撞鸟遮帘 | 全球城市问题；校园真实，但不是 listed KTP。 |
| 无刺蜂门 / 水龙石 / 针鼹停割桩 | 从实验室往外编的。 |
| H5N1 检测器 / 大堡礁白化灯 / 火况盒子 | 紧急，**且** 做成第 13 周玩具是不诚实。 |
| 往真实狐蝠营地装喷雾 | 一月的死亡是真的；本学期往营地施工是伦理/NPWS，不是 tutorial。 |

---

## 3. 从这些来源推出来的装置

每一行：**先引议题**。交互设计其次。架构是校园模型。Vision Pro = 探询叠加，永远不是 Pass 产品。

### A. 猫 — KTP + TAP 2024 + NSW 游走之争

**挑战。** 全国每年猫杀死超过 15 亿本土动物；游走宠物猫在 NSW 再加数千万；NSW 仍无法强制禁足。

**非人类。** TAP 猎物名单里的袋狸、小型哺乳、爬、蛙 — 不是「我们喜欢的负鼠」。

**交互。** 黄昏后对猫体型失效关闭；分类器挂了对野生动物体型 **失效开放**。几何大于 AI。不用超声波。不做真猫实验。

**架构。** ESP32-S3-CAM + BH1750 + LD2410 → 演示篱笆上的 MG90S 缝。图像 API = Credit。演示：猫玩偶 vs 袋狸玩偶。

---

### B. 狐蝠热浪 — 2026 年 1 月死亡 + NSW 热应激科学

**挑战。** 气温约 ≥42 °C 会热死悉尼营地的灰头狐蝠；2026 年 1 月是黑夏以来最严重集群死亡；幼体先死。

**非人类。** *Pteropus poliocephalus*（EPBC 易危）。先是幼蝠。

**交互。** BoM **前馈**，不要等喘气。默认关。Wolli Creek 已经用 **封路** 减压力 — 预报热浪时对狗/人关门，比观光喷雾更接近文献。

**两个诚实做法（选一）：**

1. **热箱叶子 + 喷雾模型**（喷淋论文；绝不进真营地）。
2. **热日营地边缘步道闩**（NPWS 2026-01-10 已封路）：lux + BoM + 毫米波 → 演示小径上的实体门闩。

**AVP。** 热体积 /「谁先死（幼蝠）」。头显不得开门闩。

---

### C. 考拉道路屏障 — 110 优先种 + 2026 Appin Rd

**挑战。** 西南悉尼考拉廊道被住房和道路切断；1200 万澳元涵洞五个月 **零** 考拉记录，路杀仍在发生。

**非人类。** *Phascolarctos cinereus*（行动计划优先种）。

**交互。** **不要**宣称修好了 Appin Road。文献里的失败是：动物不进 52 米黑盒子。做 TfNSW 已列的 **涵洞家具**（单向活门、掩护、湿度）：考拉体型能过、狗不能过；光/湿不是眩光陷阱（DCCEEW 光指南：哺乳动物）。

**架构。** ESP32-S3、BH1750、湿度、LD2410、MG90S 单向活门、约 1.5 m 木/PETG 隧。演示：考拉玩偶 vs 狗 vs 人。

---

### D. 橙腹鹦鹉 + STIF 残林 — 110 优先种 + 2026-09-07 Concord

**挑战。** 橙腹鹦鹉极危；本月内西区划之争围绕冬季栖息地、**噪声和光**、以及雨水进入濒危 STIF 林。

**非人类。** *Lathamus discolor*；林分作为共同利益相关者。

**交互。** 安静：开花季黄昏「工地灯」模型 **默认关**（BoM + lux）。可选麦 + BirdNET。雨水托盘作第二执行器。外壳不是旧题路径灯。

---

### E. 海神草 / 锚链刮底 — NSW 濒危海草，2026-02 SIMS

**挑战。** *Posidonia australis* 在 NSW 六个列入河口濒危；港湾草甸被摆动锚链、船、施工、污染撕掉；自然恢复不被预期。

**非人类。** 海草甸（也是 110 里怀特海马的栖息）。

**交互。** 缸里两种系泊：刮底的链 vs 不刮的漂浮缆。浊度 + 向下光照决定舵机 **抬起链的模型**。你在排练 SIMS 的环保系泊 vs 传统锚。

**把 J 怀特海马折进 E，不要拆成两个产品。**

---

### F. 疫霉 / 壶菌洗靴闸 — 两项 KTP + 110 里的瓦勒迈杉

**挑战。** 步行者传播疫霉（KTP；悉尼砂岩、皇家国家公园）和壶菌（KTP）。联邦 *Arrive Clean, Leave Clean* 才是干预，不是「智能池塘」。

**交互。** 人是媒介。洗靴水浊度/电导说靴子冲过了，路才开。

---

### G. 桃金娘锈病工具锁 — 行动计划 2026 目标 + 新生物区系 KTP

**挑战。** 锈病在新生物区系 KTP 下；行动计划要在 2026 年前把受影响的列入桃金娘科送进保险种群。悉尼已在疫区；脏工具/植株会扩散。

**非人类。** 110 里的土番石榴 *Rhodomyrtus psidioides* 已被锈病重创 — 点名它，演示用常见红千层/澳洲蒲桃。

---

### H. Cooks / Wolli 初段径流 — 栖息地退化机制 + 活着的流域政治

**挑战。** SoE：多数威胁落地的方式是栖息地退化。2025 年 Cooks 沿河议会仍把 **雨水** 当作主要持续污染源；EPA 罚了 2024 年排入 Cooks 的未处理水。

**非人类。** 大型底栖 / 残存鱼类（报告：耐污类群）。该河段若引不出列入鳗鲡，就不要编。

---

### I. 营地边缘光 / 干扰 — DCCEEW 狐蝠营地指南 + 2023 光污染指南（蝙蝠附录）

**挑战。** 国家指南：不要照国家重要狐蝠营地；热浪中再干扰更糟。（**不是** 旧路径灯护柱。）

**不要**重做 Wildlife-First Adaptive Path Light。

---

## 4. 二十四小时（按来源选）

| 组里若要 | 选 | 记录为什么撑得住 |
|---|---|---|
| 最强的 2026 时间戳 | **B** 狐蝠热（门闩或模型喷雾） | 一月死亡，ABC/卫报/Wolli，≥42 °C |
| 最强法律 KTP | **A** 猫 | TAP 2024，每年 15 亿，NSW 立法仍在吵 |
| 最强 110 种 + 本月新闻 | **D** 橙腹鹦鹉 / STIF | Concord 2026-09-07 + 恢复计划里的光/噪声 |
| 最强港湾植物 | **E**（含海马） | 海神草濒危；SIMS 2026 |
| 最强 Country / 植物病原 | **F** 或 **G** | 两项 KTP + Arrive Clean Leave Clean |
| 最强「为什么 1200 万涵洞没用」 | **C** 考拉活门模型 | ABC 2026-09-03 空涵洞 |

**Proposal 里 Distinction 会信的一句：** 「我们没有发明一个挑战。我们取了〔KTP / 110 优先种 / 2026 年 1 月事件〕，点名非人类，并做成威胁减缓或恢复文献里已有干预的校园模型。」
