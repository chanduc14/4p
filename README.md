# 4p


CR1:1)

Create or Replace temp View cr_ons_fscsoli_delta as SELECT DISTINCT case when (fssoli.Legacy_Company_Code = 'USM0' or fssoli.Source_System_Key>=8000) then 'FED' else 'NONFED' end as order_fed_qualification,sotyp.Sales_Order_Type_Key,sotyp.Sales_Order_Type_Code,sotyp.Sales_Order_Type_Description,CASE when fssoli.Sales_Order_Trade_Intercompany_Indicator ='Trade' then 'Y' else 'N' end AS Sales_Order_Type_Trade_Revenue_Flag,sotyp.Sales_Order_Type_Quota_Credit_Flag,fssoli.Supply_Chain_Sales_Order_Line_Item_Key,fssoli.Legacy_Business_Area_Code,dmeml.Material_Product_Line_Code as Sales_Order_Source_System_Product_Line_Id,fssoli.Sales_Order_Unit_Price_Sales_Order_Document_Amount,fssoli.Sales_Order_Item_Plant_Code,fssoli.Sales_Order_Header_Customer_Required_Delivery_Date,fssoli.Sales_Order_Detail_Bundle_Type_Code,fssoli.Customer_Product_ID,fssoli.Sales_Order_End_Customer_Locator_Id,fssoli.Sales_Order_End_Customer_Relationship_Profile_Id,fssoli.Sales_Order_Line_Cancel_Date,dmpl.Plant_Code as Sales_Order_Plant_Description,          fssoli.Sales_Order_Reference_Model_Id,fssoli.Sales_Order_End_Customer_Customer_Reference_Server_Id, dmeml.Material_Product_Line_Description as Sales_Order_Source_System_Product_Description ,fssoli.Sales_Order_Supplying_Division_Sub_Entity_Code,fssoli.Sales_Order_Sales_Channel_Code,fssoli.Sales_Order_Item_Material_Account_Assignment_Group_Code,fssoli.Sales_Order_Business_Dunning_Block_Code, CASE WHEN fssoli.Sales_Order_Item_Ship_To_Country_Code IS NULL OR TRIM(fssoli.Sales_Order_Item_Ship_To_Country_Code)='' THEN '?' ELSE fssoli.Sales_Order_Item_Ship_To_Country_Code END as Sales_Order_Item_Ship_To_Country_Code   ,fssoli.Sales_Order_Payer_Company_Cutomer_Id,fssoli.Sales_Order_Direct_Customer_Indicator_Code,fssoli.Sales_Order_Distribution_Channel_Code 
FROM TERA_CSC.FACT_ENTERPRISE_SUPPLY_CHAIN_ORDER_DETAIL_Incremental_OnS_Delta fssoli 
left join TERA_CSC.DIM_ENTERPRISE_SALES_ORDER_TYPE_WORKING sotyp  
on fssoli.Sales_Order_Type_Key=sotyp.Sales_Order_Type_Key                           
left join TERA_CSC.Dim_Plant dmpl 
on dmpl.Plant_Code=fssoli.Sales_Order_Item_Plant_Code
left join TERA_CSC.dim_enterprise_material dmeml 
on dmeml.Material_Number =fssoli.Product_Id 

