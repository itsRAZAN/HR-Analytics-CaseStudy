
# HR Analytics in Power Bi (CASE STUDY)
## 1. Case Study Goal
 The core goal of this case study is to build a report using datasets from a Tech company called Atlas Labs. Atlas Labs HR team want to be able to monitor key metrics on employees. Their secondary goal is to understand what factors impact employee attrition.
 
###  Attrition Definition 
    
Attrition means,the departure of employees from the organization for any reason (voluntary or involuntary), including resignation, termination, death or retirement.

## 2. Data Modeling and Exploratory data analysis (EDA)
In this case study, using the Kimball Model approach we will be working with facts and dimensions to build our model. From our dataset, the fact table stores the Performance Ratings.\
This table contains information about employees yearly reviews and helps Atlas Labs manage their employees performance on a regular basis. This is the central point of our snowflake schema. It contains 11 different columns and has multiple rows per employee.
We have five dimension tables that we will be working with: Employee, EducationLevel, RatingLevel, SatisfiedLevel, and Date.

### 2.1 Building the data model: Date dimension and relating tables
In this stage, we will also model our data. This will enable us to connect our six different tables
The first step we will take is to Create a new calculated table that uses the DAX code.Next we will connect the following tables:

- Connect the DimEmployee table to FactPerfomanceRating through EmployeeID column.

- Connect the DimDate table to FactPerfomanceRating and the DimEmployee table and use the FactPerfomanceRating column as the active connection.

- Connect the DimEducationLevel table to the DimEmployee table.

- Connect FactPerfomanceRating table columns (EnvironmentSatisfaction, JobSatisfaction, RelationshipSatisfaction and WorkLifeBalance) to DimSatisfactionLevel table and use EnvironmentSatisfaction as the active connection.

- Connect FactPerfomanceRating table columns (SelfRating, ManagerRating) to DimRatingLevel and use the SelfRating column as the active connection.

### 2.2 Exploring the data
We will explore our data and produce high level metrics to understand the attrition at the company.First, we will create a new table called _Measures to store all the measures. Then display these measures using the card visual in our overview page. Let’s create the measures below:

- A measure to take count of all the employees

```ruby
TotalEmployees = DISTINCTCOUNT(DimEmployee[EmployeeID])
```
- Measures that take count of all employees that are currently active or inactive
```ruby
ActiveEmployee = CALCULATE([TotalEmployees], DimEmployee[Attrition] = "No")
```
```ruby
InactiveEmployee = CALCULATE([TotalEmployees], DimEmployee[Attrition] = "Yes")
```
- Attrition Rate based on the measures we have created above.
```ruby
% Attrition Rate = [InactiveEmployee]/[TotalEmployees]
```
![1](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/74b996de-cba6-46f1-9cfe-b10c6fd2e152)


### 2.3 Hiring trends over time
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

![2](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/b12969e8-e56e-4829-aa2b-0cfb01ac1488)


### 2.4 Analyzing departments and Job Roles
The next step, we will look into the typical roles department managers are hiring into the organization. This will enable every department to plan for new hiring requests in the future.\
To show ActiveEmployees by Department we Create a clustered bar chart 

![3](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/dcfecd88-a6c6-4e0b-b9ed-4f95fe1df0ba)

Then we add a treemap visualization to show Active employees by department and job role. 


![4](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/abf835dc-fbc9-4d99-b753-bf665ab5d46d)


### Keys Insights so far:
- Atlas Labs has employed over 1,470 people.
- Atlas Labs currently employs over 1,200 people.
- The attrition rate for employees leaving the organization is 16.1%.
- The largest department is Technology with Software Engineer as the most common title in the Technology team.

## 3. Analyzing Demographics and Performance
In this section Atlas Labs would like some answers on its employee demographics and performance to develop a good understanding of the diversity and inclusion metrics.

We know that the attrition rate for Atlas Labs is 16%. This opens up more questions on attrition for the company and what ultimately impacts employee happiness. In this case study, we'll finish with a deeper understanding of what factors impact employee attrition.

### 3.1 Demoaraphics: ade and aender
Now that we have a good understanding of the general
hiring trends, It's Important to look at the next layer of key HR metrics that focus on Diversity and Inclusion we will focus on employee age and gender .

Below we create a card visuals that display  the minimum and maximum value of the employees age.

![5](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/f50c0c03-cbc7-4746-9c38-ef4bb039c457)


To take a closer look at the age distribution of employees we will create a conditional column called AgeBins that separates employees' ages by bins in the following structure: <\20, 20-29, 30-39, 40-49, 50>.

```ruby
= Table.AddColumn(#"Changed Type", "AgeBins", each if [Age] >= 50 then "50>" else if [Age] >= 40 then "40-49" else if [Age] >= 30 then "30-39" else if [Age] >= 20 then "20-29" else "<20")
```
To helps Us Identify patterns across the varlous age and Gender. we use 100% Stacked column chart as shown below.

![6](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/c84bba22-674c-4298-98d8-f7952535a659)


We add a page level filter that enables us to look at the report page based on whether an employee is currently active or not.

### 3.2 Demographics: marital status and ethnicity
Second, we will be looking at further employee information regarding marital status and ethnicity. We will visualize the count of all employees by marital status.


