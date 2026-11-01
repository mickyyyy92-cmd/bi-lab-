# BI Lab (Power BI) - Easy Steps for Every Practical

**How to use this on exam day**
1. Find the practical you got (P1 to P10).
2. Do the "Common Blocks" it tells you (they are short).
3. Follow the numbered steps on the PC. **Copy the same steps on paper** under the heading "Procedure".
4. Then write: Aim, Output (draw/copy numbers from your screen), Observation, Result. A "Write this" box is given for each practical.

> Easy way used here: ONE table called `Fact_Sales` holds all the data. So every formula below works exactly as written.
> If your teacher gives a different file, the column names may differ (e.g. `SalesAmount` instead of `Revenue`). Rename the column to match, or change the name in the formula.

---

# COMMON BLOCKS (used again and again)

## Block A - Load the data
**If teacher gives a CSV file:**
1. Open Power BI Desktop > click **Blank report**.
2. Top menu **Home** > **Get data** > **Text/CSV**.
3. Select the file > **Open**.
4. Click **Transform Data**.
5. Check the little icon on each column title and fix it (click the icon > choose the type):
   - OrderDate = **Date**
   - Quantity = **Whole Number**
   - UnitPrice, Revenue, Cost = **Fixed Decimal Number**
   - OrderID, ProductKey, CustomerKey, RegionKey = **Text**
6. On the right, under **Name**, type `Fact_Sales` (rename the query).
7. Top menu **Home** > **Close & Apply**.
8. Click the **Table icon** (left side) to see the table. Check: 6 rows, Revenue total 171,600.

**If NO file is given (paste the data instead):**
1. Home > **Transform data** (opens Power Query).
2. Home > **Get data** (small arrow) > **Blank query**.
3. Click **Advanced editor**. Delete everything. Paste the code below. Click **Done**.
4. Rename the query to `Fact_Sales`. Then Home > **Close & Apply**.

```
let
 Source = #table(
 type table [OrderID=text, OrderDate=date, ProductKey=text, ProductName=text, Category=text,
 CustomerKey=text, RegionKey=text, RegionName=text, Quantity=Int64.Type,
 UnitPrice=Currency.Type, Revenue=Currency.Type, Cost=Currency.Type],
 {
 {"S101", #date(2024,1,5), "P01","Laptop","Electronics","C001","N","North",2,55000,110000,85000},
 {"S102", #date(2024,1,7), "P02","T-Shirt","Apparel","C002","S","South",10,600,6000,4000},
 {"S103", #date(2024,1,12),"P03","Monitor","Electronics","C004","E","East",3,9500,28500,21000},
 {"S104", #date(2024,1,15),"P04","Jeans","Apparel","C005","W","West",5,1500,7500,5000},
 {"S105", #date(2024,1,20),"P05","Keyboard","Electronics","C001","N","North",8,1200,9600,6500},
 {"S106", #date(2024,2,2), "P06","Jacket","Apparel","C004","S","South",4,2500,10000,7000}
 })
in
 Source
```

## Block B - Make a measure (a calculation)
1. In **Report view** (first icon on the left), click **Home** > **New measure**.
2. A formula bar appears. Type or paste the formula. Press **Enter**.
3. Make **one measure at a time**.
4. The measure appears in the Data pane on the right with a small calculator icon.

## Block C - Add a chart
1. Click an empty space on the page.
2. In the **Visualizations** pane (right), click the chart icon you need (hover to see names).
3. In the **Data** pane, **drag fields** into the boxes (X-axis, Y-axis, Legend, Values).
4. To change the title: click the chart > Format (paint roller) > Title.

