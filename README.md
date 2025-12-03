This repository is a **free-to-use template** demonstrating how to build a custom reporting backend for the **Report Creator** feature in the **Salla Partners Portal**. It shows developers how to:

The goal of this example is to provide a clear, lightweight starting point that any partner or developer can fork, extend, and adapt into their own analytics or reporting tools.

The core idea:
- Server calls `https://api.salla.dev/admin/v2/products` with your access token.
- It walks through **all pages** using Salla’s pagination.
- It computes **totals** (counts, quantities, etc.).
- It returns a rich JSON object where every block maps to a display type (unit, bar, breakdown, calendar, distribution, pipe, plot, ranking, summary, AG grid).

---

## Prerequisites

- **Node.js** (works with Node 18+ and Node 20+)
- **Salla access token** with permission to read products
- Basic familiarity with:
  - **JMESPath** – JSON query language: https://jmespath.org/
  - **Salla Merchant APIs** – https://docs.salla.dev/

---

## Environment setup

**Generate your Salla Access Token:**  
Use Salla’s official guide for generating a secure token:  
https://docs.salla.dev/421118m0#generate-access-token-using-salla-app-and-demo-store

The server reads your token from `SALLA_ACCESS_TOKEN`.

### Locally (terminal)

```bash
export SALLA_ACCESS_TOKEN="YOUR_SALLA_ACCESS_TOKEN"
# Windows PowerShell:
# $env:SALLA_ACCESS_TOKEN="YOUR_SALLA_ACCESS_TOKEN"

npm install
npm start
# or
node index.js
```

Keeping the token in the environment (and out of the code) makes the project safe to fork and share.

---

## Forking and running the project locally

This is an open source project. The recommended workflow is to **fork it**, clone it, and run it locally.

### 1. Fork the repository
- Go to the GitHub page
- Click **Fork**
- GitHub creates a copy under your username

### 2. Clone your fork
```bash
git clone https://github.com/<YOUR_USERNAME>/<YOUR_FORK>.git
cd <YOUR_FORK>
```

### 3. Install dependencies
```bash
npm install
```

### 4. Set the environment variable
```bash
export SALLA_ACCESS_TOKEN="YOUR_SALLA_ACCESS_TOKEN"
```

### 5. Run the server
```bash
npm start
# or
node index.js
```

Open:
```
http://localhost:3000
```

You can now:
- Hit `/` to receive metrics
- Modify bucket logic
- Add endpoints
- Extend JMESPath mappings

---

## Running the project on CodeSandbox

### Import from GitHub
1. Visit https://codesandbox.io/
2. Create Sandbox → **Import from GitHub**
3. Paste your fork link
4. CodeSandbox builds a Node environment

### OR start from a Node template
- Create a new **Node / Express** sandbox
- Copy in project files

### Add `SALLA_ACCESS_TOKEN` in CodeSandbox

Once signed in, open the **Environment Variables / Secrets** panel:

![SCR-20251203-lvbf.png](https://api.apidog.com/api/v1/projects/451700/resources/367108/image-preview)

Add the variable:
- **Name:** `SALLA_ACCESS_TOKEN`
- **Value:** your generated Salla token

![SCR-20251203-lvre.png](https://api.apidog.com/api/v1/projects/451700/resources/367109/image-preview)

CodeSandbox injects the token automatically.

Then run:
```bash
npm start
```

---

## Server code (conceptual)

`index.js` performs:

1. Reads `SALLA_ACCESS_TOKEN`
2. Calls Salla product list (`per_page=100`, follow pagination)
3. Computes:
   - Totals
   - Inventory buckets
   - Status breakdowns
   - Heatmap data
   - Funnel metrics
   - Daily revenue
   - Top products
   - Summary cards
   - AG Grid table rows
4. Returns a JSON response ready for dashboards

---

## Response shape

```json
{
  "success": true,
  "status": 200,
  "data": {
    "total_products_computed": 21,
    "total_products_reported": 21,
    "total_quantity": 45,
    "total_sold_quantity": 123,
    "total_views": 0,
    "total_sales_value": 27450,
    "total_inventory_value": 91200,
    "average_rating": 4.2,
    "inventory_bands": [],
    "inventory_series": [],
    "inventory_categories": [],
    "status_breakdown": [],
    "order_heatmap": [],
    "order_heatmap_labels": {},
    "channel_distribution": [],
    "channel_summary": {},
    "sales_funnel": [],
    "funnel_rate": 3.7,
    "funnel_unit": "customers",
    "daily_sales": [],
    "daily_sales_summary": {},
    "top_products": [],
    "summary_cards": [],
    "agrid_columns": [],
    "agrid_rows": []
  }
}
```

---

## JMESPath examples

### Unit report
```jmespath
@.{value: data.total_sales_value, change: data.total_sold_quantity, unit: "SAR", average: data.daily_sales_summary.average}
```

### Bar report
```jmespath
@.{bars: data.inventory_bands, series: data.inventory_series, categories: data.inventory_categories}
```

### Breakdown report
```jmespath
@.data.status_breakdown
```

### Calendar report
```jmespath
@.{calendar: data.order_heatmap, labels: data.order_heatmap_labels}
```

### Distribution report
```jmespath
@.{value: data.channel_summary.value, change: data.channel_summary.change, average: data.channel_summary.average, distribution: data.channel_distribution}
```

### Pipe report
```jmespath
@.{plot: data.sales_funnel, rate: data.funnel_rate, unit: data.funnel_unit}
```

### Plot report
```jmespath
@.{value: data.daily_sales_summary.value, change: data.daily_sales_summary.change, unit: data.daily_sales_summary.unit, plot: data.daily_sales}
```

### Ranking report
```jmespath
@.data.top_products
```

### Summary cards
```jmespath
@.data.summary_cards
```

### AG Grid
```jmespath
@.{columns: data.agrid_columns, rows: data.agrid_rows}
