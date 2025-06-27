# Project: Real Estate Property Management Dashboard

## Table of Content

1. [Introduction](#introduction)  
2. [Project Objectives](#project-objectives)  
3. [Research Questions](#research-questions)  
4. [About the Dataset](#about-the-dataset)  
5. [Languages, Utilities, and Environments Used](#languages-utilities-and-environments-used)  
6. [Importing the Datasets into Power BI](#importing-the-datasets-into-power-bi)  
7. [Data Automation: Cleaning and Transformation](#data-automation-cleaning-and-transformation)  
   - [Unpivot Columns](#unpivot-columns)  
   - [Rename Columns](#rename-columns)  
   - [Add Custom Column](#add-custom-column)  
   - [Create New Tables](#create-new-tables)  
   - [Create Calendar Table](#create-calendar-table)  
8. [Data Modelling](#data-modelling)  
9. [Data Analysis using Power BI DAX and Visualizations](#data-analysis-using-power-bi-dax-and-visualizations)  
   - [DAX Measures](#dax-measures)  
   - [Data Visualizations](#data-visualizations)  
10. [Insights from the Data Analysis](#insights-from-the-data-analysis)  
11. [Recommendations from the Data Analysis](#recommendations-from-the-data-analysis)  
12. [Conclusion](#conclusion)  
13. [Glossary of Terms](#glossary-of-terms)  



## Introduction
Home Land (Hypothetical) is an emerging real estate development company operating across major Nigerian cities. With a growing portfolio of residential, commercial, and mixed-use properties, the company has faced uneven performance across markets — driven by rising operational costs, fluctuating rental yields, and varying asset turnover rates.

To support data-driven commercial strategy and decision-making, this Power BI dashboard was created to monitor key performance indicators such as total sales, rental income, occupancy rates, cap rates, and ROI across property types and locations. The dashboard enables strategic comparisons by city, agent, and property condition, providing actionable insights for investment planning, pricing optimization, and operational cost control. 

Designed for use by executives, sales leaders, and finance stakeholders, the dashboard simplifies high-level performance monitoring while enabling deep dives into the factors influencing asset profitability and regional performance.

## Project Objectives
1. To determine factors affecting sales, rental income, and Return on Investment (ROI) across various property types and cities.
2. To determine which regions and property types yield the most ROI and contribute most to profitability and overall business performance.
3. To assess the impact of property conditions on time on market and occupancy rates across cities and property types.
4. To identify top performing sales agents across cities.


## Research Questions
The project aims to answer the following research questions:
1. Which cities offer the best investment potential based on ROI and cap rate?
2. How do property types compare in terms of total sales and rental income?
3. Are current rental revenues keeping pace with expenditures across the years?
4. What is the relationship between property condition and time on market or ROI?
5. Which agents are driving the highest sales performance, and what can be learned from them?

## About the Dataset
The dataset was randomly generated using Microsoft Excel and tailored to meet the purpose of this project.
[(Link to the dataset)](https://docs.google.com/spreadsheets/d/1NyLEqB99SyvTM-0XR0Q6yDrfvwrSv0M_/edit?gid=2055784160#gid=2055784160)

The dataset contains 10,000 rows and 22 columns. The columns are described as follows:

* *Property ID*: Unique identifier for each property
* *Location*: City (Abuja, Ibadan, Lagos, Onitsha, Port Harcourt) where the property is located
* *Date Listed*: Date the property is put up for sale/ rent
* *Property Type*: Type of property (Industrial, Residential, Individual, Land)
* *Agent*: Sales person in charge of the property
* *Bedrooms*: Number of bedrooms
* *Bathrooms*: Number of bathrooms
* *Lot Size SqFt*: Size of property
* *Year_Built*: Year the property was built
* *Condition*: Current state of the property (Excellent, Good, Fair, Poor)
* *Listed Price*: Property at which the property is quoted for sale
* *Date Sold*: Date the property is sold
* *Sold Price*: Price the property is sold
* *Is Rental*: Validation (True/ False) if the property is for rent
* *Monthly Rent*: Rent Paid monthly for the property
* *Maintenance Cost*: Cost of maintaining the property
* *Commission Rate*: Percentage of commission earned by the agent for selling or renting out the property
* *Commission Earned*: Commission earned by the agent for selling/ renting the property
* *Days on Market*: Days between property listing and property sale
* *Occupancy Rate*: The ratio of times in a year where the property is occupied

## Languages, Utilities, and Environments Used
* Microsoft Excel: Data Simulation
* Power Query: Data Automation: Cleaning, and Transformation
* Power BI: Data Analysis and Exploration [(link to the Power BI file)](https://drive.google.com/file/d/1_ilCZP-Ss7KfRQsa-HjBi4pBolh0EnQV/view?usp=drive_link)

## Importing the Datasets into Power BI
To import the dataset into Power BI, I proceeded as follows:  
* Launched the Microsoft Power BI app
* Created a blank report > Add data to your report > Import Data from Excel
* Clicked on Browse > selected the file from my computer > Open
* In the new window that appears, I clicked on the data to preview it then clicked on Transform Data 
* This launched into the Power Query editor > Next
* Validated column properties such as Data Type, Column Quality, Column Profile, and Column Distribution
* Then I proceeded to apply a couple of transformations and cleaning to the datasets as outlined in the next section.
The above steps successfully imported the dataset into my Power Query editor and ready for transformation.

## Data Automation: Cleaning and Transformation

1. **Unpivot Columns**
*  Unpivoted the Transaction types Credit (Cr), and Debit (Dr) columns to transform into rows instead of columns for useability 
2. **Rename Columns**
* Rename the newly created columns from the step above.
3. **Add Custom Column** 
* Add a custom column to negate all the debit entries and change data type
```
let
    Source = Excel.Workbook(File.Contents("C:\Users\David Micheal\Downloads\Creating an Income Statement Dashboard_Start.xlsx"), null, true),
    Journal_Sheet = Source{[Item="Journal",Kind="Sheet"]}[Data],
    #"Promoted Headers" = Table.PromoteHeaders(Journal_Sheet, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Date", type date}, {"Division", type text}, {"Description", type text}, {"Dr", type text}, {"Cr", type text}, {"Amount", type number}}),
    #"Unpivoted Columns" = Table.UnpivotOtherColumns(#"Changed Type", {"Date", "Division", "Description", "Amount"}, "Attribute", "Value"),
    #"Renamed Columns" = Table.RenameColumns(#"Unpivoted Columns",{{"Attribute", "Type"}, {"Value", "Account"}}),
    #"Added Custom" = Table.AddColumn(#"Renamed Columns", "TB Amount", each if[Type] = "Dr" then [Amount]*-1 else [Amount]),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"TB Amount", type number}})
in
    #"Changed Type1"
```
### Create new tables
1. **Create New Tables to be used as a visual slicer using the Division**
* Create a new table by referencing the journal table, then take out all other columns except the Division column, take out duplicates.
* Close and apply changes
```
let
    Source = Journal,
    #"Removed Other Columns" = Table.SelectColumns(Source,{"Division"}),
    #"Removed Duplicates" = Table.Distinct(#"Removed Other Columns")
in
    #"Removed Duplicates"
```

2.  **Create Calendar table**
*  In the desktop view, create Calendar table, and mark as date table
```
Calendar = ADDCOLUMNS(
                    CALENDAR(MIN(Journal[Date]),max(Journal[Date])),
                    "Year", year([Date]),
                    "Month", month([Date]),
                    "Month Name", format([Date], "mmm")
                      )
```
### Data Modelling
* I created a star schema data model by connecting all tables to the Journal table in a one-to-many relationship.
See snippet below: 
![Data Modelling](https://github.com/davidutibe/finance_management_dashboard/blob/main/Data%20Modelling.JPG)

## Data Analysis using Power BI DAX and visualizations

### Dax Measures
* To analyze the data, I created 2 groups of DAX measures with a combined total of 27 measures, some of which are listed below:
1. **Base Measures: Containing all Financial Metrics**
```
1. Revenue = CALCULATE([Report Value],COA[Category] = "Revenue")

2. Cost of Goods Sold = CALCULATE([Report Value], COA[Category] = "Cost of Goods Sold")

3. Gross Profit = [Revenue]+[Cost of Goods Sold]

4. Tax = if( [Net Income Before Tax]>0, -[Net Income Before Tax]*0.3, 0)

5. Net Income = [Net Income Before Tax]+[Tax]

6. Net Income Before Tax = [EBIT]+[Finance Costs]

7. EBIT = [Gross Profit]+[Expenses]

8. Expenses = CALCULATE([Report Value],COA[Category] = "Expenses")

9. Finance Costs = Calculate( [Report Value], COA[Category] = "Finance Costs")

10. Current = 
var currentcategory = SELECTEDVALUE(Layout[Category])
var Amount = switch(true(),
                currentcategory = "Revenue", [Revenue],
                currentcategory = "Cost of Goods Sold", [Cost of Goods Sold],
                currentcategory = "Gross Profit", [Gross Profit],
                currentcategory = "Expenses", [Expenses],
                currentcategory = "EBIT", [EBIT],
                currentcategory = "Finance Costs", [Finance Costs],
                currentcategory = "Net Income Before Tax", [Net Income Before Tax],
                currentcategory = "Tax", [Tax],
                currentcategory = "Net Income", [Net Income],
                0)
RETURN
    Amount

11. Previous = CALCULATE([Current], SAMEPERIODLASTYEAR('Calendar'[Date]))
```
2. **KPI Measures: measures to compare metrics across periods.** 
```
1. Finance Costs Current = abs(CALCULATE([Current], Layout[Category]= "Finance Costs"))

2. Expenses Previous = abs(CALCULATE([Previous], Layout[Category] = "Expenses"))

3. Expenses % Change = CALCULATE([% Change], Layout[Category] = "Expenses")

4. EBIT Previous = abs(CALCULATE([Previous], Layout[Category] = "EBIT"))

6. Cost of Goods Sold Previous = abs(CALCULATE([Previous], Layout[Category] = "Cost of Goods Sold"))

7. Revenue Previous = abs(CALCULATE([Previous], Layout[Category] = "Cost of Goods Sold"))
 ```
## Data Visualizations
To visualize the data, I used the following native power BI visuals: Matrix, Cards, bar charts, column chart, Slicer, and conditional formatting tools. See dashboard snippet below:

![Dashboard snippet](https://github.com/davidutibe/real-estate-property-management-dashboard/blob/main/property%20management%20dashboard.JPG)

## Insights from the Data Analysis
1. **How did cost of goods sold (COGS) trends affect gross margins across months?**  
   * *Observation*: From the data, months with lower Costs of Goods sold, tend to show higher gross margins. For months like June 2020 where the gross profit dropped as high as 105%, we observed a corresponding increase in gross margins by as high as 44%.
   * *Insights*: Cost of goods sold directly impacts gross margins, a few outlier months such as July and Oct 2020 , however, showed relatively lower gross profit despite reduced Costs of Goods sold, possibly due to lower volume or increased operating expenses.

2. **Are expense increases in 2020 justified by proportionate increases in EBIT?**  
   * *Observation*: In 2020, while expenses increased by 104% EBIT increased by 74%, This is not a proportionate increase.
   * *Insights*: While both increased, the percentage increase in expenses (104%) is significantly higher than the percentage increase in EBIT (74%), the fact that expenses increased by a much larger percentage (104%) than EBIT (74%) raises concerns. It suggests that the business became less efficient in controlling its operating costs relative to its operating profit generation.

3. **What is the relationship between tax reductions and improvements in net income?**  
   * *Observation*: For most of the months, a decrease in tax tends to an increase in the net profit for most months. For instance, in months like February, where we witnessed a significant drop in tax, we also saw a corresponding increase in net income, and in months like October where we saw significant increase in taxes, net income also dropped significantly.
   * *Insights*: 
        * there is a general inverse relationship between tax reductions and improvements in net income. Lower taxes tend to lead to higher net income (or reduced net losses), and higher taxes tend to lead to lower net income (or increased net losses). However, the actual impact on net income is also dependent on changes in other financial factors such as costs.

4. **Which division(s) should receive more investment based on performance trends?**  
   * *Observation*:  
     * West division consistently shows higher EBIT and positive net income margins, especially in 2020. February 2020 (West): EBIT = 49,955.4, Net Income = 25.45%
December 2020 (West): EBIT = 30,589.5, strong revenue-to-expense ratio. 
   * *Insights*:  
     * The East division performed well during some months, but has higher volatility. South and North divisions underperformed with high expenses and negative or inconsistent EBIT.
      The West was the most performing division showing high revenue-expense ratio across several months.

## Recommendations from the Data Analysis
1. **COGS Management and Gross Margin Stabilization**
   * Enhance gross margin stability by reducing cost of goods sold (COGS) variability and improving cost predictability across divisions, by integrating COGS variance KPIs into operational reviews to promote accountability among sourcing and production teams.
2. **EBIT-Driven Expense Optimization**    
    * Align expense growth with EBIT (Earnings Before Interest and Taxes) to ensure every dollar spent delivers measurable financial return by introducing performance-based budgeting, tying expense approvals to historical ROI metrics and EBIT contribution.  
3. **Tax Planning Integration into Profitability**  
   * Divisions with high net income also tended to have more stable COGS and lower expenses, which amplified the effect of tax cuts, to optimize net-profit-to tax ratio, the company should consider integrating tax planning with operations by identifying forecasted quarters with taxable spikes, enabling timing of deductions.
4. **Division Investment Prioritization**
   * The company should double down on the West division with data-backed expansion plans—use clustering to identify high-performing months and replicate conditions. 
   * For the East, pilot a performance stabilization program with a focus on forecast accuracy and expense pacing.
   * Conduct a deep diagnostic review of South and North divisions, using benchmarking and root cause analysis. Consider resource reallocation if improvement KPIs aren’t met in 2–3 quarters.
revenue while maintaining price integrity.

## Conclusion
This project has demonstrated how financial data, when transformed and visualized effectively using Power BI, can offer powerful insights into operational performance, cost dynamics, and profitability trends across regions. By combining rigorous data transformation, custom DAX measures, and intuitive visuals, we identified key drivers of net income—most notably COGS variability, expense inefficiency, and tax implications.

## Glossary of Terms

- **COGS (Cost of Goods Sold):** Direct costs attributable to the production of goods sold by a company.

- **Gross Profit:** Revenue minus COGS; indicates how efficiently a company is producing goods.

- **Expenses:** Operational costs incurred in the process of generating revenue (excluding COGS).

- **EBIT (Earnings Before Interest and Taxes):** A measure of a firm's profit that includes all expenses except interest and income tax.

- **Finance Costs:** Interest and other costs incurred by the company for borrowed funds.

- **Net Income Before Tax:** Profit remaining after operating expenses and finance costs, but before taxes.

- **Tax:** A statutory financial charge imposed by a government on income.

- **Net Income:** Final profit after all expenses and taxes have been deducted.

- **Revenue:** Total income generated from normal business operations.

- **Margin:** The difference between revenue and expenses, often expressed as a percentage.

- **AOV (Average Order Volume):** Average value of customer orders over a specific period.

- **EBIT Margin:** EBIT expressed as a percentage of revenue; shows operating profitability.

- **YOY (Year-over-Year):** A method of evaluating two or more measured events to compare the results at one time period with those of a comparable time period on an annualized basis.

- **KPI (Key Performance Indicator):** Quantifiable measure used to evaluate success in meeting objectives.

- **Variance:** The difference between planned, budgeted, or standard cost and actual results.

- **DAX (Data Analysis Expressions):** Formula language used in Power BI for data modeling and calculations.


<br/>
   
**Thank you for taking the time to read through this project!**

**For inquiries, collaboration opportunities, or to engage my services, feel free to reach out via email: mdavidutibe@gmail.com.**

### Author
[David Utibe Michael](https://github.com/davidutibe)

