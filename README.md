# Loan Risk Management

## Project_Overview
---
Daiz Bank is a Loan financing bank.  Its challenge revolves around the management of their vast and diverse loan portfolio—a portfolio that has been the cornerstone of the bank's success.
Conducting a risk analysis can help the bank determine whether they should approve a loan or financial application, and what actions they may need to take to protect their interests.
This type of analysis facilitates a balance between risks and risk reduction. By delving deep into the data, Daiz Bank can identify underserved markets, customer segments with growth potential, and innovative lending products. 
Insights.

### Tools
-Excel

-Microsoft PowerBI

### Data Description
Note: A default on a loan occurs when payments are missed for a specified period, and it has been established that the loan is no longer going to be repaid. Arrears, are payments that are late, delayed or missed but where the borrower has every intention of making the payments later.
The bank has a database with 1500 rows of expense information for analysis, with the following attributes:
 
✓ Loan ID: A unique identifier for each loan in the portfolio.
✓ Borrower ID: A unique identifier for each borrower associated with the loan.
✓ Borrower Name: The full name of the borrower.
✓ Gender: The gender of the borrower.
✓ Borrower Age: The age of the borrower.
✓ Marital Status: The marital status of the borrower.
✓ Borrower Address: The address of the borrower.
✓ Borrower Email: The email address of the borrower.
✓ Borrower Phone Number: The phone number of the borrower.
✓ Loan Amount: The amount of money borrowed by the borrower.
✓ Loan Type: The type of loan, such as personal loan, mortgage, auto loan, or commercial loan.
✓ Interest Rate: The annual interest rate charged on the loan.
✓ Loan Term: The duration of the loan, in years.
✓ Loan Status: The status of the loan, such as "Paid Off," "Defaulted," or "In Arrears."
✓ Credit Score: The credit score of the borrower, which reflects their creditworthiness.
✓ Income: The income of the borrower.
✓ Employment Status: The employment status of the borrower, such as "Employed," "Self-Employed," or "Unemployed."
✓ Loan Origination Date: The date when the loan was initially issued.
✓ Loan Paid Date: The date when the loan was paid off or settled.



### Methodology
✓ Data Importation: Import the data into the environment.
✓ Data Cleaning and Manipulation
✓ Data Exploration and Analysis.
✓ Data Visualization.

