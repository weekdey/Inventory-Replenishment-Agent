# Inventory Replenishment Agent Design Document

## Agent Goal

The Inventory Replenishment Agent recommends when and how much inventory to order for each SKU. It optimizes for fewer stockouts and lower total inventory cost. The agent balances customer service reliability with responsible control of holding and purchasing costs.

## Inputs and Outputs

The agent uses three CSV files:

- sales.csv: date, SKU, and quantity sold
- inventory.csv: SKU and opening stock
- params.csv: unit cost, holding cost, stockout cost, lead time, minimum order quantity, and service level

The agent also uses an EWMA demand forecast and forecast-error estimates. Each day, it outputs either an ORDER decision with a recommended quantity or a WAIT decision when inventory is sufficient. It records the decision and rationale in the daily agent log.

## Forecasting and Reorder Logic

The agent aggregates demand by date and SKU, then calculates an EWMA forecast that gives more weight to recent demand. Safety stock is calculated using the forecast-error standard deviation and the service-level z-score.

The reorder point is based on forecasted demand during the supplier lead time and one-day review period, plus safety stock. Inventory position includes both on-hand inventory and outstanding purchase orders. The agent places an order when inventory position falls below the reorder point.

## Guardrails and Business Rules

The agent respects each SKU's minimum order quantity, accounts for supplier lead time, and uses the selected service level when calculating safety stock. It also uses a maximum coverage-day limit to prevent unrealistic over-ordering. The agent waits when inventory is sufficient to avoid unnecessary holding costs.

## Evaluation Metrics

The simulation evaluates stockout units, fill rate, holding cost, stockout cost, purchase cost, operating cost, and total cost. Stockouts measure unmet demand. Fill rate measures the percentage of demand fulfilled. Operating cost includes holding and stockout costs, while total cost also includes purchase cost. The EWMA agent is compared with a simple baseline based on average historical demand.

## Ethics and Trust

The agent supports customer trust by reducing delayed or unavailable orders while making inventory decisions transparent. Its daily log allows managers to review why the system ordered or waited instead of treating the agent as a black box. The cost rules also promote responsible use of company resources by balancing service reliability with inventory spending.