2)Create or Replace temp View cr_ons_fscsoli as SELECT DISTINCT case when (fssoli.Legacy_Company_Code = 'USM0' or fssoli.Source_System_Key>=8000) then 'FED' else 'NONFED' end as order_fed_qualification,sotyp.Sales_Order_Type_Key,sotyp.Sales_Order_Type_Code,sotyp.Sales_Order_Type_Description,CASE when fssoli.Sales_Order_Trade_Intercompany_Indicator ='Trade' then 'Y' else 'N' end AS Sales_Order_Type_Trade_Revenue_Flag,sotyp.Sales_Order_Type_Quota_Credit_Flag,fssoli.Supply_Chain_Sales_Order_Line_Item_Key,fssoli.Legacy_Business_Area_Code,dmeml.Material_Product_Line_Code as Sales_Order_Source_System_Product_Line_Id,fssoli.Sales_Order_Unit_Price_Sales_Order_Document_Amount,fssoli.Sales_Order_Item_Plant_Code,fssoli.Sales_Order_Header_Customer_Required_Delivery_Date,fssoli.Sales_Order_Detail_Bundle_Type_Code,fssoli.Customer_Product_ID,fssoli.Sales_Order_End_Customer_Locator_Id,fssoli.Sales_Order_End_Customer_Relationship_Profile_Id,fssoli.Sales_Order_Line_Cancel_Date,dmpl.Plant_Code as Sales_Order_Plant_Description,          fssoli.Sales_Order_Reference_Model_Id,fssoli.Sales_Order_End_Customer_Customer_Reference_Server_Id, dmeml.Material_Product_Line_Description as Sales_Order_Source_System_Product_Description ,fssoli.Sales_Order_Supplying_Division_Sub_Entity_Code,fssoli.Sales_Order_Sales_Channel_Code,fssoli.Sales_Order_Item_Material_Account_Assignment_Group_Code,fssoli.Sales_Order_Business_Dunning_Block_Code, CASE WHEN fssoli.Sales_Order_Item_Ship_To_Country_Code IS NULL OR TRIM(fssoli.Sales_Order_Item_Ship_To_Country_Code)='' THEN '?' ELSE fssoli.Sales_Order_Item_Ship_To_Country_Code END as Sales_Order_Item_Ship_To_Country_Code   ,fssoli.Sales_Order_Payer_Company_Cutomer_Id,fssoli.Sales_Order_Direct_Customer_Indicator_Code,fssoli.Sales_Order_Distribution_Channel_Code
FROM TERA_CSC.fact_enterprise_supply_chain_order_detail fssoli 
left join TERA_CSC.DIM_ENTERPRISE_SALES_ORDER_TYPE_WORKING sotyp 
on fssoli.Sales_Order_Type_Key=sotyp.Sales_Order_Type_Key 
left join TERA_CSC.Dim_Plant dmpl 
on dmpl.Plant_Code=fssoli.Sales_Order_Item_Plant_Code 
left join TERA_CSC.dim_enterprise_material dmeml
on dmeml.Material_Number =fssoli.Product_Id