## Data Analysis
```
let
    // configurations start
    Today=Date.From(DateTime.LocalNow()), // today's date
    FromYear = 2022, // set the start year of the date dimension. dates start from 1st of January of this year
    ToYear=2023, // set the end year of the date dimension. dates end at 31st of December of this year
    StartofFiscalYear=7, // set the month number that is start of the financial year. example; if fiscal year start is July, value is 7
    firstDayofWeek=Day.Monday, // set the week's start day, values: Day.Monday, Day.Sunday....
    // configuration end
    FromDate=#date(FromYear,1,1),
    ToDate=#date(ToYear,12,31),
    Source=List.Dates(
        FromDate,
        Duration.Days(ToDate-FromDate)+1,
        #duration(1,0,0,0)
    ),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Renamed Columns" = Table.RenameColumns(#"Converted to Table",{{"Column1", "Date"}}),
    #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"Date", type date}}),
    #"Inserted Year" = Table.AddColumn(#"Changed Type", "Year", each Date.Year([Date]), Int64.Type),
    #"Inserted Start of Year" = Table.AddColumn(#"Inserted Year", "Start of Year", each Date.StartOfYear([Date]), type date),
    #"Inserted End of Year" = Table.AddColumn(#"Inserted Start of Year", "End of Year", each Date.EndOfYear([Date]), type date),
    #"Inserted Month" = Table.AddColumn(#"Inserted End of Year", "Month", each Date.Month([Date]), Int64.Type),
    #"Inserted Start of Month" = Table.AddColumn(#"Inserted Month", "Start of Month", each Date.StartOfMonth([Date]), type date),
    #"Inserted End of Month" = Table.AddColumn(#"Inserted Start of Month", "End of Month", each Date.EndOfMonth([Date]), type date),
    #"Inserted Days in Month" = Table.AddColumn(#"Inserted End of Month", "Days in Month", each Date.DaysInMonth([Date]), Int64.Type),
    #"Inserted Day" = Table.AddColumn(#"Inserted Days in Month", "Day", each Date.Day([Date]), Int64.Type),
    #"Inserted Day Name" = Table.AddColumn(#"Inserted Day", "Day Name", each Date.DayOfWeekName([Date]), type text),
    #"Inserted Day of Week" = Table.AddColumn(#"Inserted Day Name", "Day of Week", each Date.DayOfWeek([Date],firstDayofWeek), Int64.Type),
    #"Inserted Day of Year" = Table.AddColumn(#"Inserted Day of Week", "Day of Year", each Date.DayOfYear([Date]), Int64.Type),
    #"Inserted Month Name" = Table.AddColumn(#"Inserted Day of Year", "Month Name", each Date.MonthName([Date]), type text),
    #"Inserted Quarter" = Table.AddColumn(#"Inserted Month Name", "Quarter", each Date.QuarterOfYear([Date]), Int64.Type),
    #"Inserted Start of Quarter" = Table.AddColumn(#"Inserted Quarter", "Start of Quarter", each Date.StartOfQuarter([Date]), type date),
    #"Inserted End of Quarter" = Table.AddColumn(#"Inserted Start of Quarter", "End of Quarter", each Date.EndOfQuarter([Date]), type date),
    #"Inserted Week of Year" = Table.AddColumn(#"Inserted End of Quarter", "Week of Year", each Date.WeekOfYear([Date],firstDayofWeek), Int64.Type),
    #"Inserted Week of Month" = Table.AddColumn(#"Inserted Week of Year", "Week of Month", each Date.WeekOfMonth([Date],firstDayofWeek), Int64.Type),
    #"Inserted Start of Week" = Table.AddColumn(#"Inserted Week of Month", "Start of Week", each Date.StartOfWeek([Date],firstDayofWeek), type date),
    #"Inserted End of Week" = Table.AddColumn(#"Inserted Start of Week", "End of Week", each Date.EndOfWeek([Date],firstDayofWeek), type date),
    FiscalMonthBaseIndex=13-StartofFiscalYear,
    adjustedFiscalMonthBaseIndex=if(FiscalMonthBaseIndex>=12 or FiscalMonthBaseIndex<0) then 0 else FiscalMonthBaseIndex,
    #"Added Custom" = Table.AddColumn(#"Inserted End of Week", "FiscalBaseDate", each Date.AddMonths([Date],adjustedFiscalMonthBaseIndex)),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"FiscalBaseDate", type date}}),
    #"Inserted Year1" = Table.AddColumn(#"Changed Type1", "Year.1", each Date.Year([FiscalBaseDate]), Int64.Type),
    #"Renamed Columns1" = Table.RenameColumns(#"Inserted Year1",{{"Year.1", "Fiscal Year"}}),
    #"Inserted Quarter1" = Table.AddColumn(#"Renamed Columns1", "Quarter.1", each Date.QuarterOfYear([FiscalBaseDate]), Int64.Type),
    #"Renamed Columns2" = Table.RenameColumns(#"Inserted Quarter1",{{"Quarter.1", "Fiscal Quarter"}}),
    #"Inserted Month1" = Table.AddColumn(#"Renamed Columns2", "Month.1", each Date.Month([FiscalBaseDate]), Int64.Type),
    #"Renamed Columns3" = Table.RenameColumns(#"Inserted Month1",{{"Month.1", "Fiscal Month"}}),
    #"Removed Columns" = Table.RemoveColumns(#"Renamed Columns3",{"FiscalBaseDate"}),
    #"Inserted Age" = Table.AddColumn(#"Removed Columns", "Age", each [Date]-Today, type duration),
    #"Extracted Days" = Table.TransformColumns(#"Inserted Age",{{"Age", Duration.Days, Int64.Type}}),
    #"Renamed Columns4" = Table.RenameColumns(#"Extracted Days",{{"Age", "Day Offset"}}),
    #"Added Custom1" = Table.AddColumn(#"Renamed Columns4", "Month Offset", each (([Year]-Date.Year(Today))*12)
+([Month]-Date.Month(Today))),
    #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom1",{{"Month Offset", Int64.Type}}),
    #"Added Custom2" = Table.AddColumn(#"Changed Type2", "Year Offset", each [Year]-Date.Year(Today)),
    #"Changed Type3" = Table.TransformColumnTypes(#"Added Custom2",{{"Year Offset", Int64.Type}}),
    #"Added Custom3" = Table.AddColumn(#"Changed Type3", "Quarter Offset", each (([Year]-Date.Year(Today))*4)
+([Quarter]-Date.QuarterOfYear(Today))),
    #"Changed Type4" = Table.TransformColumnTypes(#"Added Custom3",{{"Quarter Offset", Int64.Type}}),
    #"Added Custom4" = Table.AddColumn(#"Changed Type4", "Year-Month", each Date.ToText([Date],"MMM yyyy")),
    #"Added Custom5" = Table.AddColumn(#"Added Custom4", "Year-Month Code", each Date.ToText([Date],"yyyyMM")),
    #"Changed Type5" = Table.TransformColumnTypes(#"Added Custom5",{{"Year-Month", type text}, {"Year-Month Code", Int64.Type}})
in
    #"Changed Type5"
```

