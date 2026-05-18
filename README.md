# 📊 E2E Loan Default Data Analysis (Snowflake + PowerBI Project)

## Project Overview

This end-to-end data analysis project focuses on analyzing loan default patterns and borrower financial behavior using Snowflake, SQL, and PowerBI. The project aims to identify key financial risk indicators, borrower demographics, lending trends, and default behaviors through interactive dashboards and business intelligence reporting.

The analysis explores how factors such as income, employment type, credit score, debt-to-income ratio (DTI), education level, marital status, and loan purposes influence loan approval trends and default risks. The project also highlights year-over-year lending performance, borrower segmentation, and financial risk metrics to support data-driven lending decisions and portfolio risk management.

**PowerBI Report**: [Loan Default Data Analysis.pbix](https://app.powerbi.com/links/6YQeHhxOYO?ctid=35ae1710-01c9-4681-aee5-aefcb73885b1&pbi_source=linkShare&bookmarkGuid=ac2fcc75-b6a5-40be-9af3-29b47cb96f2f)

---

## Objectives

- Analyze borrower demographics and financial characteristics.
- Identify key factors contributing to loan default risk.
- Monitor lending trends and repayment behavior over time.
- Compare loan distributions across employment, education, income, and credit score categories.
- Evaluate financial risk indicators such as DTI ratio, interest rate, and credit score.
- Build interactive dashboards for business insights and decision-making.
- Discover high-risk borrower segments for better risk management strategies.
- Perform year-over-year and year-to-date loan performance analysis.

---

## Dataset Information

**Raw Data**: [Loan Default Dataset](https://raw.githubusercontent.com/htetaunglynn94/udemy/refs/heads/main/da_bootcamp_BtoA/snowflake/Project%201%20Loan%20Data%20Analysis/loan_default_data.csv?token=GHSAT0AAAAAAD4ZYNWVWPDX6PJDYQGY44EW2QLFD7A)

**Dataset Description**
- The Loan Default dataset contains information about borrowers who have applied for loans, along with details about their financial status, loan characteristics and repayment behavior.
- The dataset contains:

| Column Name | Definition |
| ----------- | ----------- |
| LoanID      | A unique identifier for each loan in the dataset.|
|Age| The borrower's age at the time the loan was issued. |
|Income| The borrower's annual income. |
|LoanAmount| The total amount of the loan that the borrower is requesting or has been approved for.|
|CreditScore|A numerical representation of the borrower's credit worthiness, typically ranging from 300 to 850. A higher credit score indicates the borrower is more likely to repay the debt.|
|MonthsEmployed| The number of months the borrower has been employed at their current job or with their current employer.|
|NumCreditLines| The total number of active credit lines (e.g., credit cards, loans) the borrower has at the time of applying for the loan.|
|InterestRate| The annual percentage rate (APR) charged for borrowing the loan amount, usually expressed as a percentage.|
|LoanTerm| The length of time (in months) over which the loan is to be repaid. |
|DTIRatio| The Debt-to-Income ratio, which measures the borrower's debt payments relative to their income. A higher ratio can indicate greater financial stress. |
|Education| The highest level of education the borrower has completed (e.g., High School, Bachelor's, Master's, etc.). |
|EmploymentType| The type of employment the borrower is engaged in (e.g., Full-Time, Part-Time, Self-Employed, etc.) |
|MaritalStatus| The marital status of the borrower. (e.g., Single, Married, Divorced, etc.) |
|HasMortgage| An indicator (e.g., Yes/No) that shows whether the borrower has an existing mortgage on a property. |
|HasDependents| An indicator (e.g., Yes/No) that shows whether the borrower has dependents (children, other family members) to support. |
|LoanPurpose| The primary reason for taking out the loan (e.g., Home Purchase, Debt Consolidation, Education, etc.) |
| HasCoSigner| An indicator (e.g., Yes/No) that shows whether the borrower has a co-singer for the loan (someone who agrees to take responsibility if the borrower defaults). |
|Default| An indicator (e.g., Yes/No) that shows whether the borrower defaulted on the loan or failed to make timely payments. |
|Loan Date (DD/MM/YYYY)| The date the loan was issued or originated. |

---

## Dashboard Structure

### Overview Dashboard

The dashboard is divided into multiple analytical sections to provide comprehensive insights into borrower behavior, loan distribution, and financial risk metrics.

Key dashboard sections include:
- Loan Distribution by Purpose
- Borrower Demographics Analysis
- Employment & Income Analysis
- Credit Score & Financial Risk Assessment
- Default Rate Analysis
- DTI & Interest Rate Analysis
- Year-over-Year Loan Performance Tracking
- YTD Lending Trend Analysis

Interactive filters and slicers allow users to dynamically explore borrower profiles, financial conditions, and lending performance across different categories.

---

## Data Preparation

### DAX Functions

#### Create COLUMNS
- Extract Date
    ```DAX
    Year = YEAR(Loan_default[Loan_Date_DD_MM_YYYY].[Date])
    ```
- Age Groups
    ```DAX
    Age Groups = 
    IF('Loan_default'[Age] <= 19, "Teen",
    IF('Loan_default'[Age] <= 39, "Adults",
    IF('Loan_default'[Age] <= 59, "Middle Age Adults",
    "Senior Citizen")))
    ```
- Credit Score Bins
    ```DAX
    Credit Score Bins = 
    IF('Loan_default'[CreditScore] <= 400, "Very low",
    IF('Loan_default'[CreditScore] <= 450, "Low",
    IF('Loan_default'[CreditScore] <= 650, "Medium", "High")))
    ```
- IncomeBracket
    ```DAX
    IncomeBracket = 
    SWITCH(
        TRUE(), 
        'Loan_default'[Income] <  3000, "Low",
        'Loan_default'[Income] >= 3000 && 'Loan_default'[Income] < 60000,
        "Medium",
        'Loan_default'[Income] >= 60000, "High")
    ```
- DTI Group
    ```DAX
    DTI Group = 
    SWITCH(TRUE(),
    'Loan_default'[DTIRatio] < 0.35, "Low DTI",
    'Loan_default'[DTIRatio] < 0.75, "Medium DTI",
    "High DTI")
    ```
- Interest Rate Group
    ```DAX
    Interest Rate Group = 
    SWITCH(TRUE(),
    'Loan_default'[InterestRate] < 7.75, "Low Interest",
    'Loan_default'[InterestRate] < 13.5, "Medium Interest",
    "High Interest")
    ```

#### Create MEASURES
- Loan Amount by Purposes
    ```DAX
    Loan Amount by Purpose = 
    SUMX(FILTER('Loan_default', NOT(ISBLANK('Loan_default'[LoanAmount]))),
    'Loan_default'[LoanAmount])
    ```
- Avg Income by EmploymentType
    ```DAX
    Aveg Income by EmploymentType = 
    CALCULATE(AVERAGE('Loan_default'[Income]), 
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType]))
    ```
- DefaultRate by EmploymentType
    ```DAX
    DefaultRate by EmploymentType = 
    VAR TotalRecords = COUNTROWS(ALL('Loan_default'))
    VAR DefaultCases = COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE()))

    RETURN
    CALCULATE(DIVIDE(DefaultCases, TotalRecords), 
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType]))
    ```
- Avg Loan by AgeGps
    ```DAX
    Avg Loan by AgeGps = 
    AVERAGEX(VALUES('Loan_default'[Age Groups]),
    AVERAGE('Loan_default'[LoanAmount]))
    ```
- DefaultRate by Year
    ```DAX
    DefaultRate by Year = 

    VAR TotalLoans = CALCULATE(COUNTROWS('Loan_default'), 
                        ALLEXCEPT('Loan_default', 'Loan_default'[Year]))

    VAR Default = CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE())), 
                    ALLEXCEPT('Loan_default','Loan_default'[Year]))

    RETURN
    DIVIDE(Default, TotalLoans) * 100
    ```
- Median by CreditScoreBins
    ```DAX
    Median by CreditScoreBins = 
    MEDIANX('Loan_default', 'Loan_default'[LoanAmount])
    ```
- Avg Loan Amt (High Credit)
    ```DAX
    Avg Loan Amt (High Credit) = 
    AVERAGEX(FILTER('Loan_default', 'Loan_default'[Credit Score Bins] = "High"),
    AVERAGE('Loan_default'[LoanAmount]))
    ```
- TotalLoan (CreditBins)
    ```DAX
    TotalLoan (CreditBins) = 
    CALCULATE(SUM('Loan_default'[LoanAmount]), 
                'Loan_default'[Age Groups] = "Adults",
                    ALLEXCEPT('Loan_default', 
                    'Loan_default'[Age],
                    'Loan_default'[Age Groups],
                    'Loan_default'[CreditScore], 
                    'Loan_default'[Credit Score Bins]))
    ```
- TotalLoan (MiddleAgeAdults)
    ```DAX
    TotalLoan (MiddleAgeAdults) = 
    SUMX(FILTER('Loan_default', 
                'Loan_default'[Age Groups] = "Middle Age Adults"), 
        'Loan_default'[LoanAmount])
    ```
- NLoans by EduType
    ```DAX
    NLoans by EduType =
    COUNTROWS(FILTER('Loan_default', NOT(ISBLANK('Loan_default'[LoanID]))))
    ```
- YOY LoanAmount Change
    ```DAX
    YOY LoanAmount Change = 
    DIVIDE(
        CALCULATE(SUM('Loan_default'[LoanAmount]), 
                    'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))) - 
        CALCULATE(SUM('Loan_default'[LoanAmount]),
                    'Loan_default'[Year] = YEAR(max('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),

        CALCULATE(SUM('Loan_default'[LoanAmount]),
                    'Loan_default'[Year] = YEAR(max('Loan_default'[Loan_Date_DD_MM_YYYY]))-1) * 100,0)
    ```
- YOY DefaultLoansChange
    ```DAX
    YOY DefaultLoansChange = 
    DIVIDE(
        CALCULATE(COUNTROWS(FILTER('Loan_default',
                            'Loan_default'[Default] = TRUE())), 
                'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))) -
                CALCULATE(COUNTROWS(FILTER('Loan_default', 
                            'Loan_default'[Default] = TRUE())), 
                'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1)
                ,
        CALCULATE(COUNTROWS(FILTER('Loan_default', 
                            'Loan_default'[Default] = TRUE())), 
                'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1), 0)
                * 100
    ```
- YTD Loan Amount
    ```DAX
    YTD Loan Amount = 
    CALCULATE(SUM('Loan_default'[LoanAmount]), 
                DATESYTD('Loan_default'[Loan_Date_DD_MM_YYYY].[Date]),
                ALLEXCEPT('Loan_default', 
                        'Loan_default'[MaritalStatus], 
                        'Loan_default'[Credit Score Bins]))
    ```

---

## Data Visualization & Analysis

### Key Insights

#### Loan Amount by Purposes
- Loan Amount by Purpose
    - Home loans have the highest total loan amount (~6545M), indicating strong demand and larger borrowing amounts in the housing sector.
- Average Income by Employment Type
    - Full-time employees have the highest average income (~82.9K), indicating stronger earning stability.
- Default Rate(%) by Employment Type
    - Unemployed, followed by Part-time employees (~3.01%) borrowers have the highest default rate (~3.39%), indicating greater financial risk. Default rates decrease as employment stability increases.
- Average Loan Amount by Age Groups
    - Adults (20 yrs - 40 yrs) have the highest average loan amount (~127.9K), indicating stronger borrowing capacity and higher financing demand. Adults appear to be the primary segment for larger loan financing.
- Default Rate(%) by Years
    - 2016 recorded the highest default rate (~11.75%), indicating a slight increase in loan repayment risk during that year. 2014 and 2017 had the lowest default rates (~11.50%).

#### Applicant Demographics & Financial Profile
- Median Loan Amount by Credit Score Categories
    - Borrowers with Low credit scores have the highest median loan amount (~128.4K). Borrowers with High credit scores have the lowest median loan amount (~127.1K). Higher loan amounts among lower credit score borrowers may indicate increased lending risk exposure.
- Avg Loan Amt (High Credit) by Age Groups and Marital Status
    - Adult Singles have the highest average loan amount (~128.35K), while Teen Married borrowers show the lowest (~125.69K). Marital status has limited impact on loan amount among high-credit borrowers. High-credit borrowers generally maintain consistent borrowing behavior regardless of demographic group.
- Total Loan (Adults) by Cred Score Bins
    - Borrowers with Medium and High credit scores hold the largest total loan amounts (~4.6bn and ~4.5bn), while borrowers with low credit scores contribute the smallest total loan amount (~1.1bn). Higher loan concentration among medium/high credit borrowers may help reduce overall default exposure.
- Total Loan (Middle Age Adults) by has Mortgage/Dependents
    - Total loan amounts are nearly identical across all mortgage and dependent categories, ranging from approximately 3.12bn to 3.14bn.
- No of Loans by Education Type
    - Borrowers with a Bachelor’s degree have the highest number of loans (~64.4K). Master’s and PhD borrowers show slightly lower but nearly identical loan counts (~63.5K). Individuals with undergraduate education appear to represent the largest borrowing segment.

#### Financial Risk Metrics
- YOY Loan Amount Change by Year
    - Overall, loan amount growth fluctuated significantly across the years, reflecting changing lending trends and market conditions.
- YOY Default Loans Change by Year
    - Overall, default loan changes fluctuated heavily across the years, reflecting unstable credit risk trends.
- YTD Loan Amount by Credit Score Bins and Marital Status
    - Borrowers with Medium and High credit scores hold the highest YTD loan amounts across all marital statuses. The lending portfolio is concentrated more heavily on borrowers with stronger credit profiles regardless of marital status.

#### Discovered Insights
- Default Rate(%) by Age Groups and Education
    - Teen borrowers have the highest default rates across all education levels, indicating the highest financial risk segment. Borrowers with High School education generally show the highest default rates within each age group. Higher education appears associated with lower default risk across all age groups.
- Default Rate(%) by Income Groups
    - Income level appears to be a strong factor influencing loan repayment behavior and default risk.
- Default Rate(%) by DTI Groups
    - DTI ratio appears to be an important indicator of borrower financial stability.
- Sum of Loan Amount by DTI Group and Interest Rate Groups
    - The highest loan concentration is observed among borrowers with Medium DTI and High Interest Rates (~1.39bn). Borrowers with High DTI have lower total loan amounts compared to Medium and Low DTI groups.

---

## Conclusion

This project demonstrates how business intelligence and data analytics can be applied to evaluate loan repayment behavior, borrower financial profiles, and lending risks. The analysis reveals that employment stability, income level, credit score, DTI ratio, and age groups play significant roles in influencing default risk and borrowing behavior.

The findings show that borrowers with stable employment and stronger credit profiles generally present lower default risks, while younger borrowers, unemployed individuals, and borrowers with high DTI ratios demonstrate higher financial risk exposure. Additionally, lending activity is heavily concentrated among medium and high credit score borrowers, helping reduce overall portfolio risk.

Through PowerBI dashboards and analytical reporting, this project provides actionable insights that can support financial institutions in improving credit risk assessment, borrower segmentation, and lending decision strategies.

### Recommendations:
- Apply stricter loan approval policies for borrowers with high DTI ratios and low credit scores.
- Monitor unemployed and part-time borrowers more carefully due to their higher default rates.
- Encourage risk-based interest rate strategies to balance profitability and default exposure.
- Develop targeted financial education programs for younger borrowers to improve repayment behavior.
- Prioritize lending toward financially stable borrower groups with strong credit profiles.
- Implement predictive risk models to identify high-risk borrowers earlier.
- Continuously monitor year-over-year lending and default trends to improve portfolio management.
- Use interactive BI dashboards for real-time business monitoring and decision-making.

---

## Tools Used

- Snowflake (Cloud Data Warehouse)
- PowerBI (Data Visualization & Dashboarding)
- SQL (Data Preparation & Querying)
- Microsoft Excel (Raw Data Source)

---

## Contact

If you have feedback or opportunities, feel free to connect:

* [LinkedIn](https://www.linkedin.com/in/htet-aung-lynn-64ba06146/)
* [GitHub](https://github.com/htetaunglynn94)
