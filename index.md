![Alt text](images/logo_hc_ta.png?raw=true "Title")

![image](https://user-images.githubusercontent.com/41034062/92675057-2a8cea00-f351-11ea-8454-6ad7881162a8.png)
## SAP HANA GRAPH Overview

A Graph is based on Nodes and Edges. In our dataset, a Node will represent a Customer or a Merchant and an Edge will present a Transaction which happens between a customer and a Merchant. By visualizing the Transaction network graph , we can see the clusters between merchants & customers, observer anomalies , check the patterns of a Fraudulent Transaction etc.

![image](https://user-images.githubusercontent.com/41034062/93108557-85259d80-f6e5-11ea-94e1-b79a38b0545b.png)

### Hands-On: Building Graph space 

### Prepare Graph Dataset 
For this exercise we picked 10 random Merchants from July transactions. Since we don’t have Merchant name column in our dataset, we are adding them manually for this exercise. We will create CARD_TRAN table for these 10 Merchants and pick 100 transactions for each. Also, we will add a merchant name. 


```markdown

create table CARD_TRAN as ( 

select top 100 *,'TGT' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='7010' union 

select top 100 *,'WAL' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='7011' union 

select top 100 *,'APL' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='6927' union 

select top 100 *,'MAC' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='6059' union 

select top 100 *,'UNI' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='7238' union 

select top 100 *,'MOB' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='5655' union 

select top 100 *,'PEP' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='5877' union 

select top 100 *,'COK' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='5209' union 

select top 100 *,'AZ' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='6604' union 

select top 100 *,'VN' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='7897' 

); 
```


### Create a Join of the Transactions data with the Merchant & customer master data:

We are adding customer and merchant details. 

```markdown

create table CARD_TRAN_FULL as (select * from CARD_TRAN a left outer join SOURCEDATA.MERCHANT_CA b on a.CT_MER_ID=b.MER_ID left outer join SOURCEDATA.CUSTOMER c on c.CUST_ID=a.CT_CUST_ID);
```
### Create NODES 
Creating nodes table and identify node attributes such as type, county, gender etc. 

```markdown
CREATE COLUMN TABLE "NODES"( 
"node_id" NVARCHAR(100) NOT NULL,
"name" NVARCHAR(100),
"type" NVARCHAR(100), 
"county" NVARCHAR(100),
"gender" NVARCHAR(100), 
PRIMARY KEY ( 
"node_id" 
) 
); 
```

### Insert NODES Data
Populating Nodes data using 2 SQL statements. First we are inserting merchants (using mer_name as node_id so its easier to navigate in graph viewer), then we are adding customers 
```markdown
insert into  "NODES" select distinct "MER_NAME" "node_id", "MER_NAME" "name", 'Merchant' "type", "COUNTY", '' "gender" from CARD_TRAN_FULL; 

insert into  "NODES" select distinct "CUST_ID" "node_id", "CUST_NAME" "name", 'Customer' "type", '' "COUNTY", "CUST_SEX" "gender" from CARD_TRAN_FULL; 
```
### Create EDGES table: 

Create edges table and add edge attributes such as transaction datetime, fraud flag, dayname and amount.
```markdown
CREATE COLUMN TABLE "EDGES"( 
"edge_id" INTEGER NOT NULL, 
"fraud_flag" INTEGER, 
"amount" DECIMAL(12,2), 
"trans_datetime" seconddate, 
"dayname" nvarchar(100), 
"cust_id" NVARCHAR(100) NOT NULL, 
"mer_id" NVARCHAR(100) NOT NULL, 
PRIMARY KEY ( 
"edge_id" 
) 
); 
```
### Add foreign keys to Edges that references Nodes: 
Add constraints on the generated CARD_TRAN table as this is a requirement imposed by the SAP HAAN Graph model

```markdown

ALTER TABLE "EDGES" ADD FOREIGN KEY ( "cust_id" ) REFERENCES "NODES" ("node_id") ON UPDATE CASCADE ON DELETE CASCADE ENFORCED VALIDATED ; 

ALTER TABLE "EDGES" ADD FOREIGN KEY ( "mer_id" ) REFERENCES "NODES" ("node_id") ON UPDATE CASCADE ON DELETE CASCADE ENFORCED VALIDATED ; 
```

### Insert Edge data (transactions) :
```markdown
insert into EDGES select "CT_ID" "edge_id", "CT_FLAG_FRAUD" "fraud_flag", "CT_AMOUNT" "amount", "CT_DATE" "trans_datetime", dayname("CT_DATE") "dayname", "CT_CUST_ID" "cust_id", "MER_NAME" "mer_id" from CARD_TRAN_FULL; 
```


### CREATE GRAPH
```markdown
 

create graph workspace "GRAPH_1" 
edge table "EDGES" 
source column "cust_id" 
target column "mer_id" 
key column "edge_id" 
vertex table "NODES" 
key column "node_id" ; 

 ```
### Displaying Graph
Goto Catalog --> Graph Workspace

![image](https://user-images.githubusercontent.com/41034062/93111458-3d087a00-f6e9-11ea-8dd6-e453f0fc6057.png)

•	Right click graph workspaces and select show graph workspaces

![image](https://user-images.githubusercontent.com/41034062/93111622-77721700-f6e9-11ea-85d0-abd4ab58720a.png)


Find the workspace in your schema and right click and select Open :

![image](https://user-images.githubusercontent.com/41034062/93111706-8f499b00-f6e9-11ea-8133-122781e3ef42.png)

•	It will show the details of the EDGE and VERTEX tables

![image](https://user-images.githubusercontent.com/41034062/93111796-b607d180-f6e9-11ea-8747-4677f482fddf.png)


•	Click Glasses icon to open graph viewer

![image](https://user-images.githubusercontent.com/41034062/93111832-c61fb100-f6e9-11ea-870b-2e4a4e9b2e4d.png)

Randomly selected 200 vertexes (nodes) will be shown

![image](https://user-images.githubusercontent.com/41034062/93112088-21ea3a00-f6ea-11ea-9b9a-845dc2707586.png)

Click Filter Select Vertex
Column = type
Operator = =
Value = Merchant

Click Add Then Apply -->

![image](https://user-images.githubusercontent.com/41034062/93112194-46461680-f6ea-11ea-8361-b89dc7724b1f.png)


Only Merchants will be displayed:


![image](https://user-images.githubusercontent.com/41034062/93112276-61b12180-f6ea-11ea-8540-e1f976db33a6.png)

Clear all to delete the filter and click Apply
Graph will go back to normal

![image](https://user-images.githubusercontent.com/41034062/93112558-bb195080-f6ea-11ea-9f71-7ce0f066c17f.png)

![image](https://user-images.githubusercontent.com/41034062/93112477-a8068080-f6ea-11ea-8f48-d9e11785623d.png)

Switch to Edge filter
Add
Dayname = SUNDAY
Apply to show all transactions happened on Sunday

![image](https://user-images.githubusercontent.com/41034062/93112626-cff5e400-f6ea-11ea-8ea8-0875ea1afdb7.png)

Switch to preferences
Edge tab
Fraud_flag 1 
Select color
Apply

![image](https://user-images.githubusercontent.com/41034062/93112754-f1ef6680-f6ea-11ea-92cc-260aa110b218.png)

Fraud_flag = 1 edges will be highlitghed in the color selected.

![image](https://user-images.githubusercontent.com/41034062/93112897-1a776080-f6eb-11ea-9d65-981ac737fa62.png)

Separate VN and AZ from the group

![image](https://user-images.githubusercontent.com/41034062/93113099-56aac100-f6eb-11ea-9f45-7e7404ee2527.png)

Expand both VN and AZ
Select 177363
Show Edge details
Note this customer purchased item in both stores.

![image](https://user-images.githubusercontent.com/41034062/93113217-7e018e00-f6eb-11ea-8c36-b8be292dd487.png)

