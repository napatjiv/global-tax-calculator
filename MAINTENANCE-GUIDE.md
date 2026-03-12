# Tax Calculator Maintenance Guide

## 🔧 Quick Update Process (10 minutes per country)

### When to Update
**Every January** - Most countries update tax brackets annually for inflation

### How to Update a Country

1. **Find the TAX_DATA object** in the HTML file (around line 300)

2. **Pick a country** you want to update

3. **Go to the official source** listed in the `source:` field

4. **Update the brackets array:**
   ```javascript
   brackets: [
       {threshold: 0, rate: 0},           // Tax-free up to this amount
       {threshold: 12400, rate: 0.10},    // 10% from $12,400 to next threshold
       {threshold: 50400, rate: 0.12},    // 12% from $50,400 to next threshold
       // ... continue for all brackets
   ]
   ```

5. **Update social insurance if changed:**
   ```javascript
   socialInsurance: {
       rate: 0.0765,    // Employee portion (as decimal)
       cap: 160200,     // Maximum income subject to this tax
       additionalAbove: 0.0235  // Additional % above cap (if any)
   }
   ```

6. **Change verified to true:**
   ```javascript
   verified: true,  // You've verified this is current!
   ```

7. **Test:** Open the HTML file, enter different incomes, verify calculations look reasonable

---

## 📋 Official Sources Checklist

### ✅ Already Verified (2026 Data)
- **United States**: https://www.irs.gov/pub/irs-drop/rp-25-32.pdf
- **United Kingdom**: https://www.gov.uk/income-tax-rates
- **Canada**: https://www.canada.ca/en/revenue-agency/services/tax/individuals/frequently-asked-questions-individuals/canadian-income-tax-rates-individuals-current-previous-years.html
- **Australia**: https://www.ato.gov.au/tax-rates-and-codes/tax-rates-australian-residents
- **Singapore**: https://www.iras.gov.sg/taxes/individual-income-tax/basics-of-individual-income-tax/tax-residency-and-tax-rates/individual-income-tax-rates

### ⚠️ Need Verification (Estimates Only)
- **Germany**: https://www.bundesfinanzministerium.de/
- **France**: https://www.impots.gouv.fr/
- **Japan**: https://www.nta.go.jp/english/taxes/individual/12018.htm
- **Thailand**: https://www.rd.go.th/english/
- **Vietnam**: General Department of Taxation website
- **Portugal**: https://info.portaldasfinancas.gov.pt/
- **Mexico**: https://www.sat.gob.mx/
- **Costa Rica**: Ministerio de Hacienda website

### Tax Havens (Should Never Change)
- UAE, Monaco, Cayman Islands - no income tax

---

## 🎯 Example Update: United States

**When IRS releases 2027 brackets (usually November 2026):**

1. Go to https://www.irs.gov/ and search "revenue procedure 2027"
2. Find the tax bracket table for single filers
3. Update this section:

```javascript
'United States': {
    brackets: [
        {threshold: 0, rate: 0},
        {threshold: 12900, rate: 0.10},     // NEW for 2027 (was 12400)
        {threshold: 52500, rate: 0.12},     // NEW for 2027 (was 50400)
        // ... continue with all new brackets
    ],
    socialInsurance: {
        rate: 0.0765,
        cap: 168600,                         // NEW cap for 2027
        additionalAbove: 0.0235
    },
    verified: true,                          // Keep as true!
    source: 'https://www.irs.gov/pub/irs-drop/rp-26-XX.pdf',  // Update to new doc
    // ... rest stays the same
}
```

4. Save and test!

---

## 💡 Common Issues & Solutions

### "Calculations look way off"
- Did you use the right currency? Check `currency:` field
- Are brackets in local currency (not USD)?
- Did you enter rates as decimals (0.10 = 10%, not 10)?

### "How do I handle multiple tax rates?"
Progressive taxation works automatically:
- First $50k taxed at 10%
- Next $50k taxed at 20%
- Above $100k taxed at 30%

Just list the brackets in order with cumulative thresholds.

### "What about deductions/credits?"
This calculator uses SIMPLIFIED brackets:
- US: Assumes standard deduction
- Others: Usually assumes basic personal allowance
- For 100% accuracy, users need professional tax software

### "Exchange rates outdated?"
Update the `EXCHANGE_RATES` object at the top:
```javascript
const EXCHANGE_RATES = {
    'USD': 1,
    'GBP': 0.79,  // Update these annually
    'CAD': 1.36,
    // ... etc
};
```
Get current rates from xe.com or similar

---

## 🚀 Adding a New Country

Copy this template:

```javascript
'New Country': {
    brackets: [
        {threshold: 0, rate: 0},
        {threshold: XXXX, rate: 0.XX},
        // Add all brackets
    ],
    socialInsurance: {
        rate: 0.XX,      // Employee social security %
        cap: XXXXX       // Max income subject to it
    },
    currency: 'XXX',     // ISO currency code
    verified: true,      // Only if from official source!
    source: 'https://...',
    notes: 'Key details about what is/isn't included'
}
```

---

## ⏰ Annual Maintenance Schedule

**Every December:**
1. Check IRS for US updates (usually mid-Nov)
2. Check HMRC for UK updates (usually Budget Day)
3. Check CRA for Canada updates (usually late Dec)
4. Check ATO for Australia updates (usually July, but plan ahead)
5. Update exchange rates
6. Test with sample incomes: $50k, $100k, $500k, $10M

**Takes ~2 hours total if you do all countries**

**Or just update the ones you care about!**

---

## 📞 When Things Break

1. **Open browser console** (F12)
2. **Look for errors** - usually typos in the brackets array
3. **Common mistakes:**
   - Missing comma between brackets
   - Rate as 10 instead of 0.10
   - Threshold as string "50000" instead of number 50000
   - Missing closing bracket }

4. **Can't fix it?**
   - Download a fresh copy of the original
   - Re-apply your updates one country at a time
   - Test after each update

---

## 🎓 Understanding the Code

The calculator does this for each country:

1. **Converts USD to local currency** using exchange rates
2. **Calculates income tax** using progressive brackets
3. **Adds social insurance** (capped)
4. **Subtracts total tax from gross**  
5. **Converts back to USD** for comparison
6. **Sorts countries by take-home** (highest first)

The chart shows everything in USD so countries are comparable.

---

## 📌 Pro Tips

- **Keep a spreadsheet** of when you last updated each country
- **Set a calendar reminder** for December tax updates
- **Test edge cases** like $0, $100k, $1M, $1B
- **Compare to official calculators** from government sites
- **Users will complain** - have links to official sources ready
- **Perfect is the enemy of good** - estimates are fine if marked as such

---

## 🔒 Version Control (Optional but Smart)

Keep old versions with dates:
- `tax-calculator-2026-03.html`
- `tax-calculator-2027-01.html`

So you can compare what changed year-over-year.

---

## Questions?

The code is heavily commented. Search for:
- `TAX_DATA` - all country data
- `calculateTax` - how brackets work
- `EXCHANGE_RATES` - currency conversions

**You got this!** 🚀
