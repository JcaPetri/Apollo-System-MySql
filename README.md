# Apollo-System-MySql
CREATE DATABASE `systemsdb` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_as_cs */ /*!80016 DEFAULT ENCRYPTION='N' */;

Main Structure of all Microservices
Group: com.apolo
Name: system / articles / persons / users

## System Microservice
**Definitions:**
Contains the system structure of all the software. In this module you need to create all the databases and java classes, enums, interfaces, entities, etc.

The main tables of this module will be in the other databases such as mirrors, but only the data that their need. These mirror tables will be updated via Kafka topics.
> [!IMPORTANT]
> With this mechanism each microservice becames independent of the others. Example: MirSysCompanies, MirSysBusinessUnits, etc.
One important thing, is you make a mirror only of the records that need in the other microservice, not all the records that exists in the System Microservice.

$`\textcolor{blue}{\text{TABLES}}`$ 

**The are two types of tables.**
- **Tbl** is the normal table, where stores the specific software data. This kind of table have relation with others tables of the same kind.

> All tables have the key for each record:
>- **ID** --> is the uniqueidentifier auto generated.
>- **IDNum** --> is the autoincrement number auto generated.

- **Tli** is the list table, in which only enable the IDNum for one Microservice and BusinessUnit. This kind of table do not have any relation with others.

> [!NOTE]
> Tables with their owns ID and IDNum. These are the common tables.
> 
> Tables without owns ID and IDNum, It is generated in SysBaseElements_Tbl.
> 
> Microservice_Tbl has not their own ID and IDNum.

___
### Scope
The Scope are the tables of the software. 

All scope are created in the SysBaseElements_Tbl. If some others properties of a specific record is needed, we add this information in the SysBaseElementOthersFiels_Tbl table.
So with this type of database structure, all the record has a common and specific properties.
> [!TIP]
> In the Article database we can create all the articles with theirs specific properties without create new columns. We avoid have a lot of record with null values.
> 
> In the Supplier database we can create all the client an each one has their own properties.

>[!IMPORTANT]
 Each Scope must have defined a table structure in the software. 
- When you create a Scope is a Table, which could be real or virtual, you need to create it in the sofware structure:
    - EntityStructures_Tbl --> here you set up wich field it has.
    - EntityFieldProperties_Tbl	--> here you set up the properties of each Field. (DataType, Lenght, etc)
    - EntityFieldDefaultValues_Tbl	--> here you set up the default value, when the user do not send it. So the user fill only the changed values.

___
## The Apolo Software Structure are defined by:
***Frontend***
This is the user interface, which call the backend to get the information and business logic.
Example to create an invoice, you need the information of:
> Clients -> call the PersonsMicroservices, which has all the information about the clients/supplier/both.
- The parameters request are: 
    - Endpoint: this is the  address of the Microservice.
    - Group: this can be Clients types, BusinessUnits, etc. This can be an array of Groups.
    - Others parameters defined by the microservice, needed to comply the request.
> Articles -> call the ArticlesMicroservices, which has all information about the articles (things and services) and theirs relations.
- The parameters request are: 
    - Endpoint: this is the  address of the Microservice.
    - Group: this can be articles types, BusinessUnits, etc. This can be an array of Groups.
    - Others parameters defined by the microservice, needed to comply the request.
> Taxes -> call the TaxesMicrosevices, which has all infomation about the taxes subject.
- The parameters request are: 
    - Endpoint: this is the  address of the Microservice.
    - GeneratorID: the seller ID.
    - DestiantionID: the client ID.
    - Others parameters defined by the microservice, needed to comply the request.

***Backend***
This is the logic and where the permanent information are stored.
Each Microservice specializes in a specific task. 
___
### Data Base Structure
***Structure are as follows:***
- ***Used to create the main elements***
    - SysBaseElements_Tbl	--> Contains the diccionary of all system elements of the Microservice.
    - SysBaseElementOthersFiels_Tbl	--> Contains the optional fields/columns of the data elements.
    - SysBaseElementLanguages_Tbl	--> Contains the meaning of the diccionary in other languages.
    - SysBaseElementComments_Tbl	--> Contains one or more comments/details/explains of each record of the diccionary.
    - SysBaseElementKafka_Tbl	--> Contains the relation between the BaseElements and the Kafka Topics, to make updated all the microservices.

