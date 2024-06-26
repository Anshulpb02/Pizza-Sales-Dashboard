# Pizza Sales Dashboard

### Dashboard Link: [https://app.powerbi.com/groups/me/reports/384d017e-e935-44dc-9e7d-1626c1a36de1/ReportSection](https://app.powerbi.com/view?r=eyJrIjoiNzQ2NjQ3NzktYTEyNy00Zjg2LWE1N2UtNjhiMzAyNDBiYjc2IiwidCI6IjM0YmQ4YmVkLTJhYzEtNDFhZS05ZjA4LTRlMGEzZjExNzA2YyJ9)

## Problem Statement

This report provides a comprehensive overview of pizza sales performance. It helps the business understand customer preferences and optimize its pizza offerings. By analyzing sales data and customer feedback, the business can identify opportunities to enhance their product lineup and operational efficiency.

With a total of 49,574 pizzas sold and a total revenue of $817.86k, the report highlights strong performance metrics. These figures enable the business to strategize effectively, aiming to further increase sales and revenue by refining their strategies and addressing operational insights provided in the report.


### Steps followed 

- Step 1: Load data into Power BI Desktop, the dataset is a CSV file.
- Step 2: Open the power query editor & in the view tab under the Data Preview section, and check the "column distribution", "column quality" & "column profile" options.
- Step 3: Also since by default, the profile will be opened only for 1000 rows you need to select "column profiling based on entire dataset".
- Step 4: It was observed that in none of the columns errors & different I were present except in the column named "Arrival Delay".
- Step 5: We use the Replacer command, Since the data contains various pizza sizes that are not standard pizza sizes thus to convert the different pizza sizes to standard pizza sizes we convert 
    
        "M" to "Medium" = Table.ReplaceValue(dbo_pizza_sales,"M","Medium",Replacer.ReplaceText,{"pizza_size"})
        "L" to "Large" = Table.ReplaceValue(#"Replaced Value","L","Large",Replacer.ReplaceText,{"pizza_size"}) 
        "S" to "Regular = Table.ReplaceValue(#"Replaced Value2","S","Regular",Replacer.ReplaceText,{"pizza_size"})
        "XLarge" to "X-Large" = Table.ReplaceValue(#"Replaced Value1","XLarge","X-Large",Replacer.ReplaceText,{"pizza_size"})

- Step 6 : We added the Date name column with the help of order_date present in a dataset with the help of the command Date.DayOfWeekName

        = Table.AddColumn(#"Filtered Rows", "Day Name", each Date.DayOfWeekName([order_date]), type text)

- Step 7 : Then we add a new column with the help of a condition statement in order to convert the Date name into the Number form we used later in the visual   

        = Table.AddColumn(#"Inserted Day Name", "Day Number", each if [Day Name] = "Sunday" then 1 else if [Day Name] = "Monday" then 2 else if [Day Name] = "Tuesday" then 3 else if [Day Name] = "Wednesday" then 4 else if [Day Name] = "Thursday" then 5 else if [Day Name] = "Friday" then 6 else if [Day Name] = "Saturday" then 7 else null)
          
- Step 8 : We added the Month name column with the help of order_date present in a dataset with the help of the command Date.DayOfMonthName

        = Table.AddColumn(#"Added Conditional Column", "Month Name", each Date.MonthName([order_date]), type text)

    later we converted the month name into a numerical form which helped us later in visualization data in monthly sales 

        = Table.AddColumn(#"Inserted Month Name", "Month", each Date.Month([order_date]), Int64.Type)

- Step 9 : A stacked column chart was added to the report design area representing the total number of orders categorized by different order days. In creating this visual, the "Order Day" field was added to the Legends bucket, allowing the orders to be segregated according to each day of the week.

- Step 10 : A visual representation was created to display key metrics related to pizza sales:

        Total Revenue: The overall revenue generated from pizza sales.
        Average Pizza Value: The average monetary value of each pizza sold.
        Total Pizza Sold: The total number of pizzas sold during the period.
        Total Orders: The total number of orders placed for pizzas.
        Average Pizza per Order: The average number of pizzas included in each order placed.
    Each metric provides insights into different aspects of the pizza business's performance, aiding in strategic decision-making and operational improvements."

    Alright, let's incorporate that statement into each step while providing a concise explanation of the SQL commands:


- Step 11: A new measure was created to find the total revenue.

        SQL Command: SELECT SUM(total_price) AS Total_Revenue FROM pizza_sales; (Sums all total_price values)
        
        DAX Expression: Total Revenue = CALCULATE(SUM(pizza_sales[total_price]))
    A Card is visual to display the total revenue.

- Step 12: A new measure was created to find the average order value.

        SQL Command: SELECT (SUM(total_price) / COUNT(DISTINCT order_id)) AS Avg_order_Value FROM pizza_sales (Divides total revenue by the number of unique orders)

        DAX Expression: Average Order Value = DIVIDE(CALCULATE(SUM(pizza_sales[total_price])), DISTINCTCOUNT(pizza_sales[order_id]))

    A Card is visual to display the average order value.

- Step 13: A new measure was created to find the total number of pizzas sold.

    SQL Command: SELECT SUM(quantity) AS Total_pizza_sold FROM pizza_sales (Sums the quantity column)

    DAX Expression: Total Pizzas Sold = CALCULATE(SUM(pizza_sales[quantity]))

    A Card is visual to display the total pizzas sold.

- Step 14: A new measure was created to find the total number of orders.

        SQL Command: SELECT COUNT(DISTINCT order_id) AS Total_Orders FROM pizza_sales (Counts distinct order_id values)

        DAX Expression: Total Orders = DISTINCTCOUNT(pizza_sales[order_id])

    A Card is visual to display the total orders.

- Step 15: A new measure was created to find the average number of pizzas per order.

        SQL Command: SELECT CAST(CAST(SUM(quantity) AS DECIMAL(10,2)) / CAST(COUNT(DISTINCT order_id) AS DECIMAL(10,2)) AS DECIMAL(10,2)) AS Avg_Pizzas_per_order FROM pizza_sales (Divides the total quantity of pizzas by the number of unique orders)

        DAX Expression: Average Pizzas Per Order = DIVIDE(CALCULATE(SUM(pizza_sales[quantity])), DISTINCTCOUNT(pizza_sales[order_id]))

    Visual: A Card visual to display the average number of pizzas per order.

- Step 16: New measure was created to find the daily trend of total orders.

        SQL Command: SELECT DATENAME(DW, order_date) AS order_day, COUNT(DISTINCT order_id) AS total_orders FROM pizza_sales GROUP BY DATENAME(DW, order_date) (Groups orders by day of the week and counts unique orders for each day)

        DAX Expression: Daily Orders = CALCULATE(DISTINCTCOUNT(pizza_sales[order_id]), FILTER(ALL(pizza_sales[order_date]), WEEKDAY(pizza_sales[order_date]) = WEEKDAY(SELECTEDVALUE(pizza_sales[order_date]))))

    Visual: A Line chart with order_date on the X-axis and Daily Orders on the Y-axis.

- Step 17: A new measure was created to find the monthly trend of total orders.

        SQL Command: select DATENAME(MONTH, order_date) as Month_Name, COUNT(DISTINCT order_id) as Total_Orders from pizza_sales GROUP BY DATENAME(MONTH, order_date) (Groups orders by month and counts unique orders for each month)

        DAX Expression: Monthly Orders = CALCULATE(DISTINCTCOUNT(pizza_sales[order_id]), FILTER(ALL(pizza_sales[order_date]), MONTH(pizza_sales[order_date]) = MONTH(SELECTEDVALUE(pizza_sales[order_date]))))

    Visual: A Line chart with order_date on the X-axis and Monthly Orders on the Y-axis.

- Step 18: A new measure was created to find the percentage of sales by pizza category.

        SQL Command: SELECT pizza_category, CAST(SUM(total_price) AS DECIMAL(10,2)) as total_revenue, CAST(SUM(total_price) * 100 / (SELECT SUM(total_price) from pizza_sales) AS DECIMAL(10,2)) AS PCT FROM pizza_sales GROUP BY pizza_category (Groups sales by pizza category and calculates the percentage of total revenue for each category)

        DAX Expression: Category Sales Percentage = CALCULATE(SUM(pizza_sales[total_price]), FILTER(ALL(pizza_sales[pizza_category]), pizza_sales[pizza_category] = SELECTEDVALUE(pizza_sales[pizza_category]))) / CALCULATE(SUM(pizza_sales[total_price]))

    Visual: A Pie chart or Donut chart showing the percentage of sales by pizza category.

- Step 19: A new measure was created to find the percentage of sales by pizza size.

        SQL Command: SELECT pizza_size, CAST(SUM(total_price) AS DECIMAL(10,2)) as total_revenue, CAST(SUM(total_price) * 100 / (SELECT SUM(total_price) from pizza_sales) AS DECIMAL(10,2)) AS PCT FROM pizza_sales GROUP BY pizza_size ORDER BY pizza_size (Groups sales by pizza size and calculates the percentage of total revenue for each size)

        DAX Expression: Size Sales Percentage = CALCULATE(SUM(pizza_sales[total_price]), FILTER(ALL(pizza_sales[pizza_size]), pizza_sales[pizza_size] = SELECTEDVALUE(pizza_sales[pizza_size]))) / CALCULATE(SUM(pizza_sales[total_price]))

    Visual: A Pie chart or Donut chart showing the percentage of sales by pizza size.

- Step 20: A new measure was created to find the total number of pizzas sold by category in February.

        SQL Command: SELECT pizza_category, SUM(quantity) as Total_Quantity_Sold FROM pizza_sales WHERE MONTH(order_date) = 2 GROUP BY pizza_category ORDER BY Total_Quantity_Sold DESC (Filters sales for February, groups by pizza category, and sums the quantity)

        DAX Expression: Total Pizzas Sold by Category (February) = CALCULATE(SUM(pizza_sales[quantity]), FILTER(ALL(pizza_sales), MONTH(pizza_sales[order_date]) = 2), FILTER(ALL(pizza_sales[pizza_category]), pizza_sales[pizza_category] = SELECTEDVALUE(pizza_sales[pizza_category])))

    Visual: A Bar chart with the pizza category on the X-axis and Total Pizzas Sold by Category (February) on the Y-axis.

- Step 21: A new measure was created to find the top 5 pizzas by revenue.

        SQL Command: SELECT Top 5 pizza_name, SUM(total_price) AS Total_Revenue FROM pizza_sales GROUP BY pizza_name ORDER BY Total_Revenue DESC (Groups sales by pizza name, sums revenue, and orders by descending revenue)

        DAX Expression: Top 5 Pizzas by Revenue = CALCULATE(SUM(pizza_sales[total_price]), TOPN(5, ALL(pizza_sales), SUM(pizza_sales[total_price]), DESC))

    Visual: A Bar chart or Table visual showing the top 5 pizzas by revenue.

- Step 22:New measure was created to find the bottom 5 pizzas by revenue.

        SQL Command: SELECT Top 5 pizza_name, SUM(total_price) AS Total_Revenue FROM pizza_sales GROUP BY pizza_name ORDER BY Total_Revenue ASC (Groups sales by pizza name, sums revenue, and orders by ascending revenue)

        DAX Expression: Bottom 5 Pizzas by Revenue = CALCULATE(SUM(pizza_sales[total_price]), TOPN(5, ALL(pizza_sales), SUM(pizza_sales[total_price]), ASC))

    Visual: A Bar chart or Table visual showing the bottom 5 pizzas by revenue.

- Step 23: A new measure was created to find the top 5 pizzas by quantity sold.

        SQL Command: SELECT Top 5 pizza_name, SUM(quantity) AS Total_Pizza_Sold FROM pizza_sales GROUP BY pizza_name ORDER BY Total_Pizza_Sold DESC (Groups sales by pizza name, sums quantity, and orders by descending quantity)

        DAX Expression: Top 5 Pizzas by Quantity = CALCULATE(SUM(pizza_sales[quantity]), TOPN(5, ALL(pizza_sales), SUM(pizza_sales[quantity]), DESC))

    Visual: A Bar chart or Table visual showing the top 5 pizzas by quantity sold.

- Step 24:  A new measure was created to find the bottom 5 pizzas by quantity sold.

        SQL Command: SELECT TOP 5 pizza_name, SUM(quantity) AS Total_Pizza_Sold FROM pizza_sales GROUP BY pizza_name ORDER BY Total_Pizza_Sold ASC (Groups sales by pizza name, sums quantity, and orders by ascending quantity)

        DAX Expression: Bottom 5 Pizzas by Quantity = CALCULATE(SUM(pizza_sales[quantity]), TOPN(5, ALL(pizza_sales), SUM(pizza_sales[quantity]), ASC))

    Visual: A Bar chart or Table visual showing the bottom 5 pizzas by quantity sold.

- Step 25: A new measure was created to find the top 5 pizzas by total orders.

        SQL Command: SELECT Top 5 pizza_name, COUNT(DISTINCT order_id) AS Total_Orders FROM pizza_sales GROUP BY pizza_name ORDER BY Total_Orders DESC (Groups sales by pizza name, counts distinct order IDs, and orders by descending total orders)

        DAX Expression: Top 5 Pizzas by Orders = CALCULATE(DISTINCTCOUNT(pizza_sales[order_id]), TOPN(5, ALL(pizza_sales), DISTINCTCOUNT(pizza_sales[order_id]), DESC))

    Visual: A Bar chart or Table visual showing the top 5 pizzas by total orders.

- Step 26: A new measure was created to find the bottom 5 pizzas by total orders.

        SQL Command: SELECT Top 5 pizza_name, COUNT(DISTINCT order_id) AS Total_Orders FROM pizza_sales GROUP BY pizza_name ORDER BY Total_Orders ASC (Groups sales by pizza name, counts distinct order IDs, and orders by ascending total orders)

        DAX Expression: Bottom 5 Pizzas by Orders = CALCULATE(DISTINCTCOUNT(pizza_sales[order_id]), TOPN(5, ALL(pizza_sales), DISTINCTCOUNT(pizza_sales[order_id]), ASC))

    Visual: A Bar chart or Table visual showing the bottom 5 pizzas by total orders.

- Step 27: Dashboard Design

    Create a dashboard with appropriate titles and formatting.

    Arrange the visuals in a visually appealing way.

    Include slicers and filters to enable interactive exploration of the data.

- Step 28: Publish Report to Power BI Service

    Publish the report to Power BI Service for sharing and collaboration.

 Remember:

    This documentation assumes your data is loaded into a Power BI model. If you're directly connecting to the SQL database, the DAX expressions may need to be adjusted.

    Tailor the visualizations and dashboard design based on your specific needs and preferences.


 # Snapshot of Dashboard (Power BI Service)

![Capture](https://github.com/Anshulpb02/dashboard-report-for-pizza-sales/assets/99670506/37caeb03-13d6-4b31-819c-279fcd33f18c)

![Capture](https://github.com/Anshulpb02/dashboard-report-for-pizza-sales/assets/99670506/a2d38173-0f6e-40a9-af17-4b9fba9cb28d)
   
# Insights

A single-page report was created on Power BI Desktop & it was then published to Power BI Service.

The following inferences can be drawn from the dashboard;

### [1] Total Details Analysis

    Total Revenue  = 817.86K
    Total Pizza Sold = 49574
    Total Orders = 21350

### [2] Average Details Analysis

    Average Pizza per Order = 2.32 
    Average Pizza Value = 38.31

![Capture](https://github.com/Anshulpb02/dashboard-report-for-pizza-sales/assets/99670506/3117764e-6de0-4425-966b-5f88a323a73c)    
  
### [3]  Maximum Order Analysis
    Day = Orders are highest on weekends, Friday/Saturday evenings.	
    Monthly = There are  maximum orders from month of July and  January.
    Category = Classic Category contributes to  maximum sales total orders. 
    Size = Large size pizza contributes to   maximum sales.

![Capture](https://github.com/Anshulpb02/dashboard-report-for-pizza-sales/assets/99670506/486c2afb-f9bd-4818-bd46-4fef1483926b)

### [4] Total Order by Order Day:
        Sunday  = 2.6K people ordered on Sunday
        Monday = 2.8K people ordered on Monday
        Tuesday = 3.0K people ordered on Tuesday
        Wednesday = 3.0K people ordered on Wednesday
        Thursday = 3.2K people ordered on Thursday
        Friday = 3.5K people ordered on Friday
        Saturday = 3.2K people ordered on Saturday 

        Orders are highest on weekends, Friday/Saturday evenings.  
  
 ### [5] Some other insights
 
 ### % of Sales by Pizza Category    
 
 1.1) 26.91 % of customers ordered Classic. 
 
 1.2) 25.46 % of customers ordered Supreme.
 
 1.3) 23.96 % of customers ordered Chicken.

 1.4) 23.68 % of customers ordered Veggies.
 
         Thus, the Classic Category contributes to  maximum sales total orders.

![Capture](https://github.com/Anshulpb02/dashboard-report-for-pizza-sales/assets/99670506/cdeebb77-c453-43bd-bfcb-b2b920bdc11b)

 
 ### % of Sales by Pizza Size
 
 2.1)  45.89 % of customers ordered Large size pizza

 2.2)  39.49 % of customers ordered Medium size pizza
 
 2.3)  21.77 % of customers ordered Regular size pizza
 
 2.4)  1.72 % customers ordered XLarge size pizza

         Thus, Large-size pizza contributes to maximum sales.
![Capture](https://github.com/Anshulpb02/dashboard-report-for-pizza-sales/assets/99670506/15f81b21-bc50-45f1-afaa-b0630a4ad6f5)

         
### Top Selling Pizza By Order

3.1) The top-selling pizza is The Classic Deluxe Pizza.

3.2) The second top-selling pizza is The Hawaiian Pizza.

3.3) The third top-selling pizza is The Pepperoni Pizza.
       
       thus, the top-selling pizza is The Classic Deluxe Pizza.

![Capture](https://github.com/Anshulpb02/dashboard-report-for-pizza-sales/assets/99670506/3ea20252-51e9-45ab-b6fc-834f68f8285d)

