# 内容包格式

引擎不认内容，只认这个格式。以后任何资料（教材、书、笔记、别的课程）
按这个格式产出 JSON，就能挂进学习台，**不用改引擎**。

---

## 最小可用的包

```json
{
  "id": "my-pack",
  "title": "我的资料",
  "courses": [
    {
      "id": "c1",
      "title": "第一门课",
      "desc": "一句话说明",
      "units": [
        {
          "id": "u1",
          "title": "第一个单元",
          "items": [
            {
              "q": "题干\n可以换行",
              "type": "choice",
              "options": ["选项一", "选项二", "选项三"],
              "answer": 1,
              "explain": "解析：为什么是第二个"
            }
          ]
        }
      ]
    }
  ]
}
```

把文件命名成 `任意名字.json`，在 App 里「内容包 → 导入内容包」选中它即可。

---

## 两种单元

### A. 静态单元（`items`）——放任意资料

适合：教材原文、笔记、题库、别的课程。

```json
{
  "id": "u1", "title": "概念题",
  "items": [
    {
      "q": "点差的英文是？",
      "type": "choice",
      "options": ["Spread", "Swap", "Slippage"],
      "answer": 0,
      "explain": "Spread = 点差；Swap = 隔夜费；Slippage = 滑点。"
    },
    {
      "q": "0.2 手 EUR/USD 走 30 pip，盈亏多少美元？",
      "type": "number",
      "answer": 60,
      "dp": 2,
      "unit": "美元",
      "explain": "0.2 × 30 × $10 = $60。"
    }
  ]
}
```

一个单元里放多题，每次随机抽一题，所以同一个单元可以反复练。

**`type` 字段**

| 值 | 说明 |
|---|---|
| `choice` | 单选。`options` 数组 + `answer` 是**从 0 开始的序号** |
| `number` | 填数。`answer` 数字，`dp` 是保留几位（判分按这个精度四舍五入） |

**可选字段**

| 字段 | 作用 |
|---|---|
| `assume` | 本题假设，格式 `[["账户币种","USD"],["滑点","忽略"]]` |
| `causes` | `{"错误值": "错因"}`，用来归类错误 |

### B. 模板单元（`template`）——随机出数

适合：需要每次换数字的计算题。

```json
{ "id": "u2", "title": "多头往返", "template": "fx.long_roundtrip" }
```

模板是**引擎里的代码**，目前只有 FX 这一套（`fx.*`，见下）。
要加新模板必须改代码；只想放内容的话用静态单元就够了。

现有模板：

```
fx.direction    fx.rotate       fx.cross        fx.exposure
fx.long_roundtrip  fx.short_roundtrip  fx.spread_cost
fx.equity       fx.margin_level  fx.stopout      fx.eff_lev
```

---

## 放进手机

**方式 1：导入（推荐）**
把 JSON 传到手机 → App 里「内容包 → 导入内容包」→ 选中文件。
存在手机本地，离线可用，随时可删。

**方式 2：服务器（GitHub Pages）**
放到 `packs/` 目录，并在 `packs/index.json` 里登记文件名：

```json
{ "files": ["fx-basics.json", "my-pack.json"] }
```

App 打开时会自动拉取。改了内容只要推上去，手机下次打开就是新的。

---

## 改完必须自检

```
cd fxapp/tools
py -3 build.py
```

它会检查：

- 42 组 golden（JS 公式和 Python 一致）
- 内容包里引用的模板是否都存在
- 静态题的 `answer` 是否越界 / 缺失
- 每个单元是否既有模板又有题目

输出出现 `错误：` 就说明包写坏了，App 里那个单元会显示"没有可用题目"。

---

## 写内容时值得遵守的三条

1. **每题都要有 `explain`。** 没有解析的题只能判对错，不能教东西。
2. **`answer` 只能用序号，不能用文字。** 顺序变了答案就错，所以别在 `options` 里插新选项。
3. **同一个单元里的题难度要一致。** 单元是最小复习单位，难度混在一起会让复习排期失真。
