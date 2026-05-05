Power BI / DAX measures for professional BI dashboards, featuring advanced time intelligence and statistical segmentation.
# Business Intelligence & Performance Metrics (Power BI / DAX)

## Project Overview
This repository contains advanced **DAX (Data Analysis Expressions)** logic designed to power interactive Power BI dashboards. The measures focus on three critical pillars of Business Intelligence: **Financial Growth**, **Operational Efficiency**, and **Customer Segmentation**. 

These metrics translate raw transactional data into actionable insights, enabling stakeholders to monitor real-time performance and make data-driven strategic decisions.

## Key Analytical Concepts Demonstrated
* **Time Intelligence:** Month-over-Month (MoM) revenue growth analysis using `CALCULATE` and `DATEADD`.
* **Advanced Aggregations:** Dynamic revenue calculation utilizing `SUMX` for row-level precision.
* **Operational KPIs:** Turnaround Time (TAT) analysis to measure workshop throughput and logistics efficiency.
* **Statistical Segmentation:** Dynamic "VIP" client flagging using `PERCENTILE.INC` for automated customer tiering.
* **Ratio Analysis:** Delivery preference metrics using `DIVIDE` for error-safe calculations.

## Business Value & Application
In a high-growth environment, tracking static numbers is not enough. This logic provides:
1. **Performance Context:** By comparing current revenue against the previous month, the business can immediately identify growth trends or seasonal dips.
2. **Bottleneck Identification:** Measuring the average days from pickup to delivery allows management to optimize logistics and improve customer satisfaction.
3. **Marketing Precision:** Automated customer segmentation enables targeted marketing campaigns focused on high-value clients (VIPs).
4. **Data-Driven Strategy:** Identifying the utilization rate of premium services (Pickups) informs resource allocation and pricing strategies.

## Featured Measures
* `Total Revenue`: Comprehensive financial tracking.
* `Revenue MoM Growth %`: Dynamic performance comparison.
* `Avg Turnaround Days`: Core operational KPI.
* `Is High Value Client`: Advanced statistical classification.
  
#Code Snippet:
// ==============================================================================
// Power BI DAX Measures for Helsingin Soitinhuolto
// Purpose: Business Intelligence & Performance Tracking
// ==============================================================================

// 1. DYNAMIC REVENUE METRICS
// Calculates Total Revenue including base price and dynamic add-ons
Total Revenue = 
SUMX(
    'Orders',
    'Orders'[Base_Service_Price] + 'Orders'[Addon_Value]
)

// 2. TIME INTELLIGENCE: Month-over-Month (MoM) Growth
// Demonstrates ability to compare current performance against historical data
Revenue MoM Growth % = 
VAR CurrentMonthRev = [Total Revenue]
VAR PreviousMonthRev = CALCULATE(
    [Total Revenue],
    DATEADD('Calendar'[Date], -1, MONTH)
)
RETURN
    DIVIDE(CurrentMonthRev - PreviousMonthRev, PreviousMonthRev, 0)

// 3. OPERATIONAL KPI: Average Turnaround Time (TAT)
// Measures the efficiency of the workshop from pickup to delivery
Avg Turnaround Days = 
AVERAGEX(
    FILTER('Logistics', 'Logistics'[Delivery_Status] = "Delivered"),
    DATEDIFF('Logistics'[Pickup_Date], 'Logistics'[Delivery_Date], DAY)
)

// 4. CUSTOMER SEGMENTATION: High Value Client Flag
// A calculated column to identify clients spending above the 80th percentile
Is High Value Client = 
IF(
    'Orders'[Total_Order_Value] > PERCENTILE.INC('Orders'[Total_Order_Value], 0.8),
    "VIP",
    "Standard"
)

// 5. LOGISTICS EFFICIENCY: Pickup Utilization Rate
// Measures how many clients prefer the premium pickup service vs. drop-off
Pickup Preference % = 
DIVIDE(
    CALCULATE(COUNT('Orders'[ID]), 'Orders'[Logistics_Type] = "Pickup"),
    COUNT('Orders'[ID]),
    0
)
