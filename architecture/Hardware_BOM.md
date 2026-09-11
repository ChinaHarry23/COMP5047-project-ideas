# Hardware BOM — Wildlife-First Adaptive Path Light

Order list for the COMP5047 / COMP4447 bollard. Matches `Hardware_stack.md`: three sensor types, two non-screen actuators, one ESP32-S3, **analog frequency-division / envelope into ADC** — not a 192 kHz I2S FFT as the Week 13 path.

Prices are **typical Taobao listing ranges in CNY as of late August 2026**. They move. Item pages rot; **official shops and search URLs are the durable links**. From Australia use Taobao / Cainiao global checkout, or a forwarder (Superbuy, CNFans). Allow **10–21 days**. Buy **two of every demo-critical module** — a dead LD2410 the week before the demo is worse than ¥15 extra.

| Priority | Meaning |
|---|---|
| **MUST** | Order this week. Demo fails without it. |
| **SHOULD** | Enclosure, power, or bring-up. Order with the same parcel. |
| **OPTIONAL** | Alternate or spare. |
| **STRETCH** | After the demo path works. |
| **AVOID** | Conflicts with the proposal or wastes the week. |

Currency rough convert: **¥4.7 ≈ AUD 1**. Minimum cart (MUST only, qty as listed) ≈ **¥280–420 + postage**. Recommended cart (MUST + SHOULD, two of critical sensors) ≈ **¥480–750 + postage**.

---

## How to buy from Australia

1. Open the **search** link. Prefer **官方 / 旗舰 / 企业店** (Espressif, Hi-Link, Seeed).
2. Pick the **SKU in the notes** (e.g. LD2410C 套件, MG90S **180° metal gear**, not 360° continuous).
3. If a search link 404s, paste the **Chinese keywords** into the Taobao app.
4. USB-C cables must be **data** cables, not charge-only.

Official shops (bookmark these):