3)
Create or Replace temp View cr_ons_fscsosli_delta as SELECT DISTINCT case when fsosli.Sales_Order_Source_System_Key>=8000 then 'FED' else 'NONFED' END as shipment_fed_qualification,fsosli.Order_Line_Item_Key,fsosli.Shipment_Line_Item_Key,fsosli.Shipment_Line_Item_Id,trim(fsosli.Shipment_Id) as Shipment_Id, trim(fsosli.Sales_Order_Id) as Sales_Order_Id,cast(fsosli.Sales_Order_Line_Item_Id as int ) as Sales_Order_Line_Item_Id,CASE WHEN fsosli.Product_Id IS NULL OR TRIM(fsosli.Product_Id)='' THEN '?' ELSE fsosli.Product_Id END as Product_Id, case when instr(fsosli.Product_Id,'#') > 1 then substr(fsosli.Product_Id,1,instr(fsosli.Product_Id,'#')-1) else fsosli.Product_Id end as Product_id_without_localization ,fsosli.Shipment_Date, concat(substring (string (fsosli.shipment_date), 1,4),substring (string (fsosli.shipment_date), 6,2),'00') Ship_Year_Month_Identifier,concat(substring (string (fsosli.Sales_Order_Create_Date), 1,4),substring (string (fsosli.Sales_Order_Create_Date), 6,2),'00') Order_Year_Month_Identifier,fsosli.Shipment_Billing_Document_Id,fsosli.Shipment_Base_Quantity,fsosli.Sales_Order_Legacy_Profit_Center_Code,fsosli.Sales_Order_End_Customer_Id,fsosli.Sales_Order_Record_Explosion_Code,fsosli.Sales_Order_Gross_US_Dollar_Amount,fsosli.Shipment_Item_Plant_Code,fsosli.Shipment_Item_Legacy_Plant_Code,fsosli.Shipment_Header_Delivery_Type_Code,fsosli.Shipment_Header_Inbound_Transport_Means_Type_Code,fsosli.Shipment_Header_Transportation_Route_Duration_Code,fsosli.Sales_Order_Sales_Geography_Code,fsosli.Original_Product_Id,fsosli.Shipment_Third_Party_Product_Id,case when fsosli.Shipment_Return_Item_Flag In ('X','Y') then 'Y' else case when fsosli.Shipment_Return_Item_Flag='N' then 'N' else 'N' end end as Shipment_Return_Item_Flag,CASE WHEN fsosli.Shipment_Header_Proof_Of_Delivery_Date IS NULL OR TRIM(fsosli.Shipment_Header_Proof_Of_Delivery_Date)='' OR fsosli.Shipment_Header_Proof_Of_Delivery_Date='?' or fsosli.Shipment_Header_Proof_Of_Delivery_Date='9999-12-31' or fsosli.Shipment_Header_Proof_Of_Delivery_Date='1900-01-01' THEN b.Delivery_Events_Event_End_Time ELSE fsosli.Shipment_Header_Proof_Of_Delivery_Date end as Shipment_Header_Proof_Of_Delivery_Date 
FROM TERA_CSC.FACT_ENTERPRISE_SUPPLY_CHAIN_ORDER_SHIPMENT_DETAIL_Incremental_OnS_Delta fsosli 
LEFT join TERA_CSC.Fact_Delivery_Events b 
on fsosli.Delivery_Item_Handle_For_Time_Segment_Header=b.Delivery_Events_Handle_For_Time_Segment_Header 


4)Create or Replace temp View cr_ons_fscsosli as SELECT DISTINCT case when fsosli.Sales_Order_Source_System_Key>=8000 then 'FED' else 'NONFED' END as shipment_fed_qualification,fsosli.Order_Line_Item_Key,fsosli.Shipment_Line_Item_Key,fsosli.Shipment_Line_Item_Id,trim(fsosli.Shipment_Id) as Shipment_Id, trim(fsosli.Sales_Order_Id) as Sales_Order_Id,cast(fsosli.Sales_Order_Line_Item_Id as int ) as Sales_Order_Line_Item_Id,CASE WHEN fsosli.Product_Id IS NULL OR TRIM(fsosli.Product_Id)='' THEN '?' ELSE fsosli.Product_Id END as Product_Id, case when instr(fsosli.Product_Id,'#') > 1 then substr(fsosli.Product_Id,1,instr(fsosli.Product_Id,'#')-1) else fsosli.Product_Id end as Product_id_without_localization ,fsosli.Shipment_Date, concat(substring (string (fsosli.shipment_date), 1,4),substring (string (fsosli.shipment_date), 6,2),'00') Ship_Year_Month_Identifier,concat(substring (string (fsosli.Sales_Order_Create_Date), 1,4),substring (string (fsosli.Sales_Order_Create_Date), 6,2),'00') Order_Year_Month_Identifier,fsosli.Shipment_Billing_Document_Id,fsosli.Shipment_Base_Quantity,fsosli.Sales_Order_Legacy_Profit_Center_Code,fsosli.Sales_Order_End_Customer_Id,fsosli.Sales_Order_Record_Explosion_Code,fsosli.Sales_Order_Gross_US_Dollar_Amount,fsosli.Shipment_Item_Plant_Code,fsosli.Shipment_Item_Legacy_Plant_Code,fsosli.Shipment_Header_Delivery_Type_Code,fsosli.Shipment_Header_Inbound_Transport_Means_Type_Code,fsosli.Shipment_Header_Transportation_Route_Duration_Code,fsosli.Sales_Order_Sales_Geography_Code,fsosli.Original_Product_Id,fsosli.Shipment_Third_Party_Product_Id,case when fsosli.Shipment_Return_Item_Flag In ('X','Y') then 'Y' else case when fsosli.Shipment_Return_Item_Flag='N' then 'N' else 'N' end end as Shipment_Return_Item_Flag,
CASE WHEN fsosli.Shipment_Header_Proof_Of_Delivery_Date IS NULL OR TRIM(fsosli.Shipment_Header_Proof_Of_Delivery_Date)='' OR fsosli.Shipment_Header_Proof_Of_Delivery_Date='?' or fsosli.Shipment_Header_Proof_Of_Delivery_Date='9999-12-31' or fsosli.Shipment_Header_Proof_Of_Delivery_Date='1900-01-01' THEN b.Delivery_Events_Event_End_Time ELSE fsosli.Shipment_Header_Proof_Of_Delivery_Date end as Shipment_Header_Proof_Of_Delivery_Date 
FROM TERA_CSC.FACT_ENTERPRISE_SUPPLY_CHAIN_ORDER_SHIPMENT_DETAIL fsosli 
LEFT join TERA_CSC.Fact_Delivery_Events b
on fsosli.Delivery_Item_Handle_For_Time_Segment_Header = b.Delivery_Events_Handle_For_Time_Segment_Header
 --- inner join with shipment and delta order table-----