![7](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/a60585e8-18d4-4cd9-b700-fe8b2214c3d9)

Next, we will create a new measure, AverageSalary which works out the average salary of all employees.

```ruby
AverageSalary = AVERAGE(DimEmployee[Salary])
```

Below is a visualization that displays the count
of all employees and their average salary by Ethnicity.

![8](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/8ee02c05-8e6c-4222-b3b2-03c66e0897a9)


### 3.3 Performance Tracker 1

The HR Team would like to have a view where they
can continually track an individual employee's
performance scores based on their yearly performance
reviews.

We will Create a slicer with a calculated column called FullName in the DimEmployee table that combines FirstName and LastName which has single select and search enabled. We will add a card which shows:
 - selected employee 
 - Hire date
 - last review date ( a measure that gets the last performance review for the selected individual)
 - next review date ( a measure that calculates when
the next review is due. It should be 365 days after the
LastReviewDate 
```ruby
FullName = DimEmployee[FirstName] & " " & DimEmployee[LastName]
```
```ruby
LastReviewDate = if(MAX(FactPerformanceRating[ReviewDate])=BLANK(),"No Review Yet",MAX(FactPerformanceRating[ReviewDate] ))
```
```ruby
NextReviewDate = 
VAR reviewOrHire =
    IF (
        MAX ( FactPerformanceRating[ReviewDate] ) = BLANK (),
        MAX ( DimEmployee[HireDate] ),
        MAX ( FactPerformanceRating[ReviewDate] )
    )
RETURN
    reviewOrHire + 365
```

![9](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/bb0c4d4f-ae56-4678-b39b-64f34833401e)


### 3.4 Performance Tracker 2

Next we would like to take a closer look at the employees review ratings by create a satisfaction measures and visualise them by year.
- JobSatisfaction
- EnvironmentSatisfaction
- RelationshipSatisfaction
- WorkLifeBalance
```ruby
JobSatisfaction = MAX(FactPerformanceRating[JobSatisfaction])
```
```ruby
EnvironmentSatisfaction = CALCULATE(
MAX(FACTPerformanceRating[EnvironmentSatisfaction]),
USERELATIONSHIP(FACTPerformanceRating[EnvironmentSatisfaction], DimSatisfiedLevel[SatisfactionID]))
```
```ruby
RelationshipSatisfaction = CALCULATE(
MAX(FACTPerformanceRating
[RelationshipSatisfaction]),
USERELATIONSHIP(FACTPerformanceRating
[RelationshipSatisfaction], DimSatisfiedLevel[SatisfactionID])
)
```
```ruby
WorkLifeBalance = CALCULATE(
MAX(FACTPerformanceRating
[WorkLifeBalance]),
USERELATIONSHIP(FACTPerformanceRating
[WorkLifeBalance], DimSatisfiedLevel[SatisfactionID])
)
```
We will also Create two more measures
- SelfRating 
- ManagerRating 
```ruby
SelfRating = 
CALCULATE (
    MAX ( FactPerformanceRating[SelfRating] ),
    USERELATIONSHIP ( FactPerformanceRating[SelfRating], DimRatingLevel[RatingID] )
)
```
```ruby
ManagerRating = 
CALCULATE (
    MAX ( FactPerformanceRating[ManagerRating] ),
    USERELATIONSHIP ( FactPerformanceRating[ManagerRating], DimRatingLevel[RatingID] )
)
```

![10](https://github.com/itsRAZAN/HR-Analytics-CaseStudy/assets/128379502/bec02b90-b4c4-485b-aa7d-00ae234483e2)


### More Insights uncovered:
- Majority of employees are between 20-29 years.
- Atlas Labs employees 2.7% more women than men.
- Employees who identify as Non Binary makeup 8.5% of total employees.
- White have the highest average salary.
- Mixed or multiple ethnic groups have one of the lowest average salaries.


## 4. Attrition Overview

In this section we'll focusing on dellvering insights on attrition and what factors affect employee retention to deliver a user-friendly report.

### 4.1 understanding Attrition 

We will create a card visual that show Attrition Rate and visualization that displays the
% Attrition Rate for each department and job role, and visualization to understand our attrition rate based on
our HireDate by create a measure on InactiveEmployees.
```ruby
InactiveEmployeesDate = CALCULATE([InactiveEmployee], USERELATIONSHIP(DimEmployee[HireDate], DimDate[Date]))
```

![11](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/87e92761-5226-4b54-957f-908e12269fe1)


### 4.2 Other factors that impact attrition

Now that we have some Initial Insight Into which
department see the most turnover of employees, we can add additional context to see If we can figure out what other factors affect attrittion.
The next visuallzations display:
- % Attrition Rate by Travel Frequency
- % Attrition Rate by Overtime Requirement
- % Attrition Rate by Tenure (years working for Atlas Labs)
 
![12](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/16f2d044-54c0-4ea6-8dae-43e29606a4e0)



The Atlas Labs HR team can now easily
navigate key metrics around the company
employees. This will enable the company to
make more informed decisions from
measuring hiring to monitoring diversity and
inclusion metrics, as well monitor employee
performance and attrition.

![DashBoard](https://github.com/itsRAZAN/HR_Analytics_CaseStudy/assets/128379502/5816a26e-ea63-421e-aa67-f6204befc827)


