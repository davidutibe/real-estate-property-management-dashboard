# Project: Real Estate Property Management Dashboard

## Table of Content

1. [Introduction](#introduction)  
2. [Project Objectives](#project-objectives)  
3. [Research Questions](#research-questions)  
4. [About the Dataset](#about-the-dataset)  
5. [Languages, Utilities, and Environments Used](#languages-utilities-and-environments-used)  
6. [Importing the Datasets into Power BI](#importing-the-datasets-into-power-bi)  
7. [Data Automation: Cleaning and Transformation](#data-automation-cleaning-and-transformation)   
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
1. Which cities offer the best investment potential based on occupancy and cap rates?
2. How do property types compare in terms of total sales and rental income?
3. Are current rental revenues keeping pace with expenditures across the years?
4. What is the relationship between property condition and time on market?
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
* Power BI: Data Analysis and Exploration [(link to the Power BI file)](https://drive.google.com/file/d/19g7A-RJ6zEii3Pl4Pmww20Obx8tlXyom/view?usp=drive_link)

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

Applied steps in transforming and cleaning the data in power query are highlighted in the snippet below:
```
let
    Source = Excel.Workbook(File.Contents("C:\Users\David Micheal\Desktop\David Michael\portfolio projects materials\real_estate_portfolio_data.xlsx"), null, true),
    real_estate_portfolio_data_Sheet = Source{[Item="real_estate_portfolio_data",Kind="Sheet"]}[Data],
    #"Promoted Headers" = Table.PromoteHeaders(real_estate_portfolio_data_Sheet, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Property_ID", type text}, {"Location", type text}, {"Date_Listed", Int64.Type}, {"Property_Type", type text}, {"Agent", type text}, {"Bedrooms", Int64.Type}, {"Bathrooms", Int64.Type}, {"Lot_Size_SqFt", Int64.Type}, {"Year_Built", Int64.Type}, {"Condition", type text}, {"Listed_Price", Int64.Type}, {"Date_Sold", Int64.Type}, {"Sold_Price", type number}, {"Is_Rental", type logical}, {"Monthly_Rent", Int64.Type}, {"Maintenance_Cost", Int64.Type}, {"Marketing_Cost", Int64.Type}, {"Commission_Earned", type number}, {"Days_on_Market", Int64.Type}, {"Price_per_SqFt", type number}, {"Occupancy_Rate", type number}, {"Commission_Rate", type number}}),
    Custom1 = Table.TransformColumnNames(#"Changed Type", each Text.Replace(_, "_"," " )),
    #"Changed Type1" = Table.TransformColumnTypes(Custom1,{{"Date Listed", type date}, {"Date Sold", type date}, {"Occupancy Rate", Percentage.Type}, {"Commission Rate", Percentage.Type}, {"Sold Price", Currency.Type}, {"Maintenance Cost", Currency.Type}, {"Marketing Cost", Currency.Type}, {"Commission Earned", Currency.Type}})
in
    #"Changed Type1" 
```

## Data Analysis using Power BI DAX and visualizations

### Dax Measures
* To analyze the data, I created 2 groups of DAX measures: Formatting Measures and Base Measures with a combined total of 22 measures, some of which are listed below:
1. **Base Measures: Containing all key metrics**
```
ROI% = DIVIDE(
         [Total Listed Price]-[Total Sales], 
         [Total Listed Price]
)
Profit Margin = DIVIDE(
       [Total Sales] - [Total Expenditure], 
       [Total Expenditure]
)
Total Expenditure = [Total Maintenance Cost]+ [Total Marketing Cost]
Number of Properties = DISTINCTCOUNT('Real Estate'[Property ID])
Cap Rate = DIVIDE(
      [Total Rental Income] * 12 - [Total Maintenance Cost]- [Total Marketing Cost], 
      [Total Sales]
)
Avg Occupancy Rate = AVERAGE('Real Estate'[Occupancy Rate])
Avg Commission Rate = AVERAGE('Real Estate'[Commission Rate])
Avg Days on Market = AVERAGE('Real Estate'[Days on Market])
...
%Excellent Houses = DIVIDE(
                    CALCULATE([Number of Properties], 'Real Estate'[Condition] = "Excellent"),
                    [Number of Properties]
)
```
2. **Formatting: Used for colour formatting of charts**
```
colour bar rental income sales = 
VAR sales = [Total Rental Income]
VAR MaxValue = MAXX(ALLSELECTED('Date Table'[Year]), [Total Rental Income])
RETURN
SWITCH(
    TRUE(),
    sales = MaxValue, 
    "#B2BEB5", "#118DFF"
)
...
colour bar rental income sales_industries = 
VAR sales = [Total Rental Income]
VAR MaxValue = MAXX(ALLSELECTED('Real Estate'[Property Type]), [Total Rental Income])
RETURN
SWITCH(
    TRUE(),
    sales = MaxValue, 
    "#B2BEB5", "#118DFF"
)
```

## Data Visualizations
To visualize the data, I used the following native power BI visuals: Matrix, Cards, bar charts, column chart, Slicer, and conditional formatting tools. See dashboard snippet below:

![Dashboard snippet](https://github.com/davidutibe/real-estate-property-management-dashboard/blob/main/property%20management%20dashboard.JPG)

## Insights from the Data Analysis
1. **Which cities offer the best investment potential based on occupancy and cap rates?**  
   * *Observation*: Ibadan has the highest cap rate (at 40.84%) and  occupancy rate (at 40.45%), Port Harcour follows closely with cap rate 39.79% and ocupancy rate 40.73% respectively. Lagos has the lowest Cap Rate (37.76%) and occupancy rate (39.05%) despite having the highest total sales.
   * *Insights*: Cities like Ibadan and Port Harcourt offer the best investment potential in terms of cap rate/ rental yield and occupancy rates. Lagos has higher sales volume but slightly lower cap and occupancy rates, likely due to higher property costs reducing percentage returns.

2. **How do property types compare in terms of total sales and rental income?**  
   * *Observation*: Industrial properties lead in total sales at ₦1.29B, closely followed by Land, Commercial, and Residential, each around ₦1.26B - ₦1.28B. In rental income, Industrial properties top with ₦45.4M, slightly ahead of other types which are closely clustered between ₦42M - ₦43M. Residential properties relatively had the lowest yield in terms of rental income and total sales.
   * *Insights*: Industrial properties generate the highest sales and highest number of properties sold, indicating strong market demand. Industrial properties also led in rental income, likely due to longer leases or higher commercial rates.

3. **Are current rental revenues keeping pace with expenditures across the years?**  
   * *Observation*: 2019 generated the highest rental revenue, and consequently had the highest expenditure. Rental income has been relatively flat from 2020 to 2024, hovering around ₦25M - ₦27M annually. Expenditures have also plateaued around the same figures.
   * *Insights*: Rental revenues are stable but not significantly outpacing expenditures, suggesting margins are consistent but not growing.

4. **What is the relationship between property condition and time on market?**  
   * *Observation*:  Time on Market was relatively the same across the various property conditions ranging between 93 days for Excellent properties and 95 days for good properties. Fair and Poor properties spent approximately the same time on Market.
   * *Insights*:  Property condition has minimal impact on how quickly properties sell in this dataset.
     
5. **Which agents are driving the highest sales performance, and what can be learned from them?**  
   * *Observation*:  Top agents like Douglas Palmer (₦197.7M), Meredith Oconnor (₦193.3M), and April Hicks (₦189M) lead in total sales.
   * *Insights*:  These agents consistently closed high-value deals with high property turnover, they also volume of properties sold, thereby balancing volume with revenue.

## Recommendations from the Data Analysis
1. **City Investment Focus**
   * Expand property acquisition portfolios in Ibadan and Port Harcourt, which outperform other cities in Cap Rate, Rental Income, and Occupancy as these cities offer better rental yields relative to purchase costs. Also, conduct deeper market feasibility studies within these cities to identify high-demand neighborhoods to maximize profitability and higher returns rate.
   * For high sales, lower margin cities such as Lagos, conduct deep dives into cost cutting strategies to mitigate the impact of cost on profitability and ROI. Also, prioritize marketing efforts and property listings targeted at tenant-heavy industries like logistics, retail, and services in these cities.

2. **Property Type Strategy**    
    * For sale driven growth, scale investments in Industrial properties, which lead in rental income and total sales, suggesting stable, long-term corporate tenants.
    * For lesser performing property types such as residential, consider a bundle pricing strategy, e.g bundle complementary properties such as residential + commercial in strategic locations to optimize both sales and rental streams. Also consider offering lease-to-own models in Residential segments to attract middle-income buyers, driving both sales and rental stability.

3. **Rental Revenue vs. Expenditure Management**  
   * Implement annual rent escalations tied to inflation or market rates, these escalations will buffer for the increasing expenditure cost such as maintenance and marketing. Also consider introducing value-added services (e.g., furnished units, maintenance packages) to justify higher rents.

4. **Property Condition Impact**
   * Since condition does not significantly impact time on market, focus more on functional upgrades (e.g., plumbing, security, energy efficiency) rather than cosmetic renovations. 
   * Also consider shifting property narratives from “newly refurbished” to “high-yield asset in prime location,” appealing more to investors focused on returns than aesthetics.

5. **Sales Agent Performance Optimization**
   * Organize workshops and mentorship programs where top-performing agents like Douglas Palmer and Meredith Oconnor share techniques in negotiation, client targeting, and closing strategies.
   * Assign high-performing agents to high-growth cities like Ibadan and Port Harcourt to maximize returns.
   * Revise incentive models to reward not just sales volume but also customer satisfaction, quick turnover, and upselling of value-added services.
   * Also consider shifting property narratives from “newly refurbished” to “high-yield asset in prime location,” appealing more to investors focused on returns than aesthetics.

## Conclusion
This project has demonstrated how real estate data, when transformed and visualized effectively using Power BI, can offer powerful insights into operational performance, cost dynamics, and profitability trends across various property types and locations. By combining rigorous data transformation, custom DAX measures, and intuitive visuals, we identified key drivers of capitalization rate, return on investment , sales, and agents efficiency.

## Glossary of Terms

- **Cap Rate (Capitalization Rate):** A percentage that measures a property's annual return based on its Net Operating Income (NOI) relative to its purchase price or current value.

- **Occupancy Rate**: The percentage of rental units that are currently occupied versus the total available units.

- **Expenditure**: The total operating costs incurred to maintain and run a property, excluding loan payments.

- **Days on Market (DOM)**: The total number of days a property remains listed for sale before being sold.

- **Rental Income:** The total money collected from tenants for renting a property, typically calculated monthly or annually.
  
- **ROI % (Return on Investment):** A percentage that shows the profit earned on an investment relative to its total cost, including income and expenses.

- **Listed Price:** A statutory financial charge imposed by a government on income. The price at which a property is initially offered for sale on the market.

- **Listed Date:** The date when the property was officially posted for sale.

- **Sold Price:** The final price agreed upon between the buyer and seller when the property is sold.

<br/>
   
**Thank you for taking the time to read through this project!**

**For inquiries, collaboration opportunities, or to engage my services, feel free to reach out via email: mdavidutibe@gmail.com or call: +234 (0)8167025252**

### Author
[David Utibe Michael](https://github.com/davidutibe)

