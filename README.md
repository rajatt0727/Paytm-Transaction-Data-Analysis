
# Paytm Transactions Dashboard

## Problem Statement
**How can we enhance customer retention, optimize revenue generation, and better understand user behavior across different demographics, cities, and time periods in Paytm's transaction platform?**

This dashboard addresses these key questions by providing insights on:
1. Total transaction values and revenue.
2. Retention rates and user segmentation (new, retained, and churned users).
3. Transaction behavior across cities, user age groups, and time slots.
4. A deep dive into revenue per transaction and transaction types.

## Dashboard Components
1. **Total Transaction Value**: Aggregates the total value of transactions across the platform.
2. **Total Revenue**: Displays the total revenue generated from these transactions.
3. **Retained Users, New Users, and Churned Users**: Shows user behavior over time with clear segmentation.
4. **Average Transaction Value**: Provides insight into the average value per transaction.
5. **Transaction Trends by Day of the Week**: Highlights when users are most active.
6. **Transaction Value by Age Group**: Visualizes transaction behavior by age demographic.
7. **User Funnel**: Tracks the user journey from total users to retained and transacting users.
8. **Retention Rate**: Shows the percentage of users retained over time.
9. **Revenue per Transaction by Type**: Compares revenue generation across transaction types.

```markdown
# DAX Measures and Calculations

## DAX Functions

### New Measure

1. **Total Transaction Value**  
   `Total Transaction Value = SUM(Paytm[Transaction Value])`

2. **Total Revenue**  
   `Total Revenue = SUM(Paytm[Revenue])`

3. **Retained Users**  
   ```DAX
   Retained Users = CALCULATE(
       COUNT(Paytm[User ID]),
       Paytm[User Retention] = "Retained"
   )
   ```

4. **New Users**  
   ```DAX
   New Users = CALCULATE(
       COUNT(Paytm[User ID]),
       Paytm[User Retention] = "New"
   )
   ```

5. **Churned Users**  
   ```DAX
   Churned Users = CALCULATE(
       COUNT(Paytm[User ID]),
       Paytm[User Retention] = "Churned"
   )
   ```

6. **Avg Transaction Value**  
   `Avg Transaction Value = AVERAGE(Paytm[Transaction Value])`

7. **Retention Rate**  
   ```DAX
   Retention Rate = DIVIDE(
       CALCULATE(COUNT(Paytm[User ID]), Paytm[User Retention] = "Retained"),
       COUNT(Paytm[User ID]),
       0
   ) * 100
   ```

8. **Transactions by City**  
   `Transactions by City = COUNT(Paytm[Transaction ID])`

9. **Revenue per Transaction**  
   `Revenue per Transaction = DIVIDE(SUM(Paytm[Revenue]), COUNT(Paytm[Transaction ID]))`

10. **Revenue by User Type**  
   ```DAX
   Revenue by User Type = CALCULATE(
       SUM(Paytm[Revenue]),
       Paytm[User Type] = "New" || Paytm[User Type] = "Returning"
   )
   ```

11. **Users Who Transacted**  
   ```DAX
   Users Who Transacted = CALCULATE(
       COUNT(Paytm[User ID]), 
       Paytm[Transaction ID] <> BLANK()
   )
   ```

---

## New Columns

1. **Transaction Day**  
   `Transaction Day = WEEKDAY(Paytm[Date], 2)`  
   (Monday as 1, Sunday as 7)

2. **Age Group**  
   ```DAX
   Age Group = SWITCH(
       TRUE(),
       Paytm[User Age] <= 25, "Young",
       Paytm[User Age] > 25 && Paytm[User Age] <= 45, "Middle-Aged",
       Paytm[User Age] > 45, "Senior"
   )
   ```

3. **Time Slot**  
   ```DAX
   Time Slot = SWITCH(
       TRUE(),
       Paytm[Time of Day] < 12, "Morning",
       Paytm[Time of Day] >= 12 && Paytm[Time of Day] < 18, "Afternoon",
       Paytm[Time of Day] >= 18, "Evening"
   )
   ```

---

## New Table

1. **User Funnel**  
   ```DAX
   User Funnel = UNION(
       SELECTCOLUMNS(Paytm, "Stage", "Total Users", "Value", COUNT(Paytm[User ID])),
       SELECTCOLUMNS(Paytm, "Stage", "Users Who Transacted", "Value", COUNT(Paytm[Transaction ID])),
       SELECTCOLUMNS(Paytm, "Stage", "Retained Users", "Value", COUNT(Paytm[User ID]))
   )
   ```
```
## How to Use
This dashboard provides a comprehensive view of user and transaction trends across the platform. You can filter the data based on city, age group, time of day, or transaction type to get a more granular view of user behavior.

**Suggested Actions**:
- Use the Retention Rate card to monitor churn and identify factors contributing to user retention.
- Analyze the `Transactions by City` and `Revenue per Transaction by Type` charts to optimize marketing strategies and promotions across different regions and transaction types.
