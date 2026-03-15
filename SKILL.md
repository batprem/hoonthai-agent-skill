---
name: set-analysis
description: >
  Thai stock financial analysis skill using SET MCP data. Use this skill whenever the user asks about
  Thai stocks, SET-listed companies, financial statements (งบการเงิน), stock analysis, company comparison,
  financial ratios, or anything related to Thailand's Securities Exchange. Trigger on mentions of Thai
  stock symbols (e.g., PTT, AOT, CPALL, SCC, BDMS), "หุ้น", "งบการเงิน", "วิเคราะห์หุ้น", "เปรียบเทียบหุ้น",
  "SET", "ตลาดหลักทรัพย์", financial health, profitability analysis, or balance sheet review of Thai companies.
  Also trigger when the user wants a financial report, stock screening, or investment research on SET-listed stocks,
  even if they don't explicitly say "SET" or "Thai stock".
---

# SET Financial Analysis Skill

This skill helps Claude analyze financial data from Thailand's Securities Exchange (SET) using the `get_financial_statement` MCP tool. It provides deep fundamental analysis covering company quality assessment, debt structure analysis, profitability diagnosis, cash flow health, and red flag detection — following the same framework Thai financial analysts use.

## Available MCP Tool

```
get_financial_statement(symbol, from_year, to_year)
```

- **symbol**: Thai stock ticker (e.g., "PTT", "AOT", "CPALL")
- **from_year / to_year**: Year range for data (e.g., 2021–2024)
- **Returns**: Pipe-delimited (`|`) text containing Business Type, Income Statement, Balance Sheet, and Cash Flow Statement — all in Thai, unit: Million THB

## Data Format & Account Code Reference

The MCP returns three sections as pipe-delimited tables (`accountCode|accountName|year1|year2|...`). See `references/account_codes.md` for the full mapping. Key codes used throughout this skill:

### Income Statement (4xxxxx)
| Code | English | Thai |
|------|---------|------|
| 410101 | Revenue | รายได้จากการขายและให้บริการ |
| 419999 | Total Revenue | รวมรายได้ |
| 420400 | COGS | ต้นทุน |
| 422100 | SG&A | ค่าใช้จ่ายในการขายและบริหาร |
| 422110 | Selling Expenses | ค่าใช้จ่ายในการขาย |
| 422120 | Admin Expenses | ค่าใช้จ่ายในการบริหาร |
| 429900 | Finance Cost | ต้นทุนทางการเงิน |
| 409992 | EBIT | กำไรก่อนดอกเบี้ยและภาษี |
| 409996 | Net Profit | กำไรสุทธิ |
| 480100 | EPS (Baht/Share) | กำไรต่อหุ้น |

### Balance Sheet (1xxxxx–3xxxxx)
| Code | English | Thai |
|------|---------|------|
| 109999 | Total Assets | รวมสินทรัพย์ |
| 119999 | Current Assets | รวมสินทรัพย์หมุนเวียน |
| 110100 | Cash | เงินสด |
| 112000 | Trade Receivables | ลูกหนี้การค้า |
| 112900 | Inventory | สินค้าคงเหลือ |
| 129999 | Non-Current Assets | รวมสินทรัพย์ไม่หมุนเวียน |
| 123800 | PP&E | ที่ดิน อาคาร และอุปกรณ์ |
| 124100 | Intangible Assets | สินทรัพย์ไม่มีตัวตน (รวม Goodwill) |
| 209999 | Total Liabilities | รวมหนี้สิน |
| 219999 | Current Liabilities | รวมหนี้สินหมุนเวียน |
| 211100 | Trade Payables | เจ้าหนี้การค้า |
| 229999 | Non-Current Liabilities | รวมหนี้สินไม่หมุนเวียน |
| 309998 | Total Equity | รวมส่วนของผู้ถือหุ้น |
| 310100 | Registered Capital | ทุนจดทะเบียน |
| 310200 | Paid-up Capital | ทุนชำระแล้ว |
| 310620 | Retained Earnings (Unappropriated) | กำไรสะสมยังไม่ได้จัดสรร |

