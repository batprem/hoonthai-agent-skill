# SET Account Code Reference

A comprehensive mapping of SET account codes to their Thai names and English translations.
Use this as a lookup when parsing financial data from the MCP tool.

## Income Statement Codes (4xxxxx)

| Code | Thai | English | Category |
|------|------|---------|----------|
| 400000 | งบกำไรขาดทุนเบ็ดเสร็จ | Comprehensive Income Statement | Header |
| 409991 | กำไร(ขาดทุน)สุทธิสำหรับงวด / กำไร(ขาดทุน)จากการดำเนินงานต่อเนื่อง | Net Profit / Profit from Continuing Operations | Key |
| 409992 | กำไร(ขาดทุน)ก่อนต้นทุนทางการเงินและภาษีเงินได้ | EBIT | Key |
| 409995 | กำไร(ขาดทุน)จากการดำเนินงานต่อเนื่อง | Profit from Continuing Operations | Key |
| 409996 | กำไร(ขาดทุน)สุทธิสำหรับงวด | Net Profit for the Period | Key |
| 409999 | กำไร(ขาดทุน)เบ็ดเสร็จรวมสำหรับงวด | Total Comprehensive Income | Key |
| 410000 | รายได้ | Revenue | Header |
| 410100 | รายได้จากการดำเนินธุรกิจ | Operating Revenue | Key |
| 410101 | รายได้จากการขายและให้บริการ | Revenue from Sales & Services | Key |
| 410200 | รายได้อื่น | Other Income | |
| 419999 | รวมรายได้ | Total Revenue | Key |
| 420000 | ต้นทุนและค่าใช้จ่าย | Costs & Expenses | Header |
| 420400 | ต้นทุน | Cost of Goods Sold | Key |
| 422100 | ค่าใช้จ่ายในการขายและบริหาร | SG&A Expenses | Key |
| 422110 | ค่าใช้จ่ายในการขาย | Selling Expenses | |
| 422120 | ค่าใช้จ่ายในการบริหาร | Administrative Expenses | |
| 429900 | ต้นทุนทางการเงิน | Finance Cost | Key |
| 429999 | รวมต้นทุนและค่าใช้จ่าย | Total Costs & Expenses | Key |
| 439700 | กำไร(ขาดทุน)อื่น | Other Gains (Losses) | |
| 439710 | กำไร(ขาดทุน)จากอัตราแลกเปลี่ยน | FX Gains (Losses) | |
| 439800 | ภาษีเงินได้ | Income Tax | Key |
| 460100 | การแบ่งปันกำไรสุทธิ: ผู้ถือหุ้นบริษัทใหญ่ | Net Profit: Owners of Parent | Key |
| 480100 | กำไร(ขาดทุน)ต่อหุ้นขั้นพื้นฐาน (บาท/หุ้น) | Basic EPS (Baht/Share) | Key |

## Balance Sheet - Assets (1xxxxx)

| Code | Thai | English | Category |
|------|------|---------|----------|
| 100000 | สินทรัพย์ | Assets | Header |
| 109999 | รวมสินทรัพย์ | Total Assets | Key |
| 110000 | สินทรัพย์หมุนเวียน | Current Assets | Header |
| 110100 | เงินสดและรายการเทียบเท่าเงินสด | Cash & Cash Equivalents | Key |
| 111600 | เงินลงทุนระยะสั้น | Short-term Investments | |
| 112000 | ลูกหนี้การค้าและลูกหนี้หมุนเวียนอื่น | Trade & Other Receivables | Key |
| 112300 | เงินให้กู้ยืมระยะสั้นและดอกเบี้ยค้างรับ | Short-term Loans & Accrued Interest | |
| 112900 | สินค้าคงเหลือ | Inventory | Key |
| 119999 | รวมสินทรัพย์หมุนเวียน | Total Current Assets | Key |
| 120000 | สินทรัพย์ไม่หมุนเวียน | Non-Current Assets | Header |
| 123800 | ที่ดิน อาคาร และอุปกรณ์ | Property, Plant & Equipment | Key |
| 123900 | สินทรัพย์สิทธิการใช้ | Right-of-use Assets | |
| 124100 | สินทรัพย์ไม่มีตัวตน | Intangible Assets | |
| 129999 | รวมสินทรัพย์ไม่หมุนเวียน | Total Non-Current Assets | Key |

## Balance Sheet - Liabilities (2xxxxx)

| Code | Thai | English | Category |
|------|------|---------|----------|
| 200000 | หนี้สิน | Liabilities | Header |
| 209999 | รวมหนี้สิน | Total Liabilities | Key |
| 210000 | หนี้สินหมุนเวียน | Current Liabilities | Header |
| 211100 | เจ้าหนี้การค้าและเจ้าหนี้หมุนเวียนอื่น | Trade & Other Payables | Key |
| 211700 | เงินกู้ยืมระยะสั้น | Short-term Borrowings | |
| 211800 | ส่วนของหนี้สินระยะยาวที่ถึงกำหนดชำระภายใน 1 ปี | Current Portion of Long-term Debt | |
| 219999 | รวมหนี้สินหมุนเวียน | Total Current Liabilities | Key |
| 220000 | หนี้สินไม่หมุนเวียน | Non-Current Liabilities | Header |
| 221800 | หนี้สินระยะยาว | Long-term Borrowings | Key |
| 229999 | รวมหนี้สินไม่หมุนเวียน | Total Non-Current Liabilities | Key |

## Balance Sheet - Equity (3xxxxx)

| Code | Thai | English | Category |
|------|------|---------|----------|
| 300000 | ส่วนของผู้ถือหุ้น | Shareholders' Equity | Header |
| 309998 | รวมส่วนของผู้ถือหุ้น | Total Equity | Key |
| 310200 | ทุนที่ออกและชำระแล้ว | Paid-up Capital | Key |
| 310400 | ส่วนเกินมูลค่าหุ้น | Share Premium | |
| 310600 | กำไร(ขาดทุน)สะสม | Retained Earnings | Key |
| 310620 | กำไรสะสมยังไม่ได้จัดสรร | Unappropriated Retained Earnings | Key |

## Cash Flow Statement Codes (5xxxxx)

| Code | Thai | English | Category |
|------|------|---------|----------|
| 510000 | เงินสดสุทธิจากกิจกรรมดำเนินงาน | Operating Activities | Header |
| 510200 | กำไรก่อนต้นทุนทางการเงินและภาษี | Profit Before Finance Cost & Tax | |
| 510300 | ค่าเสื่อมราคาและค่าตัดจำหน่าย | Depreciation & Amortization | Key |
| 519999 | เงินสดสุทธิจากกิจกรรมดำเนินงาน | Net Cash from Operations | Key |
| 520000 | เงินสดสุทธิจากกิจกรรมลงทุน | Investing Activities | Header |
| 521700 | เงินสดจ่ายซื้อสินทรัพย์ถาวร | CapEx (Purchase of PP&E) | Key |
| 529999 | เงินสดสุทธิจากกิจกรรมลงทุน | Net Cash from Investing | Key |
| 530000 | เงินสดสุทธิจากกิจกรรมจัดหาเงิน | Financing Activities | Header |
| 539800 | จ่ายเงินปันผล | Dividends Paid | Key |
| 539999 | เงินสดสุทธิจากกิจกรรมจัดหาเงิน | Net Cash from Financing | Key |
| 509999 | เงินสดเพิ่มขึ้น(ลดลง)สุทธิ | Net Change in Cash | Key |
| 599998 | เงินสดต้นงวด | Beginning Cash | |
| 599999 | เงินสดสิ้นงวด | Ending Cash | Key |
