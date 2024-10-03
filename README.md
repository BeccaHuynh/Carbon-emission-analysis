# Carbon-emission-analysis
## 1. Carbon emission over the years
Overview, there is a fluctuation in carbon footprints (PCFs) from 2013 to 2017. The carbon footprints reach its peak in 2015, at 10840415 pcf and dip to its lowest point in 2017.

```sql
SELECT year, SUM(carbon_footprint_pcf)
FROM product_emissions
GROUP BY year
ORDER BY year ASC
```
**The result:**
| year | SUM(carbon_footprint_pcf) | 
| ---: | ------------------------: | 
| 2013 | 503857                    | 
| 2014 | 624226                    | 
| 2015 | 10840415                  | 
| 2016 | 1640182                   | 
| 2017 | 340271                    | 

## 2. The highest contributor to carbon emissions
###- Product

```sql
SELECT pd_e.product_name, in_gr.industry_group, MAX(pd_e.carbon_footprint_pcf) highest_carbon_footprint_pcfs
FROM product_emissions pd_e
LEFT JOIN industry_groups in_gr
ON in_gr.id= pd_e.industry_group_id
WHERE pd_e.carbon_footprint_pcf= (SELECT MAX(carbon_footprint_pcf) FROM product_emissions)
```
**The result:**
| product_name                 | industry_group                     | highest_carbon_footprint_pcfs | 
| ---------------------------: | ---------------------------------: | ----------------------------: | 
| Wind Turbine G128 5 Megawats | Electrical Equipment and Machinery | 3718044                       | 

###- Industry group**

```sql
SELECT in_gr.industry_group, SUM(pd_e.carbon_footprint_pcf)
FROM product_emissions pd_e
LEFT JOIN industry_groups in_gr
ON in_gr.id= pd_e.industry_group_id
GROUP BY in_gr.industry_group
ORDER BY SUM(pd_e.carbon_footprint_pcf) DESC
LIMIT 1
```
**The result:**
| industry_group                     | SUM(pd_e.carbon_footprint_pcf) | 
| ---------------------------------: | -----------------------------: | 
| Electrical Equipment and Machinery | 9801558                        | 

###- Company
  
```sql
SELECT comp.company_name, SUM(pd_e.carbon_footprint_pcf)
FROM product_emissions pd_e
LEFT JOIN companies comp
ON comp.id= pd_e.company_id
GROUP BY comp.company_name
ORDER BY SUM(pd_e.carbon_footprint_pcf) DESC
LIMIT 1
```
| company_name                           | SUM(pd_e.carbon_footprint_pcf) | 
| -------------------------------------: | -----------------------------: | 
| "Gamesa Corporación Tecnológica, S.A." | 9778464                        | 

###- Country

```sql
SELECT ctr.country_name, SUM(pd_e.carbon_footprint_pcf)
FROM product_emissions pd_e
LEFT JOIN countries ctr
ON ctr.id= pd_e.country_id
GROUP BY ctr.country_name
ORDER BY SUM(pd_e.carbon_footprint_pcf) DESC
LIMIT 1
```
| country_name | SUM(pd_e.carbon_footprint_pcf) | 
| -----------: | -----------------------------: | 
| Spain        | 9786130                        | 