- ***Used to create multiples tables***
    - SysRootElements_Tli	--> This is a List Table that contains the other element of the system. Enable the IDNum element to a Microservice and BusinessUnit.
	
 - ***Used to create the software structure***
    - SysEntities_Tli	--> This is a List Table that contains the entities of the system, they can be database tables or java classes. Enable the IDNum element to a Microservice and BusinessUnit.
    - SysEntityFields_Tli	--> This is a List Table that contains the fields or the columns of the entities. Enable the IDNum element to a Microservice and BusinessUnit.
    - SysEntityStructures_Tbl	--> Contains each structure of the database tables or java classes.
    - SysEntityStructureFieldProperties_Tbl	--> Contains the properties of each field or column. They can be DataType, Lenght, etc.
    - SysEntityStructureFieldDefaultValues_Tbl	--> Contains the default value of each field or column. When you set a value for a column, if the user does not enter, the system get from this table the value to enter.

- ***Used to create the Microservices.***
    - SysMicroservices_Tbl	--> Contains the microservices that use the software.
      
- ***Used to create the Companies and their Structures***
    - SysCompanies_Tbl	--> Contains the companies that use the software.
    - SysCompanyRelations_Tbl --> Contains the relations between companies. If you have a economic group in this table can build this structure.
    - SysCompanyMicroservice_Tbl --> Contains the relation between Companies and Microservices.

