# Carbon Emission Analysis
## 1. Introduction
### What is the project about?
This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

Carbon emissions play a crucial role in the environment, accounting for over 75% of global emissions and posing a significant environmental challenge. These emissions contribute to the accumulation of greenhouse gases in the atmosphere, leading to climate change, planetary warming, and involvement in various environmental disasters.

Through this analysis, we hope to gain an understanding of the environmental impact of different industries and contribute to making informed decisions in sustainable development.

### Data Source: Where Our Data Comes From
Our dataset is compiled from publicly available data from nature.com and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent).

### Data Structure
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

<img src="https://github.com/BeccaHuynh/Carbon-emission-analysis/blob/main/database_diagram.png?raw=true" alt="alt text" width="400" />

## 2. Carbon emission over the years

*First, let's extract total carbon emissions in each year from 2013 to 2017:* 
```sql
SELECT year, SUM(carbon_footprint_pcf) total_carbon
FROM product_emissions
GROUP BY year ORDER BY year ASC
```
The result:
| year | total_carbon | 
| ---: | -----------: | 
| 2013 | 503857       | 
| 2014 | 624226       | 
| 2015 | 10840415     | 
| 2016 | 1640182      | 
| 2017 | 340271       | 

> Overview, there is a fluctuation in carbon footprints (PCFs) from 2013 to 2017. The carbon footprints reach its peak in 2015, at 10840415 pcf and dip to its lowest point in 2017, at 340271 pcf.
> 
> <img src="https://github.com/BeccaHuynh/Carbon-emission-analysis/blob/main/carbon_emissions_linechart.png?raw=true" alt="alt text" width="400" />

## 3. The largest contributors to carbon emissions
### Product
*Creating a list with top 10 products that its manufacturing process emits the most CO2:*
```sql
SELECT pd_e.product_name, in_gr.industry_group
       ,SUM(pd_e.carbon_footprint_pcf) total_carbon
FROM product_emissions pd_e
LEFT JOIN industry_groups in_gr ON in_gr.id= pd_e.industry_group_id
GROUP BY pd_e.product_name
ORDER BY SUM(pd_e.carbon_footprint_pcf) DESC LIMIT 10
```
The result:
| product_name                                                                                                                       | industry_group                     | total_carbon | 
| ---------------------------------------------------------------------------------------------------------------------------------: | ---------------------------------: | -----------: | 
| Wind Turbine G128 5 Megawats                                                                                                       | Electrical Equipment and Machinery | 3718044      | 
| Wind Turbine G132 5 Megawats                                                                                                       | Electrical Equipment and Machinery | 3276187      | 
| Wind Turbine G114 2 Megawats                                                                                                       | Electrical Equipment and Machinery | 1532608      | 
| Wind Turbine G90 2 Megawats                                                                                                        | Electrical Equipment and Machinery | 1251625      | 
| TCDE                                                                                                                               | Materials                          | 198150       | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | Automobiles & Components           | 191687       | 
| Retaining wall structure with a main wall (sheet pile) | Materials                          | 167000       | 
| Electric Motor                                                                                                                     | Capital Goods                      | 160655       | 
| Audi A6                                                                                                                            | Automobiles & Components           | 111282       | 
| Average of all GM vehicles produced and used in the 10 year life-cycle.                                                            | Automobiles & Components           | 100621       | 

>The wind turbines in this dataset have the largest carbon emissions during production but provide the greatest long-term environmental benefits by generating clean energy. Automobiles and industrial machinery have lower initial emissions but contribute to ongoing carbon pollution during use. The data reflects the carbon intensity of producing large, resource-heavy products across various sectors.

> <img src="https://imagenes.elpais.com/resizer/v2/DKDCIQ6RHYXBL6ELHFWOBH63OY.jpg?auth=95ddcb4576a39c99c540fabbeaa696f785b370c0f002f093ce5d6242024091d0&width=1960" alt="Description of the image" width="500"/>