## Block D - Date table (needed for YTD, HR and the project)
1. Home > **Modeling** tab > **New table**. Paste:
```
Dim_Date =
ADDCOLUMNS(
 CALENDAR(DATE(2017,1,1), DATE(2025,12,31)),
 "Year", YEAR([Date]),
 "Quarter", "Q" & FORMAT([Date], "Q"),
 "MonthNo", MONTH([Date]),
 "Month", FORMAT([Date], "MMM"),
 "YearMonth", FORMAT([Date], "yyyy-MM"),
 "YearMonthNo", YEAR([Date]) * 100 + MONTH([Date])
)
```
2. Click the table `Dim_Date` > **Table tools** > **Mark as date table** > choose `Date` > OK.
3. Click **Model view** (third icon on the left). Drag `Dim_Date[Date]` and drop it on `Fact_Sales[OrderDate]`. A line appears (relationship).
4. Click the `Month` column > **Column tools** > **Sort by column** > `MonthNo`. Do the same for `YearMonth` > `YearMonthNo`.

---

# PRACTICAL 1 - BI Tool Setup and Data Import
**Aim:** To install Power BI Desktop, explore it, and import data from CSV, Excel and a database.

**Steps (write these):**
1. Download and install Power BI Desktop (Microsoft Store). Open it and click Blank report.
2. Identify the 3 views on the left: **Report**, **Table (Data)**, **Model**.
3. Do **Block A** (import from CSV, or paste the data).
4. Set the data types and click Close & Apply.
5. In Table view check: 6 rows, no errors, Quantity x UnitPrice = Revenue.
6. Excel import: Home > Get data > **Excel workbook** > select file > choose the sheet in Navigator > Transform Data.
7. Database import: Home > Get data > **SQL Server** > type Server and Database > OK > choose table. *(If no database is available, write the steps and say "database not available in lab".)*
8. Save: File > Save as > `Northwind_Lab.pbix`.

**Write this - Output:** table of 6 rows (S101 to S106). Total Qty 32, Revenue 171,600, Cost 128,500.
**Observation:** The table has 6 orders, 32 units and total revenue INR 171,600. Each row satisfies Quantity x UnitPrice = Revenue.
**Result:** Data was imported with correct data types from CSV, Excel and database.

---

# PRACTICAL 2 - Data Cleaning and Transformation
**Aim:** To clean and reshape customer and sales data in Power Query.

**Needs:** customer file or this code. Home > Transform data > Get data > Blank query > Advanced editor > paste > Done > rename `customer_raw`:
```
let
 Source = #table(
 type table [CustomerKey=nullable text, CustomerName=nullable text, City=nullable text, Segment=nullable text],
 {
 {"C001","Aarav Shah","mumbai","Retail"},
 {"C002","Priya Nair","mumbai","Retail"},
 {"C002","Priya Nair","mumbai","Retail"},
 {"C003",null,"pune","Retail"},
 {"C004","Rohit Verma","bangalore","Corporate"},
 {"C005","Sneha Iyer","delhi","Retail"},
 {null,null,null,null}
 })
in
 Source
```
**Steps (write these):**
1. Open Power Query (Home > Transform data). Load customer data.
2. Right-click `customer_raw` > **Reference**. Rename the new query `Dim_Customer`.
3. Select all columns (click first header, Shift+click last) > **Transform** > **Format** > **Trim**.
4. **Home** > **Remove Rows** > **Remove Blank Rows**. (7 rows become 6)
5. Click the `CustomerKey` header > **Home** > **Remove Rows** > **Remove Duplicates**. (6 become 5)
6. Click the small arrow on `CustomerName` > untick **null** and **(blank)** > OK. (5 become 4)
7. Click `City` header > **Transform** > **Format** > **Capitalize Each Word**. (mumbai becomes Mumbai)
8. Unpivot: new blank query `Unpivot_Demo` with the code below, OR select CustomerKey > **Transform** > **Unpivot Other Columns**. Rename Attribute to Month and Value to Revenue.
9. Home > **Close & Apply**.
```
let
 Source = #table(type table [CustomerKey=text, Jan=number, Feb=number],
 {{"C001",100,150},{"C002",200,250}}),
 Long = Table.UnpivotOtherColumns(Source, {"CustomerKey"}, "Month", "Revenue")
in
 Long
```
**Write this - Output:** Row count table: raw 7, after blank removal 6, after duplicates 5, after missing name 4. Unpivot gives 4 rows.
**Observation:** Customer rows reduced from 7 to 4 clean customers. Unpivot of 2 customers x 2 months gives 4 rows.
**Result:** A clean customer table and an unpivoted table were produced.