5)Create or Replace temp View cr_ons_cscons_order as select CURRENT_TIMESTAMP() as Insert_GMT_Timestamp,CURRENT_TIMESTAMP() As Source_System_Update_Timestamp, cr_ons_fscsosli.*,cr_ons_fscsoli_delta.* from cr_ons_fscsoli_delta 
inner join cr_ons_fscsosli 
on cr_ons_fscsoli_delta.Supply_Chain_Sales_Order_Line_Item_Key = cr_ons_fscsosli.Order_Line_Item_Key 

6)Create or Replace temp View cr_ons_cscons_ship as select CURRENT_TIMESTAMP() as Insert_GMT_Timestamp,CURRENT_TIMESTAMP() As Source_System_Update_Timestamp,cr_ons_fscsosli_delta.*,cr_ons_fscsoli.* from cr_ons_fscsosli_delta 
inner join cr_ons_fscsoli on cr_ons_fscsoli.Supply_Chain_Sales_Order_Line_Item_Key = cr_ons_fscsosli_delta.Order_Line_Item_Key
WHERE not exists (select 1 From cr_ons_cscons_order
WHERE cr_ons_cscons_order.Order_Line_Item_Key=cr_ons_fscsosli_delta.Order_Line_Item_Key)
 
7)INSERT overwrite TERA_CSC.CSC_COMPLIANCE_REPORTING_ONS_DENORMALIZED_TBL_ENTERPRISE_INCREMENTAL_OnS_Delta 
SELECT Insert_GMT_Timestamp, Source_System_Update_Timestamp,shipment_fed_qualification,Order_Line_Item_Key,Shipment_Line_Item_Key,Shipment_Line_Item_Id,Shipment_Id,Sales_Order_Id,Sales_Order_Line_Item_Id,Product_Id,Product_id_without_localization,Shipment_Date,Ship_Year_Month_Identifier,Order_Year_Month_Identifier,Shipment_Billing_Document_Id,Shipment_Base_Quantity,Sales_Order_Legacy_Profit_Center_Code,Sales_Order_End_Customer_Id,Sales_Order_Record_Explosion_Code,Sales_Order_Gross_US_Dollar_Amount,Shipment_Item_Plant_Code,Shipment_Item_Legacy_Plant_Code,Shipment_Header_Delivery_Type_Code,Shipment_Header_Inbound_Transport_Means_Type_Code,Shipment_Header_Transportation_Route_Duration_Code,Sales_Order_Sales_Geography_Code,Original_Product_Id,Shipment_Third_Party_Product_Id,Shipment_Return_Item_Flag,Shipment_Header_Proof_Of_Delivery_Date,order_fed_qualification,Sales_Order_Type_Key,Sales_Order_Type_Code,Sales_Order_Type_Description,Sales_Order_Type_Trade_Revenue_Flag,Sales_Order_Type_Quota_Credit_Flag,Supply_Chain_Sales_Order_Line_Item_Key,Legacy_Business_Area_Code,Sales_Order_Source_System_Product_Line_Id,Sales_Order_Unit_Price_Sales_Order_Document_Amount,Sales_Order_Item_Plant_Code,Sales_Order_Header_Customer_Required_Delivery_Date,Sales_Order_Detail_Bundle_Type_Code,Customer_Product_ID,Sales_Order_End_Customer_Locator_Id,Sales_Order_End_Customer_Relationship_Profile_Id,Sales_Order_Line_Cancel_Date,Sales_Order_Plant_Description,Sales_Order_Reference_Model_Id,Sales_Order_End_Customer_Customer_Reference_Server_Id,Sales_Order_Source_System_Product_Description,Sales_Order_Supplying_Division_Sub_Entity_Code,Sales_Order_Sales_Channel_Code,Sales_Order_Item_Material_Account_Assignment_Group_Code,Sales_Order_Business_Dunning_Block_Code,Sales_Order_Item_Ship_To_Country_Code,Sales_Order_Payer_Company_Cutomer_Id,Sales_Order_Direct_Customer_Indicator_Code,Sales_Order_Distribution_Channel_Code FROM cr_ons_cscons_order  


