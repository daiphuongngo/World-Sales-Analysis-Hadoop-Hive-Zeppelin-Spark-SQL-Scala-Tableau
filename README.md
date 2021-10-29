# World-Sales-Analysis-Zeppelin-Spark-Scala-SQL

## Overview:

## Dataset:

worldsales.csv

## Language and Tools:

- Hadoop

- HDFS

- Hive

- Zeppelin

- Spark

- Scala

- SQL

## Table of Content:

### 1. Load data

#### Load data into HDFS

Upload the file worldsales.csv to HDFS’s tmp folder

![Screenshot 2021-06-23 214558](https://user-images.githubusercontent.com/70437668/139509148-a72afb57-ab13-4537-98bf-bcbf8550fbe6.png)

#### Load data into Zeppelin

```
// Create a worldsales DataFrame from CSV file
%spark2
val worldsales = (spark.read 
.option("header", "true") // Use first line as header
.option("inferSchema", "true") // Infer schema
.csv("/tmp/worldsales.csv"))
```

![image](https://user-images.githubusercontent.com/70437668/139509201-3073af0b-df1d-4353-96f3-f7b1847942c9.png)

### 2.	Show the newly created dataframe 

```
%spark2
worldsales.select(“Id”, “Region”, “Country”, “Item_Type”, “Sales_Channel”, “Order_Priority”, “Order_Date”, “Order_ID”, “Ship_Date”, “Units_Sold”, “Unit_Price”, “Unit_Cost”, “Total_Revenue”, “Total_Cost”, “Total Profit”).show()
```

![Screenshot 2021-06-23 220234](https://user-images.githubusercontent.com/70437668/139509159-ef5ddbc4-ac14-40a1-8c47-ccaf69de97cf.png)

### 3. Print the dataframe schema  
```
%spark2
// Print the Schema in a tree format
worldsales.printSchema()
```

![Screenshot 2021-06-23 220419](https://user-images.githubusercontent.com/70437668/139509177-65606a0a-ea5e-4744-bca0-7305d455637e.png)

### 4. Filter the dataframe to show units sold > 8000 and unit cost > 500

#### Method 1:
```
%spark2
// Create a Dataset containing worldsales with units sold and unit price using “filter”
val worldsales_dataset = worldsales.select((“Id”, “Region”, “Country”, “Item_Type”, “Sales_Channel”, “Order_Priority”, “Order_Date”, “Order_ID”, “Ship_Date”, “Units_Sold”, “Unit_Price”, “Unit_Cost”, “Total_Revenue”, “Total_Cost”, “Total Profit”)
					.filter($”Units_Sold” > 8000)
					.filter($”Unit_Cost” > 500)
worldsales_dataset.show()
```

#### Method 2:
```
worldsales.filter("Units_Sold" > 8000 && "Unit_Cost" > 500).show()
```

![Screenshot 2021-06-23 221000](https://user-images.githubusercontent.com/70437668/139509184-81b9419d-24f9-4c01-a3c6-fe5505de97e0.png)

### 5.	Show the dataframe in group by “Region” and count
```
%spark2
worldsales.groupBy(“region”).count().show()
```

![Q5 ](https://user-images.githubusercontent.com/70437668/139508998-7629e595-4100-4179-8564-fb2307e26dce.png)


### 6.	Show the dataframe in group by “Region” and count
```
%spark2
worldsales.groupBy(“region”).count().show()
```

![Q5 ](https://user-images.githubusercontent.com/70437668/139509006-da033a7a-a815-4dce-8e07-a3ec880d4655.png)


### 7.	Save the new subset dataframe as a CSV file into HDFS
```
%spark2
worldsales_results.coalesce(1).write.format(“csv”).option(“header”, “true”).save(“/tmp/worldsales_results.csv”)
```

![Q7 results](https://user-images.githubusercontent.com/70437668/139509021-3041b3e1-2a11-40fe-a45b-abbb61765275.png)

### 8.	Create two views using the “createOrReplaceTempView” command

#### 8.a.	View on “Salesview” from the first dataframe
```
%spark2
worldsales.createOrReplaceTempView(“Salesview”)
```

![Q8 1](https://user-images.githubusercontent.com/70437668/139509034-16f1823f-ccdb-4621-89bd-b3ba4c6b670d.png)

#### 8.b.	View on “Regionview” from the second dataframe 
```
%spark2
Worldsales_results.createOrReplaceTempView(“Regionview”)
```

![Q8 2](https://user-images.githubusercontent.com/70437668/139509039-11506d7e-3399-4a63-9173-4b2b3c6e9870.png)

### 9.	Using SQL select all from “Regionview” view and show in a line graph 
```
%spark2.sql
SELECT * FROM Regionview
```

![Q9](https://user-images.githubusercontent.com/70437668/139509047-e583dd08-7f36-4922-a06f-b80e80ad643a.png)

### 10.	Using SQL select from the “Salesview” view – the region and sum of units sold and group by region  and display in a data grid view 
```
%spark2.sql
SELECT region, SUM(Units_Sold) AS Sum_Units_Sold
FROM Salesview
GROUP BY region
```

![Q10](https://user-images.githubusercontent.com/70437668/139509057-dad4d738-e4d3-4a9e-ae11-a5bca4cf4ac8.png)

### 11.	Using SQL select from the “Salesview” view – the region and sum of total_profit and group by region and display in a Bar chart 
```
%spark2.sql
SELECT region, SUM(Total_Profit)
FROM Salesview
GROUP BY region
```

![Q11](https://user-images.githubusercontent.com/70437668/139509063-26305d3c-b2cf-4dd5-8e89-7a8e6b3ffa65.png)

### 12.	Using SQL select from the “Salesview” view – show the total profit as profit, the total revenue as revenue and the total cost as cost from “Salesview” group by region – The client wants to see this data in a line graph so as to see the correlation between cost ,revenue ,profit between regions.
```
%spark2.sql
SELECT region, SUM(Total_Profit) AS Profit, SUM(total_revenue) AS Revenue, SUM(total_cost) AS Cost
FROM Salesview
GROUP BY region
```

![Q12 have to drag ](https://user-images.githubusercontent.com/70437668/139509085-149fe26e-79df-4b6b-b6e8-2af0d3a9b983.png)

![Q12](https://user-images.githubusercontent.com/70437668/139509090-ac994010-86a9-4954-b104-1680483bf250.png)

### 13.	The customer is in the process of opening up a new store an they are looking at the best location to do so, they need to see the avg profit in each region as a percentage (pie chart) compared to other regions, please use both views created to demonstrate answer also point out the region where it is most profitable. 
```
%spark2.sql
SELECT a.Region, AVG(Total_Profit) 
FROM Salesview b , Regionview a
WHERE a.Region = b.Region
GROUP BY a.Region
```
--> Europe's Average Profit at 27% is the highest among all continents.

![Q13 - Orren's solution on pie chart](https://user-images.githubusercontent.com/70437668/139509118-49ccd97a-31a5-40bf-9f35-bd668d27e906.png)