### Cash Flow (5xxxxx)
| Code | English | Thai |
|------|---------|------|
| 510300 | Depreciation & Amortization | ค่าเสื่อมราคา |
| 519999 | Operating Cash Flow | เงินสดจากกิจกรรมดำเนินงาน |
| 521700 | CapEx (PP&E purchases) | ซื้อที่ดินอาคารอุปกรณ์ |
| 529999 | Investing Cash Flow | เงินสดจากกิจกรรมลงทุน |
| 539999 | Financing Cash Flow | เงินสดจากกิจกรรมจัดหาเงิน |
| 599999 | Ending Cash | เงินสดสิ้นงวด |

Not all codes appear for every company. Always check what's available before computing.

---

## Workflow

### Step 1: Fetch Data

Call `get_financial_statement` for each stock. Use 3–5 year range for trend analysis. If comparing multiple stocks, call in parallel.

### Step 2: Parse the Data

Split by section headers ("Income Statement:", "Balance Sheet:", "Cash Flow Statement:"), then parse pipe-delimited rows. Write a Python script for reliable parsing:

```python
def parse_set_data(raw_text):
    sections = {}
    current_section = None
    for line in raw_text.strip().split('\n'):
        line = line.strip()
        if not line: continue
        if 'Income Statement:' in line: current_section = 'income'; sections[current_section] = []; continue
        elif 'Balance Sheet:' in line: current_section = 'balance'; sections[current_section] = []; continue
        elif 'Cash Flow Statement:' in line: current_section = 'cashflow'; sections[current_section] = []; continue
        if current_section and '|' in line:
            sections[current_section].append([p.strip() for p in line.split('|')])
    return sections
```

### Step 3: Compute Ratios & Analyze

Compute all ratios below, then apply the deep analysis framework in Step 4. When a data point is missing, skip that ratio and note it.

**Profitability Ratios**
- Gross Profit Margin = (Revenue − COGS) / Revenue × 100
- EBIT Margin = EBIT / Revenue × 100
- Net Profit Margin = Net Profit / Total Revenue × 100
- ROA = Net Profit / Total Assets × 100
- ROE = Net Profit / Total Equity × 100

**Liquidity Ratios**
- Current Ratio = Current Assets / Current Liabilities
- Quick Ratio = (Current Assets − Inventory) / Current Liabilities
- Cash Ratio = Cash / Current Liabilities

**Leverage Ratios**
- D/E Ratio = Total Liabilities / Total Equity
- Debt Ratio = Total Liabilities / Total Assets
- Interest Coverage = EBIT / Finance Cost

**Efficiency Ratios**
- Asset Turnover = Revenue / Total Assets
- Receivable Days = (Trade Receivables / Revenue) × 365
- Inventory Days = (Inventory / COGS) × 365
- Payable Days = (Trade Payables / COGS) × 365
- Cash Conversion Cycle = Receivable Days + Inventory Days − Payable Days

**Cash Flow Ratios**
- Operating CF / Net Income (earnings quality)
- Free Cash Flow = Operating CF − |CapEx|
- CapEx vs Depreciation = |CapEx| / Depreciation

---

### Step 4: Deep Analysis Framework (วิเคราะห์คุณภาพบริษัท)

This is the core of the analysis. After computing ratios, walk through each section below and flag any issues found. The goal is to diagnose the company's health like a financial doctor — looking at symptoms (ratios) and connecting them to root causes.

#### 4.1 วิเคราะห์งบแสดงฐานะการเงิน (Balance Sheet Analysis)

##### 4.1.1 โครงสร้างสินทรัพย์ (Asset Structure)

First, determine the company's asset profile — is it asset-light (current assets > non-current) or asset-heavy (non-current > current)?

- **Asset-light companies** (Current > Non-Current): Focus the analysis on liquidity. These companies rely on working capital management, so receivables, inventory, and cash cycles matter most.
- **Asset-heavy companies** (Non-Current > Current): Focus on whether fixed assets are generating revenue efficiently. Check Asset Turnover trends and whether PP&E growth matches revenue growth.

**Current asset composition check**: The bulk of current assets should be cash, trade receivables, and inventory. If "other current assets" or unusual items make up a large share, flag a potential liquidity concern — the company may have money tied up in non-productive or hard-to-liquidate items.

**Receivables warning signals**: If trade receivables grow faster than revenue AND receivable days are increasing year-over-year, flag two risks: (1) the company may have trouble collecting payments, and (2) in the worst case, this pattern can indicate revenue manipulation through fake sales booked as receivables.