---

# PRACTICAL 3 - Data Modeling (Star Schema)
**Aim:** To build a star schema with one fact table and four dimension tables.

**Steps (write these):**
1. Open Power Query. Right-click `Fact_Sales` > **Reference**. Rename the new query `Dim_Product`.
2. In Dim_Product: Home > **Choose Columns** > keep only ProductKey, ProductName, Category > OK. Select ProductKey > Remove Rows > **Remove Duplicates**.
3. Do the same again: Reference `Fact_Sales` > name `Dim_Region` > keep RegionKey, RegionName > Remove Duplicates.
4. Customers: use `Dim_Customer` from Practical 2 (if not done, skip it).
5. Home > **Close & Apply**.
6. Do **Block D** (create Dim_Date, mark as date table).
7. Open **Model view**. Drag: `Dim_Product[ProductKey]` onto `Fact_Sales[ProductKey]`; `Dim_Region[RegionKey]` onto `Fact_Sales[RegionKey]`; `Dim_Customer[CustomerKey]` onto `Fact_Sales[CustomerKey]`; `Dim_Date[Date]` onto `Fact_Sales[OrderDate]`.
8. Double-click each line and check: **One to many (1:*)**, **Single** direction, **Active** ticked.
9. Test: go to Report view, add a **Table** visual with `Dim_Region[RegionName]` and `Fact_Sales[Revenue]` (Sum).

**Write this - Output:** draw Fact_Sales in the middle and 4 boxes around it, with arrows "1 to *". Table: North 119,600 / East 28,500 / South 16,000 / West 7,500 / Total 171,600.
**Observation:** Region totals add up to 171,600. Selecting one region filters the sales rows, so the relationships work.
**Result:** Four dimension tables were linked to the fact table with correct totals.

---

# PRACTICAL 4 - Basic Visualizations
**Aim:** To create column, line, donut and treemap visuals for sales by region, time and product.

**Before you start:** do Block A. (If you already did P3, you can use the Dim fields instead; both work.)

**Steps (write these):**
1. Create two measures (**Block B**, one at a time):
```
Total Sales = SUM(Fact_Sales[Revenue])
```
```
Total Quantity = SUM(Fact_Sales[Quantity])
```
2. **Column chart** (Block C): choose **Clustered column chart**. Drag `RegionName` to X-axis and `Total Sales` to Y-axis. Click "..." on the chart > **Sort axis** > Total Sales > Sort descending. Format > Data labels ON.
3. **Line chart**: choose **Line chart**. Drag `OrderDate` to X-axis and `Total Sales` to Y-axis. On X-axis box click the arrow beside OrderDate and choose **OrderDate** (not Date Hierarchy). Format > Markers ON.
4. **Donut chart**: choose **Donut chart**. Drag `Category` to Legend and `Total Sales` to Values. Format > Detail labels > show **Category, value, percent of total**.
5. **Treemap**: choose **Treemap**. Drag `Category` to Group, `ProductName` to Details, `Total Sales` to Values. Drag `Total Quantity` into **Tooltips**.
6. Arrange all 4 charts on one page. Give each chart a title (example: "Total Sales by Region (INR)").
7. Save the file.

**Write this - Output:** sketch the 4 charts. Numbers: North 119,600; East 28,500; South 16,000; West 7,500. Electronics 148,100 (86.31%); Apparel 23,500 (13.69%).
**Observation:** North has the highest sales. Electronics gives 86.31% of total sales. Laptop has the highest revenue (110,000); T-Shirt has the highest quantity (10 units).
**Result:** Column, line, donut and treemap visuals were created with correct fields, labels and tooltips.

