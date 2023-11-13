
# HR Analytics in Power Bi (CASE STUDY)
## 1.1 Case Study Goal
 The core goal of this case study is to build a report using datasets from a Tech company called Atlas Labs. Atlas Labs HR team want to be able to monitor key metrics on employees. Their secondary goal is to understand what factors impact employee attrition.
 
## 1.2 Attrition Definition 
    
attrition means,the departure of employees from the organization for any reason (voluntary or involuntary), including resignation, termination, death or retirement.

## 2. Data Modeling and Exploratory data analysis (EDA)
In this case study, using the Kimball Model approach we will be working with facts and dimensions to build our model. From our dataset, the fact table stores the Performance Ratings.\
This table contains information about employees yearly reviews and helps Atlas Labs manage their employees performance on a regular basis. This is the central point of our snowflake schema. It contains 11 different columns and has multiple rows per employee.
We have five dimension tables that we will be working with: Employee, EducationLevel, RatingLevel, SatisfiedLevel, and Date.

## 2.1 Building the data model: Date dimension and relating tables
In this stage, we will also model our data. This will enable us to connect our six different tables
The first step we will take is to Create a new calculated table that uses the DAX code.Next we will connect the following tables:

* Connect the DimEmployee table to FactPerfomanceRating through EmployeeID column.

* Connect the DimDate table to FactPerfomanceRating and the DimEmployee table and use the FactPerfomanceRating column as the active connection.

* Connect the DimEducationLevel table to the DimEmployee table.

* Connect FactPerfomanceRating table columns (EnvironmentSatisfaction, JobSatisfaction, RelationshipSatisfaction and WorkLifeBalance) to DimSatisfactionLevel table and use EnvironmentSatisfaction as the active connection.

* Connect FactPerfomanceRating table columns (SelfRating, ManagerRating) to DimRatingLevel and use the SelfRating column as the active connection.

## 2.2 Exploring the data
We will explore our data and produce high level metrics to understand the attrition at the company.First, we will create a new table called _Measures to store all the measures. Then display these measures using the card visual in our overview page. Let’s create the measures below:

* A measure to take count of all the employees

```ruby
TotalEmployees = DISTINCTCOUNT(DimEmployee[EmployeeID])
```
* Measures that take count of all employees that are currently active or inactive
```ruby
ActiveEmployee = CALCULATE([TotalEmployees], DimEmployee[Attrition] = "No")
```
```ruby
InactiveEmployee = CALCULATE([TotalEmployees], DimEmployee[Attrition] = "Yes")
```
* Attrition Rate based on the measures we have created above.
```ruby
% Attrition Rate = [InactiveEmployee]/[TotalEmployees]
```
### pic1

## 2.3 Hiring trends over time
We would like to start with analyzing 'Atlas Labs' hiring trends over time to see where they have had the biggest growth in employees.\
We will create a new measure called TotalEmployeesDate that uses the CALCULATE() function on our TotalEmployees Measure and the USERELATIONSHIP function in the filter.The UseRelationship function allows you to use an inactive relationship by specifying the two related columns.
```ruby
TotalEmployeesDate = 
CALCULATE (
    [TotalEmployees],
    USERELATIONSHIP ( DimEmployee[HireDate], DimDate[Date] )
)
```

Then we Create a stacked column chart to show TotalEmployees by Date and add attrition to the legend on the chart to see the split of employees by active vs inactive and Change the X-axis from continuous to Categorical. 

### PIC2

## 2.4 Analyzing departments and Job Roles
