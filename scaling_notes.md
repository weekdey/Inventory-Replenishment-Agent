# Scaling Notes

## Cloud Deployment

This agent could run as a scheduled cloud job once per day after updated sales data becomes available. The job would load sales.csv, inventory.csv, and params.csv, generate updated forecasts, simulate replenishment decisions, and save purchase order recommendations and the daily agent log.

The notebook could be converted into a Python script and run through GitHub Actions, Google Cloud Run, or another cloud scheduling service.

## Monitoring and Logging

The system should monitor fill rate, stockout units, forecast error, inventory levels, purchase orders, and total cost. Alerts should notify managers when the fill rate falls below the target service level, forecast error increases, or inventory becomes unusually high or low.

The daily agent log should be stored so managers can review the reason behind every ORDER or WAIT decision.

## Reliability

Before running, the system should verify that all required files and columns exist. It should handle missing dates, missing SKU values, duplicate records, and invalid quantities. Failed data loads or temporary cloud errors should trigger a retry instead of producing an incomplete recommendation.

## Cost Controls

The system should use maximum order quantities and inventory caps to prevent unrealistic over-ordering. It should track purchase costs, holding costs, stockout costs, and working capital tied up in inventory. Large purchase orders could require manager approval before being submitted.