8)INSERT INTO 
TERA_CSC.CSC_COMPLIANCE_REPORTING_ONS_DENORMALIZED_TBL_ENTERPRISE_INCREMENTAL_OnS_Delta 
SELECT Insert_GMT_Timestamp, Source_System_Update_Timestamp,shipment_fed_qualification,Order_Line_Item_Key,Shipment_Line_Item_Key,Shipment_Line_Item_Id,Shipment_Id,Sales_Order_Id,Sales_Order_Line_Item_Id,Product_Id,Product_id_without_localization,Shipment_Date,Ship_Year_Month_Identifier,Order_Year_Month_Identifier,Shipment_Billing_Document_Id,Shipment_Base_Quantity,Sales_Order_Legacy_Profit_Center_Code,Sales_Order_End_Customer_Id,Sales_Order_Record_Explosion_Code,Sales_Order_Gross_US_Dollar_Amount,Shipment_Item_Plant_Code,Shipment_Item_Legacy_Plant_Code,Shipment_Header_Delivery_Type_Code,Shipment_Header_Inbound_Transport_Means_Type_Code,Shipment_Header_Transportation_Route_Duration_Code,Sales_Order_Sales_Geography_Code,Original_Product_Id,Shipment_Third_Party_Product_Id,Shipment_Return_Item_Flag,Shipment_Header_Proof_Of_Delivery_Date,order_fed_qualification,Sales_Order_Type_Key,Sales_Order_Type_Code,Sales_Order_Type_Description,Sales_Order_Type_Trade_Revenue_Flag,Sales_Order_Type_Quota_Credit_Flag,Supply_Chain_Sales_Order_Line_Item_Key,Legacy_Business_Area_Code,Sales_Order_Source_System_Product_Line_Id,Sales_Order_Unit_Price_Sales_Order_Document_Amount,Sales_Order_Item_Plant_Code,Sales_Order_Header_Customer_Required_Delivery_Date,Sales_Order_Detail_Bundle_Type_Code,Customer_Product_ID,Sales_Order_End_Customer_Locator_Id,Sales_Order_End_Customer_Relationship_Profile_Id,Sales_Order_Line_Cancel_Date,Sales_Order_Plant_Description,Sales_Order_Reference_Model_Id,Sales_Order_End_Customer_Customer_Reference_Server_Id,Sales_Order_Source_System_Product_Description,Sales_Order_Supplying_Division_Sub_Entity_Code,Sales_Order_Sales_Channel_Code,Sales_Order_Item_Material_Account_Assignment_Group_Code,Sales_Order_Business_Dunning_Block_Code,Sales_Order_Item_Ship_To_Country_Code,Sales_Order_Payer_Company_Cutomer_Id,Sales_Order_Direct_Customer_Indicator_Code,Sales_Order_Distribution_Channel_Code FROM cr_ons_cscons_ship 