*Let's extract the top 10 products with the largest carbon emissions per kilogram:*
```sql
SELECT pd_e.product_name, in_gr.industry_group
      ,ROUND(pd_e.carbon_footprint_pcf/pd_e.weight_kg,2) carbon_per_kg
FROM product_emissions pd_e
LEFT JOIN industry_groups in_gr ON in_gr.id= pd_e.industry_group_id
GROUP BY pd_e.product_name 
ORDER BY pd_e.carbon_footprint_pcf/pd_e.weight_kg DESC 
LIMIT 10
```
The result
| product_name                                                                                                                                                                                                                                                                                                                                                                            | industry_group                                   | carbon_per_kg | 
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | -----------------------------------------------: | ------------: | 
| Small Rack Mount Switch                                                                                                                                                                                                                                                                                                                                                                 | Technology Hardware & Equipment                  | 736.5         | 
| Alliance (HPLC)                                                                                                                                                                                                                                                                                                                                                                         | "Pharmaceuticals, Biotechnology & Life Sciences" | 681.61        | 
| "ACQUITY® UPLC, ACQUITY® I-Class, ACQUITY® H-Class"                                                                                                                                                                                                                                                                                                                                     | "Pharmaceuticals, Biotechnology & Life Sciences" | 659.23        | 
| Bloomberg's standard-issue flat panel configuration (prior to 2010) | Media                                            | 646.44        | 
| Bloomberg-Provided Flat Panel Unit                                                                                                                                                                                                                                                                                                                                                      | Media                                            | 646.44        | 
| Electric Motor                                                                                                                                                                                                                                                                                                                                                                          | Capital Goods                                    | 589.53        | 
| Mobile Network Equipment                                                                                                                                                                                                                                                                                                                                                                | Technology Hardware & Equipment                  | 532.83        | 
| BlackBerry Priv                                                                                                                                                                                                                                                                                                                                                                         | Technology Hardware & Equipment                  | 416.67        | 
| BlackBerry Z10                                                                                                                                                                                                                                                                                                                                                                          | Technology Hardware & Equipment                  | 414.55        | 
| BlackBerry Bold 9900                                                                                                                                                                                                                                                                                                                                                                    | Telecommunication Services                       | 400           | 
> - The products in Technology Hardware & Equipment and Life Sciences sectors have the highest carbon footprints, largely due to the energy-intensive processes involved in their production and their complex designs. 
> - Media products, while not as high, still contribute significantly when viewed across their lifecycle. It's crucial to consider how long products are used to contextualize their environmental impact.
> - Mobile devices show relatively lower carbon footprints per unit, but given their mass production and short lifecycle (frequent replacements), they still have a considerable cumulative environmental impact.
### Industry group
> <img src="https://github.com/BeccaHuynh/Carbon-emission-analysis/blob/main/machine_industry.png" alt="Carbon Emission Analysis" width="500"/>

*Next, let's find out what are the industries with the highest contribution to carbon emissions.*
```sql
SELECT in_gr.industry_group, SUM(pd_e.carbon_footprint_pcf) total_carbon
FROM product_emissions pd_e
LEFT JOIN industry_groups in_gr ON in_gr.id= pd_e.industry_group_id
GROUP BY in_gr.industry_group
ORDER BY SUM(pd_e.carbon_footprint_pcf) DESC 
LIMIT 1
```
The result:
| industry_group                     | total_carbon | 
| ---------------------------------: | -----------: | 
| Electrical Equipment and Machinery | 9801558      | 
> Improving sustainability in these industries could have a notable impact on reducing overall emissions.


We will use similar SQL querries to see which companies and countries is the largest emitter of carbon dioxide gas.

### Company
```sql
SELECT comp.company_name, SUM(pd_e.carbon_footprint_pcf) total_carbon
FROM product_emissions pd_e
LEFT JOIN companies comp ON comp.id= pd_e.company_id
GROUP BY comp.company_name
ORDER BY SUM(pd_e.carbon_footprint_pcf) DESC
LIMIT 1
```
The result:
| company_name                           | total_carbon | 
| -------------------------------------: | -----------: | 
| Gamesa Corporación Tecnológica, S.A. | 9778464      | 
> While Gamesa’s total carbon emissions are substantial, it’s important to weigh these numbers against the carbon reductions their products enable. Renewable energy infrastructure tends to have a high carbon cost at the beginning, but it plays a vital role in decarbonizing the energy sector over time.
### Country
```sql
SELECT ctr.country_name, SUM(pd_e.carbon_footprint_pcf) total_carbon
FROM product_emissions pd_e
LEFT JOIN countries ctr ON ctr.id= pd_e.country_id
GROUP BY ctr.country_name
ORDER BY SUM(pd_e.carbon_footprint_pcf) DESC
LIMIT 1
```
The result:
| country_name | total_carbon | 
| -----------: | -----------: | 
| Spain        | 9786130      | 


## 4. Production stage
```sql
WITH production_stage AS
  (SELECT upstream_percent_total_pcf*carbon_footprint_pcf/100 up, 
         operations_percent_total_pcf*carbon_footprint_pcf/100 op,
	     downstream_percent_total_pcf*carbon_footprint_pcf/100 down
   FROM product_emissions)
SELECT ROUND(SUM(up),2) upstream_total_pcf , 
       ROUND(SUM(op),2) operations_stream_total_pcf, 
	   ROUND(SUM(down),2) downstream_total_pcf
FROM production_stage
```
The result:
| upstream_total_pcf | operations_stream_total_pcf | downstream_total_pcf | 
| -----------------: | --------------------------: | -------------------: | 
| 294738.59          | 213619.52                   | 853500.63            | 
> - Downstream Total PcF is the highest in the table, indicating that post-production phases—especially product usage and disposal—are the largest source of carbon emissions. It highlights the need for creating more energy-efficient products, better recycling programs, and working on the end-of-life impact.
> - In order to reduce the carbon emissions in this stage, we should encourage sustainable product use (e.g., energy-saving features), improve product lifespan, and develop take-back or recycling programs to minimize disposal impact.