---

# PRACTICAL 5 - Calculated Columns and Measures (DAX)
**Aim:** To create calculated columns and DAX measures including time intelligence.

**Before you start:** Block A. For Sales YTD/PY also do **Block D**.

**Steps (write these):**
1. Click table `Fact_Sales` in the Data pane > **Table tools** > **New column**. Type:
```
Profit = Fact_Sales[Revenue] - Fact_Sales[Cost]
```
2. New column again:
```
Order Size = IF(Fact_Sales[Revenue] > 50000, "Large", "Standard")
```
3. Make these measures, **one at a time** (Block B):
```
Total Sales = SUM(Fact_Sales[Revenue])
```
```
Total Profit = SUM(Fact_Sales[Profit])
```
```
Total Orders = DISTINCTCOUNT(Fact_Sales[OrderID])
```
```
AOV = DIVIDE([Total Sales], [Total Orders])
```
```
Profit Margin = DIVIDE([Total Profit], [Total Sales])
```
```
Weighted Unit Price = DIVIDE([Total Sales], SUM(Fact_Sales[Quantity]))
```
4. Time intelligence (needs Block D):
```
Sales YTD = TOTALYTD([Total Sales], Dim_Date[Date])
```
```
Sales PY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(Dim_Date[Date]))
```
```
YoY Growth % = DIVIDE([Total Sales] - [Sales PY], [Sales PY])
```
5. Add a **Table** visual with OrderID, Revenue, Cost, Profit, Order Size.
6. Add 4 **Card** visuals: Total Sales, Total Profit, AOV, Profit Margin (set Profit Margin to Percentage: click it > Measure tools > %).
7. Add a **Slicer** with `RegionName`. Click North and see the cards change. Clear it.

**Write this - Output:** profit per order: 25,000 / 2,000 / 7,500 / 2,500 / 3,100 / 3,000. Cards: Sales 171,600; Profit 43,100; AOV 28,600; Margin 25.12%. North: sales 119,600, profit 28,100.
**Observation:** Only S101 is Large. Prior-year sales and YoY are blank because there is no 2023 data. Profit column stays fixed; measures change when a filter is applied.
**Result:** Calculated columns and filter-responsive measures were created.

---

# PRACTICAL 6 - Filters, Slicers and Drill Through
**Aim:** To use slicers, filters, drill-down and drillthrough.

**Before you start:** Block A and the measures from P4 and P5 (Total Sales, Total Quantity, Total Orders, Total Profit).

**Steps (write these):**
1. Add 3 **Slicers** (Block C, slicer icon): `RegionName`, `Category`, `OrderDate`. For the date slicer click the small arrow at its top-right > **Between**.
2. Click a chart. In the **Filters** pane (right): drag `Total Sales` into "Filters on this visual" > set "is greater than 0" (visual-level).
3. Drag `Category` into "Filters on this page" > Advanced filtering > "is not blank" (page-level).
4. Drag `OrderDate` into "Filters on all pages" > "is on or after" 01-Jan-2024 (report-level).
5. Test: click **Electronics** in the slicer, note the numbers. Click again to clear. Test **North**.
6. Interactions: click a chart > **Format** tab > **Edit interactions** to choose which charts react.
7. Hierarchy: on a column chart drag `Year > Quarter > Month > Date` from Dim_Date (Block D) into X-axis. Use the drill-down arrows on the chart.
8. New page: click **+** at the bottom. Rename it **Product Details** (double-click the tab).
9. On this page: in the Visualizations pane scroll to **Drill through** box and drag `ProductName` into it. A Back button appears automatically (or Insert > Buttons > Back).
10. Add a **Table** with ProductName, OrderID, OrderDate, Quantity, Revenue, Profit, and 2 cards (Total Sales, Total Quantity).
11. Go back to the first page. Put a chart with ProductName on it. **Right-click Laptop > Drill through > Product Details**.