9)delete from TERA_CSC.CSC_COMPLIANCE_REPORTING_ONS_DENORMALIZED_TBL_ENTERPRISE as t1 
where exists (select 1 from TERA_CSC.CSC_COMPLIANCE_REPORTING_ONS_DENORMALIZED_TBL_ENTERPRISE_INCREMENTAL_OnS_Delta t2 
where t1.Ship_Year_Month_Identifier =t2.Ship_Year_Month_Identifier and t1.Order_Line_Item_Key =t2.Order_Line_Item_Key) 


10)INSERT INTO TERA_CSC.CSC_COMPLIANCE_REPORTING_ONS_DENORMALIZED_TBL_ENTERPRISE 
SELECT Insert_GMT_Timestamp, Source_System_Update_Timestamp,shipment_fed_qualification,Order_Line_Item_Key,Shipment_Line_Item_Key,Shipment_Line_Item_Id,Shipment_Id,Sales_Order_Id,Sales_Order_Line_Item_Id,Product_Id,Product_id_without_localization,Shipment_Date,Ship_Year_Month_Identifier,Order_Year_Month_Identifier,Shipment_Billing_Document_Id,Shipment_Base_Quantity,Sales_Order_Legacy_Profit_Center_Code,Sales_Order_End_Customer_Id,Sales_Order_Record_Explosion_Code,Sales_Order_Gross_US_Dollar_Amount,Shipment_Item_Plant_Code,Shipment_Item_Legacy_Plant_Code,Shipment_Header_Delivery_Type_Code,Shipment_Header_Inbound_Transport_Means_Type_Code,Shipment_Header_Transportation_Route_Duration_Code,Sales_Order_Sales_Geography_Code,Original_Product_Id,Shipment_Third_Party_Product_Id,Shipment_Return_Item_Flag,Shipment_Header_Proof_Of_Delivery_Date,order_fed_qualification,Sales_Order_Type_Key,Sales_Order_Type_Code,Sales_Order_Type_Description,Sales_Order_Type_Trade_Revenue_Flag,Sales_Order_Type_Quota_Credit_Flag,Supply_Chain_Sales_Order_Line_Item_Key,Legacy_Business_Area_Code,Sales_Order_Source_System_Product_Line_Id,Sales_Order_Unit_Price_Sales_Order_Document_Amount,Sales_Order_Item_Plant_Code,Sales_Order_Header_Customer_Required_Delivery_Date,Sales_Order_Detail_Bundle_Type_Code,Customer_Product_ID,Sales_Order_End_Customer_Locator_Id,Sales_Order_End_Customer_Relationship_Profile_Id,Sales_Order_Line_Cancel_Date,Sales_Order_Plant_Description,Sales_Order_Reference_Model_Id,Sales_Order_End_Customer_Customer_Reference_Server_Id,Sales_Order_Source_System_Product_Description,Sales_Order_Supplying_Division_Sub_Entity_Code,Sales_Order_Sales_Channel_Code,Sales_Order_Item_Material_Account_Assignment_Group_Code,Sales_Order_Business_Dunning_Block_Code,Sales_Order_Item_Ship_To_Country_Code,Sales_Order_Payer_Company_Cutomer_Id,Sales_Order_Direct_Customer_Indicator_Code,Sales_Order_Distribution_Channel_Code 
FROM TERA_CSC.CSC_COMPLIANCE_REPORTING_ONS_DENORMALIZED_TBL_ENTERPRISE_INCREMENTAL_OnS_Delta



