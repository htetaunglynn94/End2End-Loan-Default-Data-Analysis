
# Project Title

A brief description of what this project does and who it's for


# 📊 End2End-Loan-Default-Data-Analysis (Snowflake + PowerBI Project)

## Project Overview

This project analyzes **Student Depression Data** to identify key factors influencing mental health among students. The goal is to uncover patterns, correlations, and risk factors contributing to depression using interactive visualizations.

The analysis focuses on understanding how **academic pressure, lifestyle habits, financial stress, and study satisfaction** impact student well-being.

**Tableau Report**: [Student Depression Data Analysis.twb](https://public.tableau.com/shared/YRGK8CCQD?:display_count=n&:origin=viz_share_link)

---

## Objectives

- Enable interactive filtering across multiple dimensions (Gender, Age Group, Diet, Sleep, etc.)
- Analyze depression patterns by:
  - Academic Pressure
  - Study Satisfaction
  - Financial Stress
  - Sleep Duration
  - Dietary Habits
- Compare depression rates across demographic groups
- Identify high-risk student segments
- Provide actionable insights for improving student mental health

---

## Dataset Information

**Raw Data**: [Student Depression Data.xlsx](https://github.com/user-attachments/files/27337330/Student.Depression.Data.xlsx) 

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

- KPI indicators (Total Students, Depression Rate)
- Interactive filters (Gender, Age Group, Lifestyle factors)
- Bar charts and percentage distributions
- Comparative analysis across categories

---

## Data Processing Steps

### 1️⃣ Data Preparation

- Imported dataset into Microsoft SQL Server for data manipulation purpose
- Verified and cleaned data types
- Created `Age Group` using SQL script:

```SQL
ALTER TABLE [Student Depression Data]
ADD [Age Group] AS
    CASE
        WHEN Age BETWEEN 18 AND 24 THEN 'A1'
        WHEN Age BETWEEN 25 AND 30 THEN 'A2'
        ELSE 'A3'
    END;
```
- Change `Depression` column from **boolean** to **categorical**

```SQL
UPDATE [Student Depression Data]
SET Depression = CASE
    WHEN Depression = '1' THEN 'Yes'
    ELSE 'No'
END
```

- Exported all result as `.xlsx` and imported to Tableau Public
- Created calculated fields where necessary

---

### 2️⃣ Feature Engineering

#### Age Group Segmentation

| Age Range | Group |
|----------|------|
| 18–24    | A1   |
| 25–30    | A2   |
| > 30     | A3   |

---

## Data Visualization & Analysis

### Key Insights

#### Loan Amount by Purposes
- Home loans have the highest total loan amount (~6545M), indicating strong demand and larger borrowing amounts in the housing sector.
- Full-time employees have the highest average income (~82.9K), indicating stronger earning stability.
- **Unemployed**, followed by **Part-time employees** (~3.01%) borrowers have the highest default rate (~3.39%), indicating greater financial risk. Default rates decrease as employment stability increases.
- Adults (20 yrs - 40 yrs) have the highest average loan amount (~127.9K), indicating stronger borrowing capacity and higher financing demand. Adults appear to be the primary segment for larger loan financing.
- 2016 recorded the highest default rate (~11.75%), indicating a slight increase in loan repayment risk during that year. 2014 and 2017 had the lowest default rates (~11.50%).


#### Applicant Demographics & Financial Profile
- Borrowers with Low credit scores have the highest median loan amount (~128.4K). Borrowers with High credit scores have the lowest median loan amount (~127.1K). Higher loan amounts among lower credit score borrowers may indicate increased lending risk exposure.
- Adult Singles have the highest average loan amount (~128.35K), while Teen Married borrowers show the lowest (~125.69K). Marital status has limited impact on loan amount among high-credit borrowers. High-credit borrowers generally maintain consistent borrowing behavior regardless of demographic group.
- Borrowers with Medium and High credit scores hold the largest total loan amounts (~4.6bn and ~4.5bn), while borrowers with low credit scores contribute the smallest total loan amount (~1.1bn). Higher loan concentration among medium/high credit borrowers may help reduce overall default exposure.
- Total loan amounts are nearly identical across all mortgage and dependent categories, ranging from approximately 3.12bn to 3.14bn.
- Borrowers with a Bachelor’s degree have the highest number of loans (~64.4K). Master’s and PhD borrowers show slightly lower but nearly identical loan counts (~63.5K). Individuals with undergraduate education appear to represent the largest borrowing segment.

#### Financial Risk Metrics
- Overall, loan amount growth fluctuated significantly across the years, reflecting changing lending trends and market conditions.
- Overall, default loan changes fluctuated heavily across the years, reflecting unstable credit risk trends.
- Borrowers with Medium and High credit scores hold the highest YTD loan amounts across all marital statuses. The lending portfolio is concentrated more heavily on borrowers with stronger credit profiles regardless of marital status.


---

## Conclusion

The findings from this analysis demonstrate that student depression is a complex and multi-dimensional issue. Academic pressure emerges as the most critical factor, followed by financial stress and study dissatisfaction.

While lifestyle factors such as diet and sleep contribute to mental health outcomes, their impact is relatively secondary compared to academic and financial influences.

### Recommendations:
- Reduce excessive academic pressure
- Provide financial support mechanisms
- Improve study engagement and satisfaction
- Encourage healthy lifestyle habits

---

## Tools Used

- Tableau Public (Data Visualization & Dashboarding)
- Microsoft SQL Server (Data Preparation)
- Microsoft Excel (Data Source)

---

## Contact

If you have feedback or opportunities, feel free to connect:

* [LinkedIn](https://www.linkedin.com/in/htet-aung-lynn-64ba06146/)
* [GitHub](https://github.com/htetaunglynn94)

--
