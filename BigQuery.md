# Wonder Natality Database


**Number of Counties Recorded in Database**

```sql
SELECT Count (DISTINCT County_of_Residence) AS Number_of_Counties
from bigquery-public-data.sdoh_cdc_wonder_natality.county_natality
```
*Result :*

![cbd2](https://user-images.githubusercontent.com/88874864/131240192-f8245d32-da01-4c38-b73f-e779f6fd9c30.PNG)


**Total Number of births in different counties over the years 2016,2017 and 2018**

```sql
SELECT County_of_Residence AS County, SUM(Births) AS Births
FROM bigquery-public-data.sdoh_cdc_wonder_natality.county_natality
group by County_of_Residence
order by Births DESC 
LIMIT 1000
```
*Result :*

![cbd1](https://user-images.githubusercontent.com/88874864/131238171-bcb6e5f2-b697-4211-afaf-0cce706c7028.PNG)


**Average Number of Pre-Natal Visits made in each county over the years**

```sql
SELECT County_of_Residence AS County, AVG(Ave_Number_of_Prenatal_Wks) AS Prenatal_Visits
FROM bigquery-public-data.sdoh_cdc_wonder_natality.county_natality
group by County
order by Prenatal_Visits DESC 
LIMIT 1000
```
*Result :*

![cbd3](https://user-images.githubusercontent.com/88874864/131240303-e6d8043a-07a0-4075-b67a-951f9a4b9ab8.PNG)




**Gestational Age(in Weeks) for maximum and minimum birth weights(in gms)**

```sql
with max_weight AS
(
select Ave_LMP_Gestational_Age_Wks,Ave_Birth_Weight_gms
FROM bigquery-public-data.sdoh_cdc_wonder_natality.county_natality
 AS t1
inner join
(
  select max(Ave_Birth_Weight_gms) as max_avg_weight
  FROM bigquery-public-data.sdoh_cdc_wonder_natality.county_natality t1
) 
AS t2
  on t1.Ave_Birth_Weight_gms = t2.max_avg_weight
),

min_weight as
(
select Ave_LMP_Gestational_Age_Wks,Ave_Birth_Weight_gms
FROM bigquery-public-data.sdoh_cdc_wonder_natality.county_natality
 AS t3
inner join
(
  select min(Ave_Birth_Weight_gms) as min_avg_weight
  FROM bigquery-public-data.sdoh_cdc_wonder_natality.county_natality t1
) 
AS t4
  on t3.Ave_Birth_Weight_gms = t4.min_avg_weight
)

SELECT * FROM max_weight 

UNION ALL

SELECT * FROM min_weight 
 
```
*Result :*

![cbd 5](https://user-images.githubusercontent.com/88874864/131245497-22cfad73-9873-4051-8369-89b5c82eac46.PNG)




**Finding Counties where Private Insuarance mode is most used**

```sql
with private_insuarance AS
(
SELECT County_of_Residence, Source_of_Payment_Code, SUM(Births) AS total_births_by_private_insuarance
From bigquery-public-data.sdoh_cdc_wonder_natality.county_natality_by_payment
WHERE  Source_of_Payment_Code = 2
Group by County_of_Residence, Source_of_Payment_Code
),
Births_in_counties AS
(
SELECT County_of_Residence, AVG(Births) AS avg_births 
From bigquery-public-data.sdoh_cdc_wonder_natality.county_natality_by_payment
Group by County_of_Residence
)

SELECT private_insuarance.County_of_Residence, Source_of_Payment_Code, total_births_by_private_insuarance ,avg_births
FROM  private_insuarance 
join Births_in_counties
on private_insuarance.County_of_Residence=Births_in_counties.County_of_Residence
WHERE total_births_by_private_insuarance>avg_births

ORDER by total_births_by_private_insuarance DESC
```
*Result :*

![cbd4](https://user-images.githubusercontent.com/88874864/131241362-fe52c019-a06e-4148-82ba-d65b0c73b9bd.PNG)