| Shop | Link |
|---|---|
| Espressif 乐鑫官方 | [espressif.taobao.com](http://espressif.taobao.com/) · [ESP32-S3 开发板分类](http://espressif.taobao.com/category-1608826765.htm) · [世界站店铺](https://world.taobao.com/dianpu/113593007.htm) |
| Hi-Link 海凌科 | [官方产品页 + 购买入口](https://hlktech.com/cn/Goods-239.html) · [hilink旗舰店](https://www.taobao.com/list/dianpu/423428990.htm) |
| Seeed 矽递 | [shop123145485.taobao.com](https://shop123145485.taobao.com/) |

---

## Cart A — order this week (MUST)

| # | Part | Qty | Spec to click | Why | Est. ¥ | Taobao |
|---|---|---|---|---|---|---|
| A1 | **ESP32-S3 DevKit** | 2 | **ESP32-S3-DevKitC-1 N8R8 or N16R8**, USB-C, pins **pre-soldered**. Official ~¥99; clone N16R8 ~¥25–45. | MCU: I2C, UART, ADC, LEDC PWM. N8R8 is enough; N16R8 if you later try TinyML. | 50–200 | [乐鑫官方分类](http://espressif.taobao.com/category-1608826765.htm) · [搜索 ESP32-S3-DevKitC-1](https://s.taobao.com/search?q=ESP32-S3-DevKitC-1%20N16R8) |
| A2 | **USB-C data cable** | 2 | USB-C to USB-C or C-to-A, **data**, ≥1 m | Flash + serial. Charge-only cables look identical and waste a night. | 10–20 | [搜索 Type-C 数据线 充电+数据](https://s.taobao.com/search?q=Type-C%20%E6%95%B0%E6%8D%AE%E7%BA%BF%20%E5%85%85%E7%94%B5%2B%E6%95%B0%E6%8D%AE) |
| A3 | **BH1750 lux** | 2 | **GY-302 BH1750FVI**, 3.3–5 V, I2C, ADDR pad | Night / dawn / spill. ADDR to GND = 0x23. | 6–16 | [搜索 GY-302 BH1750](https://s.taobao.com/search?q=GY-302%20BH1750) |
| A4 | **LD2410C mmWave** | 2 | **HLK-LD2410C** (not D-only, not 2401). Prefer **套件A** (module + 5-pin Dupont) or **套件B** (+ USB-TTL). 5 V supply, **UART 3.3 V**. | Walker presence, range-gate ~5 m. Official: 0.75–6 m, ~79 mA. | 16–40 | [海凌科产品页](https://hlktech.com/cn/Goods-239.html) · [搜索 LD2410C 海凌科](https://s.taobao.com/search?q=LD2410C%20%E6%B5%B7%E5%87%8C%E7%A7%91) |
| A5 | **MAX4466 or MAX9814 mic** | 2 | Analog electret + amp, **3.3 V**, OUT to ADC. MAX9814 has AGC (easier). | **10–15 kHz** gate (*Austronomus australis* is often audible). Phone playback works here. | 6–16 | [搜索 MAX9814 麦克风模块](https://s.taobao.com/search?q=MAX9814%20%E9%BA%A6%E5%85%8B%E9%A3%8E%20%E6%A8%A1%E5%9D%97) · [MAX4466](https://s.taobao.com/search?q=MAX4466%20%E9%BA%A6%E5%85%8B%E9%A3%8E%E6%A8%A1%E5%9D%97) |
| A6 | **40 kHz ultrasonic receiver** | 2 | **TCT40-16R** / 16 mm **接收** (R, not T). Open or metal can. | **~25–40 kHz** proxy band (*C. gouldii* ~25–34 kHz + 40 kHz playback). Peaked at 40 kHz — good enough for a recorded stimulus. | 4–12 | [搜索 TCT40-16R 超声波接收](https://s.taobao.com/search?q=TCT40-16R%20%E8%B6%85%E5%A3%B0%E6%B3%A2%E6%8E%A5%E6%94%B6) · [16mm 40kHz 接收探头](https://s.taobao.com/search?q=16mm%2040kHz%20%E8%B6%85%E5%A3%B0%E6%B3%A2%E6%8E%A5%E6%94%B6%E6%8E%A2%E5%A4%B4) |
| A7 | **40 kHz ultrasonic transmitter** | 2 | **TCT40-16T** / 16 mm **发射**. Buy a **收发一对** if cheaper. | Assessor playback into the receiver. Not a claim of a captured bat. | 4–12 | [搜索 TCT40-16T 超声波发射](https://s.taobao.com/search?q=TCT40-16T%20%E8%B6%85%E5%A3%B0%E6%B3%A2%E5%8F%91%E5%B0%84) · [40kHz 收发一体一对](https://s.taobao.com/search?q=40kHz%20%E8%B6%85%E5%A3%B0%E6%B3%A2%E6%8E%A2%E5%A4%B4%20%E4%B8%80%E5%AF%B9) |
| A8 | **LM358** | 3 | DIP-8 **or** small dual-opamp module, single-supply 5 V / 3.3 V | Amp + envelope / comparator for the 40 kHz head. Frequency-division can be a Schmitt + 4040 later; envelope into ADC is enough for the demo gate. | 3–10 | [搜索 LM358 DIP](https://s.taobao.com/search?q=LM358%20DIP-8) · [LM358 模块](https://s.taobao.com/search?q=LM358%20%E8%BF%90%E6%94%BE%E6%A8%A1%E5%9D%97) |
| A9 | **2200 K or amber path LED** | 3 | **1 W or 3 W 仿流明 + 20 mm 铝基板**, SKU **2200K / 2000K / 金黄 / 琥珀**. Not 6500 K. | Downward path light. ~200 lm class if 3 W; 1 W is safer on USB. | 6–20 | [搜索 1W 3W 灯珠 2200K 铝基板](https://s.taobao.com/search?q=1W%203W%20%E7%81%AF%E7%8F%A0%202200K%20%E9%93%9D%E5%9F%BA%E6%9D%BF) · [琥珀光 金黄光 灯珠](https://s.taobao.com/search?q=%E7%90%A5%E7%8F%80%E5%85%89%20%E9%87%91%E9%BB%84%E5%85%89%201W%20%E7%81%AF%E7%8F%A0) |
| A10 | **Logic N-MOSFET** | 10 | **AO3400** SOT-23 **or** pre-made PWM MOSFET module. Gate works at **3.3 V**. | LED PWM. AO3400 RDS(on) is fine at 2.5–4.5 Vgs. | 3–12 | [搜索 AO3400](https://s.taobao.com/search?q=AO3400%20MOSFET) · [MOS管模块 PWM 开关](https://s.taobao.com/search?q=MOS%E7%AE%A1%E6%A8%A1%E5%9D%97%20PWM%203.3V) |
| A11 | **Amber 5 mm LED** | 20 | 5 mm **amber / 橙黄**, 2.0–2.2 V | Underside maintainer pulse only. Must not light the habitat. | 2–6 | [搜索 5mm 橙黄 LED](https://s.taobao.com/search?q=5mm%20LED%20%E6%A9%99%E9%BB%84%20%E7%81%AF%E7%8F%A0) |
| A12 | **MG90S servo** | 2 | **金属齿**, **90–180°** (not 360° continuous). Tower Pro / 辉盛 clones OK. | 180° printed blade. SG90 plastic gears fail in wind. | 16–30 | [搜索 MG90S 金属齿轮 180度](https://s.taobao.com/search?q=MG90S%20%E9%87%91%E5%B1%9E%E9%BD%BF%E8%BD%AE%20180%E5%BA%A6) |
| A13 | **Breadboard + Dupont** | 1 set | 830-point board + M-M / M-F / F-F 20 cm | Bring-up before soldering. | 12–25 | [搜索 830 面包板 杜邦线 套装](https://s.taobao.com/search?q=830%20%E9%9D%A2%E5%8C%85%E6%9D%BF%20%E6%9D%9C%E9%82%A6%E7%BA%BF%20%E5%A5%97%E8%A3%85) |
| A14 | **Resistor kit ¼ W** | 1 | 1 Ω–1 MΩ, include **5.6 Ω / 10 Ω** (LED), **220 Ω**, **1 k**, **10 k** | LED ballast, MOSFET gate, LED current-limit, I2C pull-ups if a module is missing them. | 8–15 | [搜索 电阻包 1/4W 常用](https://s.taobao.com/search?q=%E7%94%B5%E9%98%BB%E5%8C%85%201%2F4W) |
| A15 | **Capacitor kit** | 1 | Ceramic **100 nF**, electrolytic **10 µF / 100 µF** 16 V | 100 nF + 10 µF on the 3.3 V rail next to the MCU (see pin-map note). | 6–12 | [搜索 电容包 104 10uF 100uF](https://s.taobao.com/search?q=%E7%94%B5%E5%AE%B9%E5%8C%85%20104%2010uF%20100uF) |
| A16 | **1N4148 + 1N5819** | 1 pack each | Signal diode + Schottky | Envelope detector; flyback / LED reverse; cheap insurance. | 3–8 | [搜索 1N4148 1N5819](https://s.taobao.com/search?q=1N4148%201N5819) |
| A17 | **PETG filament 1.75 mm** | 1 kg | **Black PETG** (outdoor-ish, not brittle PLA). Brand: eSUN / 三绿 / 创想. | Fabricated enclosure (rubric). PLA is indoor-only. | 50–85 | [搜索 PETG 1.75 黑色 1kg](https://s.taobao.com/search?q=PETG%201.75%20%E9%BB%91%E8%89%B2%201kg) |

**Do not skip A5+A6.** There is no cheap “蝙蝠分频探测器成品” that ships in days and dumps a 0–3.3 V envelope. Dual analog heads + LM358 **is** the analog FD / envelope path in the proposal.

---

## Cart B — same parcel (SHOULD)

| # | Part | Qty | Spec | Why | Est. ¥ | Taobao |
|---|---|---|---|---|---|---|
| B1 | USB-TTL **CH340 / CP2102** | 1 | 3.3 V / 5 V jumper | Configure LD2410 with Hi-Link PC tool; spare UART debug. Often already in LD2410 **套件B**. | 6–15 | [搜索 CH340 USB转TTL](https://s.taobao.com/search?q=CH340%20USB%E8%BD%ACTTL) |
| B2 | **25 kHz ultrasonic receiver** | 1–2 | 16 mm **25 kHz 接收** if listed | Closer to *C. gouldii* 25–34 kHz than a 40 kHz head. Skip if not in stock. | 5–15 | [搜索 25kHz 超声波接收](https://s.taobao.com/search?q=25kHz%20%E8%B6%85%E5%A3%B0%E6%B3%A2%E6%8E%A5%E6%94%B6%E6%8E%A2%E5%A4%B4) |
| B3 | **CD4040** DIP | 5 | 12-stage binary counter | True frequency-division (÷16 / ÷32) if you want audible ticks. Envelope-only also passes the demo. | 3–8 | [搜索 CD4040 DIP](https://s.taobao.com/search?q=CD4040%20DIP) |
| B4 | **1 W 350 mA LED driver** | 2 | Mini buck **恒流** 350 mA, or 3 W 700 mA if you bought 3 W LEDs | Cleaner than a power resistor. PWM the driver’s enable if it has one; otherwise PWM the MOSFET **before** a resistor ballast. | 6–16 | [搜索 LED恒流驱动 350mA](https://s.taobao.com/search?q=LED%E6%81%92%E6%B5%81%E9%A9%B1%E5%8A%A8%20350mA) |
| B5 | **Small LED star heatsink** | 2 | 20 mm star compatible | 3 W on PETG will cook the print. | 4–10 | [搜索 20mm 铝基板 散热片](https://s.taobao.com/search?q=20mm%20%E9%93%9D%E5%9F%BA%E6%9D%BF%20%E6%95%A3%E7%83%AD%E7%89%87) |
| B6 | **5 V 2 A USB adaptor** | 1 | USB-A or USB-C PD 5 V | Bench supply. Servo + LED + mmWave together exceed a weak laptop port. | 15–35 | [搜索 5V2A USB充电器](https://s.taobao.com/search?q=5V2A%20USB%E5%85%85%E7%94%B5%E5%99%A8) |
| B7 | **18650 cell (protected)** | 2 | **带保护板**, name-brand (Panasonic / 三星 / 亿纬). Not a no-name 9900 mAh. | Night demo off USB. | 20–50 | [搜索 18650 保护板 原装](https://s.taobao.com/search?q=18650%20%E4%BF%9D%E6%8A%A4%E6%9D%BF%20%E5%8E%9F%E8%A3%85) |
| B8 | **18650 holder** | 2 | 1-cell, with leads | | 3–8 | [搜索 18650 电池盒](https://s.taobao.com/search?q=18650%20%E7%94%B5%E6%B1%A0%E7%9B%92%201%E8%8A%82) |
| B9 | **TP4056 + protection** | 2 | Micro-USB or USB-C, **带保护** (DW01). | Charge the 18650. Do not charge an unprotected cell on a bare TP4056. | 4–10 | [搜索 TP4056 带保护 USB-C](https://s.taobao.com/search?q=TP4056%20%E5%B8%A6%E4%BF%9D%E6%8A%A4%20Type-C) |
| B10 | **Buck 5 V** | 2 | **MP1584 / Mini360 / XL4015**, set to **5.0 V** before connecting the servo. | 18650 (3.0–4.2 V) → 5 V for servo + LD2410. | 6–16 | [搜索 MP1584 降压模块](https://s.taobao.com/search?q=MP1584%20%E9%99%8D%E5%8E%8B%E6%A8%A1%E5%9D%97) · [Mini360](https://s.taobao.com/search?q=Mini360%20%E9%99%8D%E5%8E%8B) |
| B11 | **Perfboard + headers** | 1 set | 洞洞板 5×7 / 7×9 cm + 2.54 排针 单排 | Soldered harness after breadboard. | 8–18 | [搜索 洞洞板 排针 2.54](https://s.taobao.com/search?q=%E6%B4%9E%E6%B4%9E%E6%9D%BF%20%E6%8E%92%E9%92%88%202.54) |
| B12 | **Hook-up wire** | 1 | 24 AWG silicone, mixed colours | Inside the bollard. | 8–15 | [搜索 硅胶线 24AWG](https://s.taobao.com/search?q=%E7%A1%85%E8%83%B6%E7%BA%BF%2024AWG) |
| B13 | **Heat-shrink kit** | 1 | 2:1, mixed | | 5–12 | [搜索 热缩管 盒装](https://s.taobao.com/search?q=%E7%83%AD%E7%BC%A9%E7%AE%A1%20%E7%9B%92%E8%A3%85) |
| B14 | **PETG natural / translucent** | 0.5–1 kg | Natural or white PETG | Downward diffuser so the path is a pool, not a point. | 30–70 | [搜索 PETG 1.75 透明 本色](https://s.taobao.com/search?q=PETG%201.75%20%E9%80%8F%E6%98%8E%20%E6%9C%AC%E8%89%B2) |
| B15 | **M3 heat-set inserts** | 50 | Brass, M3×4 or M3×5, OD ~5 mm | PETG lid screws. | 8–15 | [搜索 M3 热熔螺母](https://s.taobao.com/search?q=M3%20%E7%83%AD%E7%86%94%E8%9E%BA%E6%AF%8D) |
| B16 | **M3 screw kit** | 1 | M3×6 / 8 / 12 / 16, hex or Philips | | 8–16 | [搜索 M3 螺丝套装](https://s.taobao.com/search?q=M3%20%E8%9E%BA%E4%B8%9D%E5%A5%97%E8%A3%85) |
| B17 | **Silicone gasket strip** | 1 m | 2–3 mm, adhesive or D-profile | Rubric enclosure; dust/rain story for the report. | 6–15 | [搜索 硅胶密封条 3mm](https://s.taobao.com/search?q=%E7%A1%85%E8%83%B6%E5%AF%86%E5%B0%81%E6%9D%A1%203mm) |
| B18 | **Solder + flux** | 1 | 0.8 mm Sn63/Pb37 or Sn99, rosin flux | If the makerspace iron is dry. | 10–20 | [搜索 焊锡丝 0.8mm 松香](https://s.taobao.com/search?q=%E7%84%8A%E9%94%A1%E4%B8%9D%200.8mm) |

---

## Cart C — optional / alternates

| # | Part | Qty | When to buy | Est. ¥ | Taobao / other |
|---|---|---|---|---|---|
| C1 | **VEML7700** I2C module | 1 | Better lux than BH1750 (closer to human-eye, 0–120 klx). BH1750 is enough. | 8–25 | [搜索 VEML7700 模块](https://s.taobao.com/search?q=VEML7700%20%E6%A8%A1%E5%9D%97) · [DFRobot Gravity](https://mall.dfrobot.com.cn/goods-1430.html) |
| C2 | **BSS138 4-ch level shifter** | 2 | Only if a **non-C** LD2410 clone has 5 V UART. Official **LD2410C UART is 3.3 V**. | 4–10 | [搜索 BSS138 电平转换](https://s.taobao.com/search?q=BSS138%20%E7%94%B5%E5%B9%B3%E8%BD%AC%E6%8D%A2) |
| C3 | **Seeed XIAO ESP32-S3** | 1 | Smaller final board after DevKit bring-up. Same chip family. | 35–55 | [Seeed 官方店](https://shop123145485.taobao.com/) · [搜索 XIAO ESP32-S3](https://s.taobao.com/search?q=XIAO%20ESP32-S3%20seeed) |
| C4 | **PG7 cable gland** | 4 | USB / sensor cable into the bollard. | 4–10 | [搜索 PG7 防水接头](https://s.taobao.com/search?q=PG7%20%E9%98%B2%E6%B0%B4%E6%8E%A5%E5%A4%B4) |
| C5 | **Rubber feet** | 1 pack | Stop the demo unit sliding. | 3–8 | [搜索 橡胶脚垫](https://s.taobao.com/search?q=%E6%A9%A1%E8%83%B6%E8%84%9A%E5%9E%AB) |
| C6 | **Breadboard 3.3/5 V PSU** | 1 | MB-102 style, from USB or barrel. Handy; not required if the DevKit 3V3 is clean. | 6–12 | [搜索 面包板电源模块](https://s.taobao.com/search?q=%E9%9D%A2%E5%8C%85%E6%9D%BF%E7%94%B5%E6%BA%90%E6%A8%A1%E5%9D%97) |
| C7 | **2N7000 / 2N7002** | 10 | Tiny MOSFET for the amber LED if you do not want AO3400 for both. | 2–5 | [搜索 2N7000](https://s.taobao.com/search?q=2N7000%20TO-92) |
| C8 | **IRLZ44N** TO-220 | 2 | Overkill for 200 lm. Only if AO3400 stock is fake / won’t switch. | 4–10 | [搜索 IRLZ44N](https://s.taobao.com/search?q=IRLZ44N) |
| C9 | **Small 8 Ω speaker** | 1 | Listen to CD4040 ticks. Phone speaker is enough for assessor playback. | 5–12 | [搜索 8欧 0.5W 喇叭](https://s.taobao.com/search?q=8%E6%AC%A7%200.5W%20%E5%96%87%E5%8F%AD) |
| C10 | **TLP-USR01H** MEMS ultrasonic front-end | 1 | Analog + digital out, HP ~50 kHz. Rare on Taobao; design on [OSHWHub](https://oshwhub.com/Fungus/TLP_USR01chao-sheng-bo-zhuan-gan). Do not block Cart A on this. | ? | Search `TLP-USR01` · ask the project author |
| C11 | **Knowles analog MEMS** (wideband) | 1 | Bare **SPU0410**-class parts are obsolete / hard to reflow. Only if you will JLCPCB a breakout. | — | LCSC / 立创: search remaining analog SiSonic; **not** SPH0641 (PDM). |

---

## Cart D — stretch (after the key feature works)

| # | Part | Why later | Est. ¥ | Taobao |
|---|---|---|---|---|
| D1 | 6 V 1 W solar + Schottky | Proposal stretch. Document as v2. | 15–40 | [搜索 6V 1W 太阳能电池板](https://s.taobao.com/search?q=6V%201W%20%E5%A4%AA%E9%98%B3%E8%83%BD%E7%94%B5%E6%B1%A0%E6%9D%BF) |
| D2 | INMP441 **as extra**, not the only bat path | I2S 48 kHz toy / spectrogram screenshot. Does **not** replace A5+A6. | 8–15 | [搜索 INMP441](https://s.taobao.com/search?q=INMP441%20I2S%20%E9%BA%A6%E5%85%8B%E9%A3%8E) |
| D3 | LD2450 | Multi-target radar. Out of scope. | 20–40 | [搜索 LD2450 海凌科](https://s.taobao.com/search?q=LD2450%20%E6%B5%B7%E5%87%8C%E7%A7%91) |
| D4 | Waterproof enclosure spray / conformal coat | After PETG is printed. | 15–30 | [搜索 三防漆](https://s.taobao.com/search?q=%E4%B8%89%E9%98%B2%E6%BC%86%20PCB) |

---

## AVOID — do not put on the cart

| Part | Why |
|---|---|
| **SG90** (plastic gear) | Blade + wind. Buy MG90S. |
| **Cool-white / 6500 K LED** | Opposite of 2200 K / PC-amber; contradicts the ecology claim. |
| **INMP441 / SPH0641 / 192 kHz USB mic as the only detector** | I2S/PDM or a laptop pipeline is not the analog ADC demo. SPH0641 is 1.8 V PDM. |
| **ICS-40730 as a bat mic** | Datasheet **20 Hz–20 kHz**. Fine for speech; misses 25–34 kHz. |
| **Anabat / Echo Meter / Song Meter** | Thousands of CNY. Playback + analog heads is the coursework path. |
| **PIR HC-SR501 as the only presence sensor** | Proposal is mmWave. PIR is a last-resort spare only. |
| **Second “solar PSU” brick** | One 18650 + one buck. |
| **Phone / BLE screen as the primary UI** | Rubric: non-screen feedback. BLE log is maintainer-only, later. |
| **LD2410D-only bargain** | Different firmware / fewer knobs. Get **LD2410C**. |
| **MG90S 360° continuous** | You need a known 180° position for the blade. |

---

## Wiring contract (so the cart matches the firmware)

| Function | Bus | DevKit-S3 pins | Power |
|---|---|---|---|
| BH1750 SDA / SCL | I2C0 | GPIO 8 / 9 | 3.3 V |
| LD2410 TX / RX | UART1 | GPIO 17 / 18 | **5 V** module, UART **3.3 V** on C |
| Bat envelope (MAX9814 **or** LM358 out) | ADC1 | GPIO 1 | 3.3 V analog. Two heads: mux later or use GPIO 2 for the second ADC. |
| Path LED MOSFET gate | LEDC | GPIO 6 | LED on **5 V** through MOSFET to GND |
| Servo signal | LEDC | GPIO 7 | Servo **5 V from buck / USB adaptor**, **common GND**. Not from the ESP32 5 V pin. |
| Underside amber | GPIO / LEDC | GPIO 5 | 3.3 V + resistor |
| Battery sense | ADC1 | GPIO 2 | Divider if you use 18650 |

Do not sample ADC while the servo is moving.

---

## Analog bat path (what you are actually building)

```
Playback (phone 10–15 kHz  OR  40 kHz TX)
        │
        ├─► MAX9814 / MAX4466 ──► ESP32 ADC  (gate ~10–15 kHz in firmware)
        │
        └─► TCT40-16R ──► LM358 amp ──► diode envelope ──► ESP32 ADC
                              └── optional CD4040 ÷16 ──► speaker / second ADC
```

Firmware: dual-band **energy / pulse-rate gates**, not a species ID. *Nyctophilus* (faint 40–80 kHz) is **not** a live-detection claim.

---

## Tools you probably already have (do not re-buy unless missing)

Soldering iron, flush cutters, wire strippers, multimeter, hot glue, CA glue, sandpaper, USB power meter (nice). **3D print** PETG at a makerspace if you do not own a printer — PETG needs an enclosure / hotter hotend than PLA.

---

## Australia backup if Taobao is too slow

| Need | Local-ish |
|---|---|
| ESP32-S3, BH1750, Dupont, breadboard | [Core Electronics](https://core-electronics.com.au/) (Newcastle; ships nationally) |
| LEDs, proto, 18650 holder, resistors | Jaycar |
| Same modules, slower than Taobao but AUD checkout | AliExpress search the same SKUs |

---

## Spreadsheet

Machine-readable copy: [`Hardware_BOM.csv`](Hardware_BOM.csv).
