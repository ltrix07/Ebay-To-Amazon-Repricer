# Ebay-To-Amazon-Repricer
A complete automation system for dynamic repricing on Amazon based on real eBay market data.

This project is designed to work together with  
➡ **[Ebay-Checker](https://github.com/ltrix07/Ebay-Checker)**  
and acts as the second stage of a two-step pipeline:

1. **Ebay-Checker** — collects real competitor prices from eBay.  
2. **Ebay-To-Amazon-Repricer** — recalculates prices and updates them on Amazon via SP-API.

The system eliminates manual price adjustments and keeps Amazon listings competitive and profitable.

---

# 🚀 Key Features

### ✅ **Full automation cycle**
- Reads fresh data collected by *Ebay-Checker*  
- Calculates new prices based on preconfigured rules  
- Updates prices on Amazon via the official SP-API  
- Supports periodic background execution (cron / server scripts)

### ✅ **Flexible pricing engine**
- Markup percentages  
- Minimum/maximum allowed margin  
- Custom rounding strategies  
- Safety limits (don’t go below cost / MAP / threshold)  
- Multi-store logic

### ✅ **Modular architecture**
Each module is responsible for a specific part of the repricing workflow:
- data loading  
- data cleaning  
- price calculation  
- Amazon API sync  
- shop/account configuration  

### ✅ **Server-friendly**
- CLI entry point  
- `restart.sh` for server deployment  
- Works on Linux/Ubuntu  
- Can be scheduled to run automatically  

---

# 🧩 Architecture Overview
                          |                     |
                          ↓                     ↓
                    Data Parser          Price Update Handler
                    Pricing Rules        Logging & Validation
                    
# 📁 Project Structure (Detailed)
.
├── main.py # Main pipeline runner
├── moduls.py # Core repricing logic
├── functions.py # Helper utilities (I/O, parsing, validation)
├── params.py # Configuration (paths, pricing rules, shop settings)
├── add_shop.py # Store/account management
├── creds/ # Amazon SP-API credentials
├── restart.sh # Restart helper for server deployments
└── README.md # Documentation


### **main.py**
- Entry point  
- Loads config  
- Runs repricing routine  
- Calls the Amazon update module  

### **moduls.py**
Responsible for:
- reading eBay result tables  
- mapping items to Amazon SKUs/ASINs  
- applying pricing formulas  
- handling data consistency issues  
- preparing payloads for SP-API  

### **functions.py**
Utility layer:
- CSV/Excel file reading  
- formatting helpers  
- error-safe wrappers  
- logging helpers  

### **params.py**
Your configuration file:
- table path from *Ebay-Checker*  
- markup values  
- min/max margin  
- rounding rules  
- default fallback values  
- shop identifiers  
- Amazon marketplace regions  

---

# 🔗 Integration With Ebay-Checker

### Ebay-Checker output example:

| ebay_title | ebay_price | condition | url | last_update | asin | sku |
|-----------|------------|-----------|-----|-------------|------|-----|
| Sony WH1000XM4 | 128.90 | used | ... | 2025-11-13 | B08MVGF8N3 | WH1000XM4-BLK |

The repricer uses:
- `ebay_price`  
- `condition`  
- `asin` / `sku`  
- additional metadata depending on your setup  

---

# 🧮 Pricing Logic (Example)

Below is a simplified conceptual example — actual formulas depend on `moduls.py`:

```python
base_price = ebay_price
markup_price = base_price * (1 + MARKUP_PERCENT / 100)

if markup_price < MIN_ALLOWED_PRICE:
    final_price = MIN_ALLOWED_PRICE
elif markup_price > MAX_ALLOWED_PRICE:
    final_price = MAX_ALLOWED_PRICE
else:
    final_price = markup_price

final_price = round_to_ending(final_price, ".99")
```

Supports:
- dynamic markup
- margin enforcement
- rounding to .99 or .49
- conditional rules based on category or condition

# 🛠 Amazon SP-API Integration
The project updates prices using the Listings Items API or Product Pricing API.

Typical payload structure:
```json
{
  "sku": "YOUR_SKU",
  "pricing": {
    "price": 149.99,
    "currency": "USD"
  }
}
```
Requests are sent with:
- retries
- error handling
- response validation

# 📦 Installation
## 1. Clone the repo
```bash
git clone https://github.com/ltrix07/Ebay-To-Amazon-Repricer
cd Ebay-To-Amazon-Repricer
```

## 2. Install dependencies
```bash
pip install -r requirements.txt
```

## 3. Configure parameters
```bash
params.py
creds/amazon_keys.json
```

## 4. Run
```bash
python main.py
```