### Results/Findings
1. Customer Segmentation:
   - Low Risk Customers:
     - Represent 72.67% of the total customer base.
     - Have received 81.35% ($30.1M) of the total loan amount issued.
     - Most likely to pay off their debt with a lower default rate (17.34%).
     - Highest average loan amount ($27.66k) and income ($52.95k).
     - Employed customers make up 76.33% of this segment.
     - Mortgage loans are the most common type among this group.

   - Moderate Risk Customers:
     - Represent 16.53% of the total customer base.
     - Have received 13.51% ($5M) of the total loan amount issued.
     - Moderate likelihood of paying off their debt with a default rate of 14.92%.
     - Average loan amount is $21.40k and average income is $39.98k.
     - Employment status is mixed, with a significant portion unemployed (29.44%).
     - Personal loans are the most common type among this group.

   - High Risk Customers:
     - Represent 10.8% of the total customer base.
     - Have received 2.7% ($1M) of the total loan amount issued.
     - Highest default rate (19.14%).
     - Lowest average loan amount ($7.18k) and income ($13.73k).
     - All customers are unemployed.
     - Commercial loans are the most common type among this group.

2. Loan Distribution:
   - Highest total loan amount was issued in 2019 ($12.3M).
   - Lowest total loan amount was issued in 2018 ($3.7M).

3. Demographics:
   - Male customers form a significant majority (71.13%).
   - Loan amounts and income levels are higher among low-risk customers.
   - All high-risk customers are unemployed and within the age bracket of 46-55.


### Recommendations

1. Risk Mitigation:
   - Low Risk Segment:
     - Focus on retaining and growing this segment by offering tailored financial products like higher-value mortgages and investment opportunities.
     - Enhance customer loyalty programs to maintain high repayment rates.

   - Moderate Risk Segment:
     - Implement support programs to reduce unemployment within this segment, potentially improving their repayment capability.
     - Offer financial literacy programs to moderate-risk customers to improve their credit scores.
     - Consider providing lower interest rates or extended terms for those showing consistent repayment.

   - High Risk Segment:
     - Limit exposure to high-risk customers by capping loan amounts or requiring collateral.
     - Introduce more stringent credit checks and monitoring.
     - Offer microloans with higher interest rates to offset the risk.

2. Product Innovation:
   - Introduce flexible repayment plans for customers in the moderate and high-risk categories to reduce default rates.

3. Market Expansion:
      - Explore partnerships with organizations that can provide employment or skill development to unemployed customers.

4. Enhanced Customer Relationship Management:
   - Maintain regular communication with all customer segments to understand their needs and adjust products/services accordingly.
   - Offer personalized financial advice and planning services to improve customer satisfaction and loyalty.

<img width="747" height="743" alt="image" src="https://github.com/user-attachments/assets/6d5d0650-f55c-43db-a6ec-ed048e2c5eef" />

### References
AMDARI
