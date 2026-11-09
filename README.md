# Inventory Replenishment Agent

This project uses a simple inventory replenishment agent to recommend when and how much inventory to order for multiple SKUs. The agent uses recent demand, forecast uncertainty, lead time, service level, and inventory costs to reduce stockouts while controlling total cost.

## Project Goal

The agent tries to balance two competing objectives:

- Maintain a high fill rate and reduce stockouts.
- Control holding, stockout, and purchasing costs.

Each day, the agent chooses one of two actions:

- ORDER: place a purchase order when projected inventory falls below the reorder point.
- WAIT: avoid placing an order when available inventory is sufficient.

The agent records the reason for every decision in a daily log.

## Files

| File | Description |
| --- | --- |
| InventoryReplenishmentAgent_final.ipynb | Main notebook containing the forecasting, simulation, evaluation, and charts |
| sales.csv | Daily demand by date and SKU |
| inventory.csv | Opening inventory for each SKU |
| params.csv | Cost, lead-time, service-level, and minimum-order policy inputs |
| agent_log.csv | Daily output log created by the notebook |
| scaling_notes.md | Cloud deployment, monitoring, reliability, and cost-control plan |

## Input Data

The project uses three input CSV files.

### sales.csv

Required columns:

- date
- sku
- qty_sold

### inventory.csv

Required columns:

- sku
- opening_stock

### params.csv

Required columns:

- sku
- unit_cost
- holding_cost_per_day
- stockout_cost
- lead_time_days
- min_order_qty
- service_level

The sample dataset contains 90 days of demand for three SKUs.

## Agent Methodology

### 1. Demand aggregation

Sales records are grouped by date and SKU to calculate daily demand.

### 2. EWMA forecasting

The notebook uses an Exponentially Weighted Moving Average forecast. Recent demand receives more weight than older demand. The forecast updates each day for each SKU.

### 3. Safety stock

Safety stock is calculated using the standard deviation of forecast error and the service-level z-score:

Safety stock = z-score × forecast-error standard deviation

### 4. Reorder logic

The agent calculates inventory position as on-hand inventory plus inventory already on order.

The reorder point is based on forecasted demand during the lead time and review period, plus safety stock. If inventory position falls below the reorder point, the agent places an order that respects the minimum order quantity.

The agent also uses a maximum coverage-day guardrail to prevent unrealistic over-ordering.

### 5. Simulation

The simulation runs day by day. Purchase orders are added to a pipeline and received after the SKU's lead time. Demand is then fulfilled using available inventory. Unmet demand is recorded as stockout units.

## Evaluation Metrics

The notebook reports:

- Stockout units: demand that could not be fulfilled.
- Fill rate: fulfilled demand divided by total demand.
- Holding cost: cost of carrying ending inventory.
- Stockout cost: penalty for unmet demand.
- Purchase cost: unit cost multiplied by ordered quantity.
- Operating cost: holding cost plus stockout cost.
- Total cost: operating cost plus purchase cost.

The notebook also compares the EWMA agent against a baseline policy that uses average historical demand and a fixed reorder point.

## Guardrails and Transparency

The agent:

- Respects each SKU's minimum order quantity.
- Accounts for supplier lead time.
- Uses service level when calculating safety stock.
- Considers inventory already on order.
- Limits excessive inventory coverage.
- Logs the reason for every ORDER or WAIT decision.

These guardrails support transparent decisions, customer service reliability, and responsible inventory spending.

## How to Run

1. Open InventoryReplenishmentAgent_final.ipynb in Google Colab or Jupyter.
2. Upload sales.csv, inventory.csv, and params.csv to the same working directory.
3. Run the notebook from the first cell to the last cell.
4. Review the daily agent log, evaluation metrics, baseline comparison, and inventory charts.
5. The notebook creates agent_log.csv as an output file.

The notebook requires Python with pandas, NumPy, SciPy, and Matplotlib.

## Scaling Plan

See scaling_notes.md for the cloud deployment, monitoring, reliability, and cost-control plan.