---
## Detailed explanation of each table.
~~~
 Used to create the main elements
		SysBaseElements_Tbl
			Contains the diccionary of all system elements of the Microservice.
			The rest of the tables only have the IDNum. To determine what a code means, you should consult this table.
			In order for the same IDName word to have different meanings depending on its use, it is defined for a Scope, BusinessUnit and Language.
			To respect all the rules the unique value must be the combination of: Name/Scope/BusinessUnit/Language.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  IDName     		-> is the readable code by the user.
			  ScopeIDn     		-> the Name must be unique for the application Scope, usually a Table.
			  BusinessUnitIDn 	-> the Name must be unique for the BusinessUnit.
			  LanguageIDn 		-> the Name must be unique for Language. This dictionary has a default language defined.
			Important: when you create the element/object in this table, this element does not exits for the software. This table is like a dictionary.
						Only exist when you create the code in the specific table.
				Example: the pampa article is created in the dictionary, but it does not exist until it is created in the Articles table.
			Modification Rules:
				You can change the Name if there is a spelling error. Example you have an spelling error in a invoce and must be invoice.
				Warning: If I change the code that represents the word Invoice and it is an afip receipt. And I put food, everywhere the code is, food will start to appear.
				If you want to change the code and it is in many places, the system must generate another code for the new value
				To change this value, it must be done by the administrator. 
				It is best to never change it.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
	 		Comments:
				In each microservice and database, you have one DataElement_Tbl. It work as a specific dictionary for it.
				Example:
					In the System Microservice you have the meaning of all databases tables, columns, stored procedures, views, java entities, classes, etc.
					In the Person Microservice you have the meaning of all person (legal or natural) whom can interact with the system.
					In the Users Microservice you have the meaning of all person who can enter in the system to work with. 
					In the Articles Microservice you have the meaning of all the articles that the campany can handle, to sell, buy, or have to use. 
			Tips:
				The ScopeIDn + BusinessUnitIDn + TableIDn combination can be the Kafka/rabbitMq topic.
    
		SysBaseElementOthersFiels_Tbl	
			Contains the Others Fields of the Data Elements records.
			This others fiedls are used to set up a new properties for a specific element. The others elements do not have this properties assigned.
			In each record you specify the property and the value it assumes for each item.
			The key for each record:
			  ID		--> is the uniqueidentifier auto generated.
			  IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  TableFieldIDn     -> the TableFieldIDn is the Field/Column that you add to the record. Linked with SysBaseElements_Tbl.
			  BaseElementIDn		-> the BaseElementIDn is the record element, which recieve the new property. Linked with SysBaseElements_Tbl.
			  Unique = One TableField must be unique for each BaseElement.
			Kafka/RabbitMQ:
			  The DataElementIDn + TableIDn combination can be the topic.  
			Common Field/Columns for all tables
			  The objective of these are to store critical information for the system and the record history.
			    StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
			    CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
			    LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
			    OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
			    DateCreated			--> The DateCreated is the record creation datetime UTC.
			    DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
			    TableHistory		-->	The TableHistory contain then change history of each column.

		SysBaseElementLanguages_Tbl	
			Contains the meaning of the diccionary in another languages than the default.
			In this table you have to comply with the same rules as the SysBaseElements_Tbl.
			Important Clarification: the values IdNum, ScopeIDn, CompanyIDn = are always equal to the ApplTDataElement table. 
									 These columns are put in this table only to ensure integrity and that there are no duplicates.
		    The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  	-- This three values are defined by the user.
			  	BaseElementLanguageIDn	--> the IdNum of the element that has another languages meaning. It is created in the SysBaseElements_Tbl.
			  	NameID     		-> is the readable code by the user.
			  	LanguageIDn 	-> the LanguagesIDn must be diferent from the default language.
			  	-- This two values are set by the system automaticaly, and are the same as the SysBaseElement_Tbl. For do that use the IdNum.
			  	ScopeIDn     	-> the Name must be unique for the application Scope, usually a Table.
			  	BusinessUnitIDn	-> the Name must be unique for the BusinessUnit.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
			Tips:
				The ScopeIDn + BusinessUnitIDn + TableIDn combination can be the Kafka/rabbitMq topic.
	
		SysBaseElementComments_Tbl	
			Contains one or more descriptions/comments/details/explains of each record of the diccionary.
			It has a defined language, an order when there is more than one description, a type of text format (mimetype), a status and the date of the last update.
			If the element has not been recorded in this table, it means the element has not clarifications.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  	This table have not unique key because one IDNum can have none, one or more coments.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.			
			Tips:
				The ScopeIDn + BusinessUnitIDn + TableIDn combination can be the Kafka/rabbitMq topic.
	
		SysBaseElementKafka_Tbl	
			Contains the relation between the BaseElements and the Kafka Topics, to make updated all the microservices.
	 		With this table the software manages what elements will be in each microservice.
			In the SysBaseElemnets_Tbl is created the KaftaTopic Scope and inside this scope you create the topics.
	 		In this table is assigned the BaseElement to a specific Topic. 
    			***You can assign one element to more than one Topic.***
			After you create or update and element, the system get the data of this table to know wich queues must be updated.
	 		If BaseElement is a Scope, all elements it contains must be updated.
	 		The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  KafkaTopicIDn     -> the KafkaTopicIDn is the IDNum of queue Topic. Linked with SysBaseElements_Tbl.
			  BaseElementIDn		-> the BaseElementIDn is the element, this could be an element or scope. Linked with SysBaseElements_Tbl.
			  Unique = One KafkaTopicIDn must be unique for each BaseElement.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.			

 
 
 	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 	Used to create the Companies
		SysCompanies_Tbl
			Contains the companies that use the software. 
			The Companies exists since you create them in this table. Its information, of what are they, are in the SysBaseElements_Tbl table.
			The key for each record:
				This table has not its own key, because in this table you only enable the company to all the system.
			The unique Key is the union of:
				CompanyIDn		--> The Company can not be duplicated. Link with the SysBaseElements_Tbl.
			Common Field/Columns for all tables
				This table do not have another field, because the store critical information for the system and the record history are set in SysBaseElements_Tbl.

  	Used to create the Microservices
		SysMicroservices_Tbl
			Contains the microservices that use the software. 
			The Microservices exists since you create them in this table. Its information, of what are they, are in the SysBaseElements_Tbl table.
			The key for each record:
				This table has not its own key, because in this table you only enable the microservice to all the system.
			The unique Key is the union of:
				MicroserviceIDn		--> The Microservice can not be duplicated. Link with the SysCompanies_Tbl.
			Common Field/Columns for all tables
				This table do not have another field, because the store critical information for the system and the record history are set in SysBaseElements_Tbl.

 	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create multiples tables
		SysRootElements_Tli
   			This Is a List Table that contains the other element of the system. Enable the IDNum element to a Microservice.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
				This table has its own key only for update it.
				The system does not use this key because, here we only enable the element to a Microservice.
			The unique Key is the union of:
				RootElementIDn 		--> the IdNum of the entity
				MicroserviceIDn		--> the IdNum of the Microservice that the Entity belong. When is equal System, all microservices of the BusinessUnit have access to them.
			Example: In the BaseElement_Tbl you have been created all values of the SysContries or SysLangueges, etc. This table is the diccionary and this values can not use it.
					 To make real and enable these values, we must to create a specific table for its. 
					 But if you are going to use only somes record of each tables, is bether have one table with all small tables. This table is called SysRootElement_Tbl.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
   			Tips:
				The RootElementIDn + MicroserviceIDn combination can be the Kafka/rabbitMq topic.		 
					 
	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create the software structure
		SysEntities_Tli	
			This is a List Table that contains the entities of the system, they can be database tables or java classes. 
			Enable the IDNum element to a Microservice.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
				This table has its own key only for update it.
				The system does not use this key because, here we only enable the element to a Microservice.
			The unique Key is the union of:
				EntityIDn 		--> the IdNum of the entity
				MicroserviceIDn --> the IdNum of the Microservice that the Entity belong. When is equal System, all microservices hava access to them.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
	 		Important:
				The Entity is enable in the SysEntities_Tli for a Microservice.
				The data is created in the SysBaseElement_Tbl and is the same to all the BusinessUnit.
				When I assign the entity, I define the business unit owner and the microservice it belongs to.
			Kafka/rabbitMq Topic:
				The combination of EntityIDn + MicroserviceIDn combination can be used.	 
	
		SysEntityFields_Tli	
			This is a List Table that contains the fields or the columns of the system. Enable the IDNum element to a Microservice.
			The fields exits to the Microservice, since you create it in this table. The information, of what are they, are in the SysBaseElement_Tbl table.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
				This table has its own key only for update it.
				The system does not use this key because, here we only enable one field/column to a Microservice.
			The unique Key is the union of:
				FieldIDn 		--> The FieldIDn is the IDNum of the field. Link with the diccionary table - SysBaseElement.
				MicroserviceIDn --> The MicroserviceIDn is the IDNum of the microservice to which the field belongs. If its value is System all microservices have access to it.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.

		SysEntityStructures_Tbl	
			Contains the system structure. In this table you build the tables and java entities structures, with thiers column and fields.
			You must define all the properties of the entities.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				FieldIDn 		--> The FieldIDn is the IDNum of the field/column of the entity. Link with the SysBaseElements_Tbl.
				EntityIDn 		--> The EntityIDn is the IDNum of the entity. Link with the SysBaseElements_Tbl.
				MicroserviceIDn --> the IdNum of the Microservice that the Field and Entity belong. When is equal System, all microservices hava access to them.
				When an entity is selected from the Entity_Tbl, the Microservice is set, this parameter define the possible fields to be selected.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
	 
		SysEntityStructureFieldProperties_Tbl
			Contains the properties of each column/table or field/entity.
			Each field/column can have many properties, like DataType, Lenght, IsPrimaryKey, IsNotNull, etc.
			The key for each record:
			    ID		--> is the uniqueidentifier auto generated.
			    IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			    FieldPropertyIDn	--> The FieldPropertyIDn is the IDNum of the field property type. Link with the SysBaseElement_Tbl.
			    EntityStructureIDn	--> The EntityStructureIDn is the IDNum of the entity structure. Link with the SysEntityStructure_Tbl.
			                            The EntityStructureIDn has a FieldIDn + EntityIDn + MicroserviceIDn unique key.
			Common Field/Columns for all tables
			    The objective of these are to store critical information for the system and the record history.
			        StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
			        CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
			        LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
			        OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
			        DateCreated			--> The DateCreated is the record creation datetime UTC.
			        DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
			        TableHistory		-->	The TableHistory contain then change history of each column.
			The table operation:
			    To enter the data, you need the combination of two field, they are:
			        EntityStructureIDn	--> The EntityStructureIDn is the IDNum of the entity structure (Microservice + Entity + Field). Link with the SysEntityStructurea_Tbl.
			        FieldPropertyIDn	--> The FieldPropertyIDn is the IDNum of the field property (DataType, Length, IsNotNull, etc). Link with the SysBaseElementa_Tbl.
			        FieldValueTypeIDn	--> The FieldValueTypeIDn is the IDNum of the type property. ILink with the SysBaseElementa_Tbl.
			        FieldValue			--> The FieldValue is a numeric/text/etc value of the property, this value is not standard and can not be multilanguage.
			                                If the value is an IDNumType, the meaning of it, is in the SysBaseElments_Tbl.
			    Example: You have a Name column. You can set the datatype as string, and the length of the string in 20.
			        First you add one record to define the datatype.
			            EntityStructureIDn = IDNum, represent Entity Person, Column = StateIDn. Link with the SysEntityStructurea_Tbl.
			            FieldPropertyIDn = IDNum, represent DataType, the property of the field. Link with the SysBaseElementa_Tbl.
			            FieldValueTypeIDn = IDNum, represent table state data type. Link with the SysBaseElementa_Tbl.
			            FieldValue = 372, IDNum, represent enable value. Link with the SysBaseElementa_Tbl.
			        Second you add another record to define the lenght.
			            EntityStructureIDn = IDNum, represent Entity Person, Column = StateIDn. Link with the SysEntityStructurea_Tbl.
			            FieldPropertyIDn = IDNum, represent Length, the property of the field. Link with the SysBaseElementa_Tbl.
			            FieldValueTypeIDn = IDNum, represent smallint, the value of the FieldValue is numeric. Link with the SysBaseElementa_Tbl.
			            FieldValue = 20, is the max length of the column. This value are not linked with anythings.
			
		SysEntityStructureFieldDefaultValues_Tbl
			Contains the default value of each field or column. When you set a value for a column, if the user do not enter, the system get the value from this table.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				DefaultVersionIDn	--> The DefaultVersionIDn is the IDNum of the DefaultVersion, defined in the SysBaseElement_Tbl.
		  		EntityStructureIDn	--> The EntityStructureIDn is the IDNum of the entity structure. Link with the SysEntityStructure_Tbl.
			                            The EntityStructureIDn has a FieldIDn + EntityIDn + MicroserviceIDn unique key.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
	 		The table operation:
				After you select the EntityStructure (FieldIDn + EntityIDn + MicroserviceIDn) you need to define this two fields:
					DefaultVersionIDn	--> The DefaultVersionIDn is the IDNum of the DefaultVersion, defined in the SysBaseElement_Tbl.
					FieldDefaultValue	--> The FieldDefaultValue is a value that must be the same DataType of the field defined in the SysEntityStructures_Tbl.
				You can define more than one version. 
				Example: If you have to import some data from two diferent suppliers, you can define two diferent version. 
						 In the field supplierIDn, you set the specific number of the supplier.

	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create the Companies and theis Structures
		SysCompanyMicroservice_Tbl
			Contains the relation between Companies and Microservices. 
			This is One Company can have multiples Microservices and One Microservices can be used by multiples Companies.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				CompanyIDn	--> The CompanyIDn is the IDNum. Link with the SysCompanies_Tbl.
		  		MicroserviceIDn	--> The MicroserviceIDn is the IDNum. Link with the SysMicroservices_Tbl.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.

		SysCompanyRelations_Tbl
			Contains the relations between companies. If you have a economic group in this table can build this structure.
   			Here you can build the tree of the groups companies using the father/son schema.
			This type of structure allow you to get a individual or group report.
			But this table does not build the internal structure of the company.
   			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
   				RelationID	--> The ID of the relation (uniqueidentifier). Link with the SysCompanyRelations_Tbl ID Value.
	   			RelationLevel	--> The level of the relation, all start with the number 1. 
	   			RelationOrden	--> The Orden inside the level, all level start with the number 1.
				CompanyIDn	--> The CompanyIDn is the IDNum of the Company involved in the relation. Link with the SysCompanies_Tbl.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.

		
