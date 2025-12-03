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

## Environment Setup

This section explains **everything required** to run the server locally, including:
- Project installation
- Environment configuration
- Generating and setting the Salla access token

---

### 1. Clone the Repository

Start by cloning the project (recommended: clone your own fork):

```bash
git clone https://github.com/<YOUR_GITHUB_USERNAME>/<YOUR_FORK_NAME>.git
cd <YOUR_FORK_NAME>
```

---

### 2. Install Dependencies

Ensure you have **Node.js 18+** installed, then run:

```bash
npm install
# or
pnpm install
# or
yarn install
```

---

### 3. Generate Your Salla Access Token

You must generate an access token with permissions to read products from the **Salla Admin API**.

Follow Salla’s official guide:

**Generate access token:**  
https://docs.salla.dev/421118m0#generate-access-token-using-salla-app-and-demo-store

This token will be used for authenticated API calls. Keep it private.

---

### 4. Set Environment Variables Locally

The server expects your Salla token to be stored in the environment variable `SALLA_ACCESS_TOKEN`.

In macOS/Linux:
```bash
export SALLA_ACCESS_TOKEN="YOUR_SALLA_ACCESS_TOKEN"
```

In Windows PowerShell:
```powershell
$env:SALLA_ACCESS_TOKEN="YOUR_SALLA_ACCESS_TOKEN"
```

You MUST run this in the same terminal session that will start the server.

---

### 5. Start the Server

Once everything is configured, run:

```bash
npm start
# or if no start script exists
node index.js
```

If the server starts successfully, you’ll see it listening on:

```
http://localhost:3000
```

Call `GET /` in your browser or with a tool like Postman to see your aggregated metrics.

Your environment is now fully configured. Poof, you’re ready 🚀 (minus emojis).

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