**Write this - Output:** table: None = 6 orders, 32 units, 171,600. Electronics = 3, 13, 148,100. North = 2 orders, 119,600. Laptop page: S101, 2 units, Revenue 110,000, Profit 25,000.
**Observation:** Filters change the numbers shown. Drillthrough shows only rows of the selected product.
**Result:** Slicers, filters, drill-down and drillthrough work correctly.

---

# PRACTICAL 7 - KPI and Scorecards
**Aim:** To create revenue and conversion KPIs, compare actual with target and apply RAG colours.

**Needs:** monthly file or this code (Transform data > Get data > Blank query > Advanced editor > paste > rename `monthly_data` > Close & Apply):
```
let
 Source = #table(
 type table [MonthStart=date, ActualRevenue=Currency.Type, TargetRevenue=Currency.Type, Leads=Int64.Type, Conversions=Int64.Type],
 {
 {#date(2024,1,1),420000,400000,1000,100},
 {#date(2024,2,1),380000,420000,1200,96},
 {#date(2024,3,1),510000,450000,1500,180},
 {#date(2024,4,1),295000,350000,1000,70}
 })
in
 Source
```
**Steps (write these):**
1. Make these measures one at a time (Block B). Click table `monthly_data` first:
```
Actual Revenue = SUM(monthly_data[ActualRevenue])
```
```
Target Revenue = SUM(monthly_data[TargetRevenue])
```
```
Revenue Attainment % = DIVIDE([Actual Revenue], [Target Revenue])
```
```
Revenue Variance = [Actual Revenue] - [Target Revenue]
```
```
Revenue Variance % = DIVIDE([Revenue Variance], [Target Revenue])
```
```
Revenue Status =
VAR T = [Target Revenue]
VAR A = [Revenue Attainment %]
RETURN IF(ISBLANK(T) || T = 0, "No target",
 SWITCH(TRUE(), A < 0.9, "Red", A < 1, "Amber", "Green"))
```
```
Conversion Rate = DIVIDE(SUM(monthly_data[Conversions]), SUM(monthly_data[Leads]))
```
2. Format the attainment, variance and conversion measures as **%** (click measure > Measure tools > %).
3. Add a **Column chart**: X-axis `MonthStart`, Y-axis `Actual Revenue`. Click "..." > Sort axis > MonthStart > Ascending.
4. Click the chart and choose the **KPI** visual. Fill: Value = Actual Revenue, Trend axis = MonthStart, Target = Target Revenue.
5. Add a **Table** with MonthStart, Actual Revenue, Target Revenue, Revenue Attainment %, Revenue Variance %, Revenue Status.
6. Colours: in the Visualizations pane click the arrow beside `Revenue Attainment %` > **Conditional formatting** > **Background color** > Format style **Rules** > rules: if value >= 1 (Number) = green; if >= 0.9 and < 1 = amber; if < 0.9 = red.
7. Add a **Card** with `Conversion Rate`.
8. Tooltip page (if asked): new page > Format page > **Canvas settings** > Type = **Tooltip** > turn on "Allow use as tooltip". Add a small chart. On the main chart: Format > General > Tooltips > Type = **Report page** > choose that page.

**Write this - Output:** table Jan 105.00% Green; Feb 90.48% Amber; Mar 113.33% Green; Apr 84.29% Red. KPI = April 295,000 vs target 350,000. Conversion 446/4,700 = 9.49%.
**Observation:** Latest month April missed target by 15.71%. March exceeded target by 13.33%.
**Result:** Revenue KPI, RAG scorecard and conversion metric were created.

---

# PRACTICAL 8 - Sales Dashboard
**Aim:** To build and publish a one-page executive sales report.