**Inventory warning signals**: If inventory grows faster than COGS AND inventory days are increasing year-over-year, flag the risk of obsolete or deteriorating stock. The company may be producing more than it can sell.

**Goodwill/Intangible asset check**: If intangible assets (code 124100) are large relative to total assets (say >20%), flag that the company may have made expensive acquisitions. Check whether ROA remains healthy and consistent despite the goodwill — if ROA is declining while intangibles are high, the acquisitions may not have been worth the price.

**PP&E growth vs Revenue growth**: If PP&E is increasing AND revenue is also increasing proportionally, this is a positive sign — the company is investing to grow and the investments are paying off. If PP&E grows but revenue doesn't follow, the company may be overinvesting or its new capacity isn't yet productive.

##### 4.1.2 โครงสร้างหนี้สิน (Liability Structure)

The key principle is that the liability structure should match the asset structure:

- If Current Assets > Non-Current Assets → Current Liabilities should be > Non-Current Liabilities (matching structure)
- If Non-Current Assets > Current Assets → Non-Current Liabilities should be > Current Liabilities (matching structure)
- **Red flag**: If a company has heavy non-current assets BUT most of its debt is short-term (current liabilities), this is a maturity mismatch — borrowing short to invest long. This creates rollover risk and potential liquidity crises.

**Debt growth vs Equity growth**: If total liabilities are growing faster than total equity over time, flag an increasing leverage risk. The company is relying more on debt than on its own earnings to grow.

##### 4.1.3 ส่วนของผู้ถือหุ้น (Equity Analysis)

- **Accumulated losses**: If retained earnings (code 310620) are negative and large relative to paid-up capital (code 310100 or 310200), the company has a history of significant losses. This is a major red flag for financial health.
- **Quasi-debt instruments**: Watch for perpetual bonds or hybrid instruments classified under equity. If "other equity components" are unusually large relative to total equity, note that the true leverage may be higher than D/E suggests, because these instruments are economically debt even though they appear as equity.

#### 4.2 วิเคราะห์งบกำไรขาดทุน (Income Statement Analysis)

##### 4.2.1 Revenue & Profit Growth Quality

A quality company shows consistent revenue and net profit growth with stable margins. Specifically:
- Revenue and net profit should grow year-over-year
- Gross margin, EBIT margin, and net margin should be relatively stable across years
- If all three are true, the company demonstrates sustainable earning power

##### 4.2.2 Gross Margin Diagnosis

Gross margin should be consistent over multiple years. If it's declining, diagnose the cause:

- **Raw material / commodity price volatility**: Common in energy, agriculture, manufacturing sectors
- **Underutilized production capacity**: The company may be in an early investment phase with fixed costs spread over low volume
- **Competitive pressure**: Price wars, discounting, or loss of pricing power against competitors

##### 4.2.3 SG&A Efficiency

- **Selling expenses** should grow in line with revenue. If selling expenses grow faster, the company is spending more on marketing per unit of revenue — the marketing spend may not be efficient.
- **Admin expenses** should also grow in line with revenue. If they grow faster, central overhead is expanding disproportionately — a sign of organizational bloat.

##### 4.2.4 Finance Cost Stress Test

If finance cost exceeds 50% of EBIT, flag a serious concern: the company's debt burden is consuming most of its operating profit. This means either (a) the company has taken on too much debt, or (b) operating profit is weak (possibly an early-stage investment with not yet ramped-up revenue). Either way, the margin of safety against interest rate increases is thin.

#### 4.3 วิเคราะห์งบกระแสเงินสด (Cash Flow Analysis)

##### 4.3.1 Operating Cash Flow Health

- Operating Cash Flow should be **positive**. A company that consistently generates positive operating CF is earning real cash from its business, not just paper profits.
- If Operating CF < Net Profit, investigate why. Possible reasons: (a) one-time gains inflating net income, (b) cash trapped in growing receivables or inventory, or (c) early warning sign of accounting manipulation — profits on paper but no actual cash coming in.

##### 4.3.2 Investment Activity Assessment

Look at CapEx (code 521700, shown as negative). Convert to positive and compare against Depreciation (code 510300):
- If |CapEx| > Depreciation → the company is investing more than its assets are wearing down, suggesting growth/expansion phase
- If |CapEx| < Depreciation → the company is underinvesting, possibly milking existing assets without building for the future