**Before you start:** Block A and measures from P4/P5 (Total Sales, Total Orders, Total Profit, AOV, Total Quantity).

**Steps (write these):**
1. Draw a layout on paper: cards on top, 2 charts in the middle, line chart at the bottom, slicers on the right.
2. **View** tab > Page view > Fit to page. Format page > Canvas settings > Type **16:9**.
3. Insert a **Text box** (Insert > Text box). Type "Executive Sales Report - Jan-Feb 2024 (INR)".
4. Add 4 **Card** visuals: Total Sales, Total Orders, Total Profit, AOV.
5. Add a **Clustered bar chart**: Y-axis `RegionName`, X-axis `Total Sales`. Sort descending.
6. Add a **Clustered column chart**: X-axis `Category`, Y-axis `Total Sales`. Drag Total Quantity and Total Profit into Tooltips.
7. Add a **Line chart** (wide): X-axis `OrderDate`, Y-axis `Total Sales`, Markers ON.
8. Add 3 **Slicers**: RegionName, Category, OrderDate (Between).
9. Test: North, Electronics, January only. Clear all after testing.
10. **View** > **Themes** > choose a blue/navy theme.
11. Save. **Home** > **Publish** > sign in > choose workspace. *(Publishing needs a work/school account. If it is not possible, write the steps and say "no account available".)*
12. Dashboard (Service): open the report online > click Pin icon on visuals > New dashboard.

**Write this - Output:** layout sketch. Cards: 171,600 / 6 / 43,100 / 28,600. Filters: North 119,600; Electronics 148,100; January 161,600.
**Observation:** The cards update when slicers are used. A report is built in Desktop; a dashboard is a page of pinned tiles in the Power BI Service.
**Result:** An interactive sales report was built and prepared for publishing.

---

# PRACTICAL 9 - HR Dashboard
**Aim:** To build an HR dashboard showing exit percentage by department, monthly exits and employee detail.

**Needs:** HR file or code (Blank query, rename `hr_dashboard_data`):
```
let
 Source = #table(
 type table [EmployeeID=text, Department=text, JoiningDate=date, ExitDate=nullable date, Status=text, Salary=Currency.Type],
 {
 {"E001","Sales",#date(2020,1,10),null,"Active",40000},
 {"E002","HR",#date(2019,6,1),#date(2024,3,1),"Exited",55000},
 {"E003","Finance",#date(2021,7,1),null,"Active",45000},
 {"E004","IT",#date(2017,8,1),#date(2023,11,1),"Exited",52000},
 {"E005","Sales",#date(2022,4,1),null,"Active",38000}
 })
in
 Source
```
**Steps (write these):**
1. Load the HR data (Close & Apply). Check JoiningDate and ExitDate are **Date** type.
2. Do **Block D** (Dim_Date).
3. Model view: drag `Dim_Date[Date]` onto `hr_dashboard_data[JoiningDate]` (stays active). Drag `Dim_Date[Date]` onto `hr_dashboard_data[ExitDate]`; double-click that line and **untick "Make this relationship active"** (inactive, dotted line).
4. Make measures (click table `hr_dashboard_data` first):
```
Total Employees = DISTINCTCOUNT(hr_dashboard_data[EmployeeID])
```
```
Exited Employees = CALCULATE([Total Employees], hr_dashboard_data[Status] = "Exited")
```
```
Cohort Exit % = DIVIDE([Exited Employees], [Total Employees])
```
```
Monthly Exits = CALCULATE([Exited Employees], USERELATIONSHIP(hr_dashboard_data[ExitDate], Dim_Date[Date]))
```
5. Add 3 **Cards**: Total Employees, Exited Employees, Cohort Exit % (format as %).
6. **Column chart**: X-axis `Department`, Y-axis `Cohort Exit %`. Colour rule: Format > Columns > Colors > fx > Rules: value > 0.20 = red.
7. **Column chart**: X-axis `Dim_Date[YearMonth]`, Y-axis `Monthly Exits`.
8. New page **Employee Detail**: drag `Department` into **Drill through** box. Add a table with EmployeeID, Department, JoiningDate, ExitDate, Status, Salary.
9. New page **Employee Record**: drag `EmployeeID` into Drill through. Add the same table.
10. Test: right-click a department bar > Drill through > Employee Detail.

**Write this - Output:** Cards 5 / 2 / 40%. Table: HR 1 of 1 = 100%, IT 1 of 1 = 100%, Sales 0 of 2, Finance 0 of 1. Exits: 2023-11 = 1, 2024-03 = 1.
**Observation:** The sample has only 5 employees, so 40% is not a company turnover rate. Exits are counted by exit date using the inactive relationship.
**Result:** HR exit analysis, monthly exit trend and employee drillthrough were produced. *(Finance version needs a finance dataset, which was not given.)*

---

# PRACTICAL 10 - End-to-End BI Project
**Aim:** To build a complete four-page BI report from data to publishing.

**Before you start:** Do P3 (star schema), P5 (measures), P7 (monthly_data). Add one more relationship: Model view > drag `Dim_Date[Date]` onto `monthly_data[MonthStart]`.

**Extra measures:**
```
Region Share % = DIVIDE([Total Sales], CALCULATE([Total Sales], REMOVEFILTERS(Dim_Region[RegionName])))
```
```
Laptop Monitor Profit Share = DIVIDE(CALCULATE([Total Profit], Dim_Product[ProductName] IN {"Laptop","Monitor"}), CALCULATE([Total Profit], REMOVEFILTERS(Dim_Product)))
```
**Steps (write these):**
1. Write the business question: "Which regions and products give most sales and profit, and are we meeting targets?"
2. Page 1 **Executive Summary**: 4 cards (Total Sales, Total Orders, Total Profit, AOV) + line chart by date + a text box "Data covers Jan-Feb 2024 only".
3. Page 2 **Regional Analysis**: column chart `RegionName` x `Total Sales`, `Region Share %` as another chart, and a table.
4. Page 3 **Product Analysis**: table with ProductName, Revenue, Cost, Total Profit, Profit Margin.
5. Page 4 **Performance Scorecard**: KPI and RAG table from Practical 7.
6. Sync slicers: add a date slicer > **View** tab > **Sync slicers** > tick all 4 pages (Sync column). Region and Category slicers: tick pages 1 to 3 only.
7. Test: baseline 171,600, January 161,600, February 10,000.
8. Publish: Home > **Publish** > workspace. In the Service: Semantic model > Settings > set credentials and gateway > Scheduled refresh > **Refresh now** > check Refresh history. *(Needs an account; if not possible, write it as steps.)*

**Write this - Output:** page sketches. Insights: North 69.70% of sales; Electronics 86.31%; Laptop + Monitor 75.41% of profit.
**Observation:** Sales are concentrated in North and Electronics. Scorecard totals differ from sales totals because monthly_data is a separate dataset. Only 2 months of data, so growth cannot be concluded.
**Result:** A four-page BI report with synced slicers and supported insights was prepared.

---

# IF SOMETHING GOES WRONG
| Problem | Fix |
|---|---|
| Totals are double | A table is loaded twice. In Power Query, right-click the extra query > untick **Enable load**. |
| Chart is blank or wrong | Check the relationship in Model view (One to many, Single, Active). |
| X-axis shows Year/Quarter | Click the arrow beside the field in the X-axis box > choose the date itself. |
| Months are in wrong order | Select Month column > Column tools > Sort by column > MonthNo. |
| DAX error | One measure per box. Use straight quotes ". Check the column name spelling. |
| Column is called `SalesAmount` | Rename to `Revenue` in Power Query (double-click the header). |
| Dates look wrong | Power Query > column type > **Using locale** > Date > English (India). |
| Cannot publish | Needs a work/school account. Write the steps and say account not available. |