##### 4.3.3 Free Cash Flow

Free Cash Flow = Operating CF − |CapEx|. This is the cash left over after maintaining and growing the business. Positive and growing FCF is the hallmark of a financially healthy company.

#### 4.4 ปัญหาธุรกิจผ่านอัตราส่วนทางการเงิน (Diagnosing Business Problems via Ratios)

After computing all ratios, apply these diagnostic rules. Each rule connects a ratio pattern to a specific business problem:

| Signal | Condition | Diagnosis |
|--------|-----------|-----------|
| Strong shareholder returns | ROE > 5% and stable | Company generates good returns for shareholders |
| Efficient asset use | ROA > 5% and stable | Assets are productive and earning their keep |
| Declining asset productivity | ROA declining over time | Investments not generating proportional returns; growth may slow |
| Debt distress risk | D/E > 1.5 AND Operating CF negative | Dangerous combination — high debt with no operating cash to service it |
| Declining asset efficiency | Asset Turnover trending down | May be in investment phase (assets up, revenue hasn't caught up yet) |
| Collection problems | Receivable Days increasing | Trouble collecting from customers; or revenue quality deteriorating |
| Inventory obsolescence risk | Inventory Days increasing | Stock piling up — may be obsolete, slow-moving, or overproduced |
| Supplier payment stress | Payable Days increasing | Company stretching payment terms — possible liquidity squeeze |
| High working capital need | Cash Conversion Cycle > 75 days | Business requires a lot of working capital; watch for liquidity strain |
| Gross margin erosion | Gross Margin declining | Cost pressure, underutilized capacity, or competitive price wars |
| Cost overrun (SG&A) | Gross Margin stable but EBIT Margin declining | Problem is in selling & admin expenses, not production costs |
| Debt burden | EBIT Margin stable but Net Margin declining | Interest costs eating into profit — too much debt in capital structure |

Present each finding as a clear verdict with supporting data. For example: "⚠️ ระยะเวลาเก็บหนี้เพิ่มจาก 45 วัน (2022) → 62 วัน (2024) ขณะที่รายได้โตเพียง 5% — ระวังปัญหาการเก็บหนี้"

---

### Step 5: Present Results

#### In-Chat Summary

Structure the response as:

1. **ภาพรวมบริษัท (Company Overview)** — business type, size (total assets, revenue), key numbers
2. **ตารางอัตราส่วนสำคัญ (Key Ratios Table)** — clean markdown table with year-over-year values
3. **วิเคราะห์คุณภาพบริษัท (Quality Analysis)** — walk through findings from Step 4, organized by:
   - งบแสดงฐานะการเงิน (Balance Sheet findings)
   - งบกำไรขาดทุน (Income Statement findings)
   - งบกระแสเงินสด (Cash Flow findings)
   - สัญญาณเตือนจากอัตราส่วนทางการเงิน (Ratio-based red flags)
4. **สรุป (Conclusion)** — overall health assessment with key strengths and concerns

Use ✅ for positive findings, ⚠️ for warnings, and ❌ for serious red flags.

For multi-stock comparison, present side-by-side and call out which company is stronger on each dimension.

**Always state that this is data analysis, not investment advice.**

#### Excel Output

When doing detailed analysis, create an .xlsx file with sheets:
1. **Income Statement** — full data with Thai+English headers
2. **Balance Sheet** — full data
3. **Cash Flow** — full data
4. **Ratios** — all computed ratios with conditional formatting (green=improving, red=deteriorating)
5. **Quality Analysis** — summary of findings from Step 4 with verdict icons
6. **Comparison** (if multiple stocks) — side-by-side

Formatting: `#,##0.00` for millions, `0.00%` for percentages, bold headers with background color, freeze panes, auto column widths.

---

## Important Notes

- All monetary values are in **Million THB (ล้านบาท)**
- EPS (code 480100) is per-share in Baht, not in millions
- Uses **consolidated financial statements** (งบการเงินรวม)
- Some codes may not exist for certain companies — always check before computing
- For banking/financial sector: skip inventory-related ratios, adapt analysis accordingly
- Present analysis in Thai with English terms where appropriate
- This is financial data analysis, **not investment advice**
