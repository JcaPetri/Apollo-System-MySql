# Apollo-System-MySql
CREATE DATABASE `systemsdb` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_as_cs */ /*!80016 DEFAULT ENCRYPTION='N' */;

Main Structure of all Microservices
Group: com.apolo
Name: system / articles / persons / users

========================================================================================================================================================================================
System Microservice
Definitions:
	Contains the system structure of all the software. In this module you need to create all the databases and java classes, enums, interfaces, entities, etc.
	The main tables of this module will be in the other databases such as mirrors. These mirror tables will be updated via Kafka topics.
	With this mechanism each microservice becames independent of the others. Example: MirSysCompanies, MirSysBusinessUnits, etc.
	One important thing, is you make a mirror only of the records that need in the other microservice, not all the records that exists in the System Microservice.

Structure are as follows:
	Used to create the main elements
		SysBaseElements_Tbl	--> Contains the diccionary of all system elements of the Microservice.
		SysBaseElementLanguages_Tbl	--> Contains the meaning of the diccionary in other languages.
		SysBaseElementComments_Tbl	--> Contains one or more comments/details/explains of each record of the diccionary.
	Used to create multiples tables
		SysRootElements_Tbl	--> Contains the other element of the system. When you enter the IDNum in this table, you active the element to be used in other microservices. 
	Used to create the software structure
		SysEntities_Tbl	--> Contains the entities of the system, they can be database tables or java classes.
		SysEntityFields_Tbl	--> Contains the fields or the columns of the entities.
		SysEntityStructures_Tbl	--> Contains each structure of the database tables or java classes.
		SysEntityStructureFieldProperties_Tbl	--> Contains the properties of each field or column. They can be DataType, Lenght, etc.
		SysEntityStructureFieldDefaultValues_Tbl	--> Contains the default value of each field or column. When you set a value for a column, if the user don't enter, the system get from this table the value to enter.
	Used to create the Companies and theis Structures
		SysCompanies_Tbl	--> Contains the companies that use the software.
		SysCompanyTree_Tbl	--> Contains the structure of each company. If you have a economic group in this table can build this structure. 
		SysBusinessUnits_Tbl	--> Contains the business units that belong to the company.

========================================================================================================================================================================================
Detailed explanation of each table.
	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
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
			Important: when you create the element/object in this tabel, this element don't exits for the software. This table is like a dictionary.
						Only exist when you create the code in the specific table.
				Example: the pampa article is created in the dictionary, but it does not exist until it is created in the Articles table.
			Modification Rules:
				You can change the Name if there is a spelling error. Example you have an spelling error in a invoce and must be invoice.
				Warning: If I change the code that represents the word Invoice and it is an afip receipt. And I put food, everywhere the code is, food will start to appear.
				If you want to change the code and it is in many places, the system must generate another code for the new value
				To change this value, it must be done by the administrator. 
				It is best to never change it.
			Comments:
				In each microservice and database, you have one DataElement_Tbl. It work as a specific dictionary for it.
				Example:
					In the System Microservice you have the meaning of all databases tables, columns, stored procedures, views, java entities, classes, etc.
					In the Person Microservice you have the meaning of all person (legal or natural) whom can interact with the system.
					In the Users Microservice you have the meaning of all person who can enter in the system to work with. 
					In the Articles Microservice you have the meaning of all the articles that the campany can handle, to sell, buy, or have to use. 
			Tips:
				The ScopeIDn + BusinessUnitIDn combination can be the Kafka/rabbitMq topic.	 (+ MicroserviceIDn + )

		SysBaseElementLanguages_Tbl	
			Contains the meaning of the diccionary in another languages than the default.
			In this table you have to comply with the same rules as the TBaseElement table.
			The key for each record:
				This table hasn't its own key.
			The unique Key is the union of:
			  	IDNum			--> Is the number unique autonumerical value. It is created in the SysBaseElements_Tbl.
				-- This two values are defined by the user.
			  	IDName     		-> is the readable code by the user.
			  	LanguageIDn 	-> the Name must be unique for the new Language. It must be diferent from the default language.
			  	-- This two values are set by the system automaticaly, and are the same as the SysBaseElement_Tbl. For do that use the IdNum.
			  	BaseElementLanguageIDn	--> the IdNum of the element that has another langueges.
			  	ScopeIDn     	-> the Name must be unique for the application Scope, usually a Table.
			  	BusinessUnitIDn	-> the Name must be unique for the BusinessUnit.
			Clarification: the values IdNum, ScopeIDn, CompanyIDn = are always equal to the ApplTDataElement table. 
					These columns are put in this table only to ensure integrity and that there are no duplicates.
	
		SysBaseElementComments_Tbl	
			Contains one or more descriptions/comments/details/explains of each record of the diccionary.
			It has a defined language, an order when there is more than one description, a type of text format (mimetype), a status and the date of the last update.
			If the element has not been recorded in this table, it means the element hasn't clarifications.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  	This table haven't unique key because one IDNum can have none, one or more coments.
			
	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create multiples tables
		SysRootElements_Tbl
			Contains the other element of the system. 
			When you enter the IDNum in this table, you active the element to be used in other microservices. 
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
				This table has its own key only for update it. 
				The system don't use this key because, here we only enable the element to a Microservice and BusinessUnit.
			The unique Key is the union of:
				RootElementIDn 		--> the IdNum of the entity
				MicroserviceIDn		--> the IdNum of the Microservice that the Entity belong. When is equal System, all microservices of the BusinessUnit have access to them.
				BusinessUnitIDn		--> the IdNum of the BusinessUnit that the Entity and Microservice belong.
			Example: In the BaseElement_Tbl you have been created all values of the SysContries or SysLangueges, etc. This table is the diccionary and this values can't use it.
					 To make real and enable these values, we must to create a specific table for its. 
					 But if you are going to use only somes record of each tables, is bether have one table with all small tables. This table is called SysRootElement_Tbl.
			Tips:
				The RootElementIDn + MicroserviceIDn + BusinessUnitIDn combination can be the Kafka/rabbitMq topic.		 
					 
	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create the software structure
		SysEntities_Tbl	
			Contains the entities of the system, they can be database tables or java classes.
			The entity exist since you create it in this table. It´s information, of what are they, are in the BaseElement table.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				EntityIDn 		--> the IdNum of the entity
				MicroserviceIDn --> the IdNum of the Microservice that the Entity belong. When is equal System, all microservices hava access to them.
				BusinessUnitID	--> the IdNum of the BusinessUnit that the Entity and Microservice belong.
			Important:
				The Entity is generated in the SysEntities_Tbl for a BusinessUnit and Microservice.
				The data is created in the BaseElement_Tbl and is the same to all the BusinessUnit.
				When I create the entity, I define the business unit owner and the microservice it belongs to.
				That is the reason why you generate a new ID and IdNum for this table. 
			Kafka/rabbitMq Topic:
				The combination of EntityIDn + MicroserviceIDn + BusinessUnitIDn combination can be used.	 
	
		SysEntityFields_Tbl	
			Contains the fields or the columns of the entities.
			The fields exits to the entity, since you create it in this table. It´s information, of what are they, are in the BaseElement table.
			The key for each record:
				This table hasn't its own key, because here we only enable one field/column to a Microservice and BusinessUnit,
			The unique Key is the union of:
				FieldIDn 		--> The FieldIDn is the IDNum of the field. Link with the diccionary table - SysBaseElement.
				MicroserviceIDn --> The MicroserviceIDn is the IDNum of the microservice to which the field belongs. If its value is System all microservices have access to it.
				BusinessUnitIDn	--> The BusinessUnitIDn is the business unit that the entities belong. Link with the SysCompany.

			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.

		SysEntityFields_Tbl	
			Contains the system structure. In this table you build the tables and java entities structures, with thiers column and fields.
			You must define all the properties of the entities.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				FieldIDn 		--> The FieldIDn is the IDNum of the field/column of the entity. Link with the SysEntityField_Tbl.
				EntityIDn 		--> The EntityIDn is the IDNum of the entity. Link with the SysEntity_Tbl.
									The entity is set to a microservice and business unit.
				When an entity is selected from the Entity_Tbl, the BusinessUnit are set, these parameters define the possible fields to be selected.

		SysEntityStructureFieldProperties_Tbl
			Contains the properties of each column/field of an table/Entity.
			Each field/column can have many properties, like DataType, Lenght, IsPrimaryKey, IsNotNull, etc.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				FieldPropertyIDn	--> The FieldPropertyIDn is the IDNum of the field property type. Link with the SysBaseElement_Tbl.
				EntityStructureIDn	--> The EntityStructureIDn is the IDNum of the entity structure. Link with the SysEntityStructure_Tbl.
										The EntityStructureIDn has a FieldIDn + EntityIDn primary key.
			The table operation:
				To enter the data you need the combination of two field, they are:
					FieldValueTypeIDn	--> The FieldValueTypeIDn is the IDNum of the type property. Its come from the SysBaseElement.
					FieldValue			--> The FieldValue is a numeric/text/etc value of the property, this value is not standar and can´t be multilanguage.
											If the value is an IDNumType, the meaning is in the ArticleOptionalFields_Tbl.
				Example: You have a Name column. You can set the datatype as string, and the length of the string in 20.
						 First you add two record, one for datatype and one for length.
						 DataType is a String type and is defined in the BaseElement_Tbl, so you only set the IDNum to the FieldValue. 
								Because the IDNum is a numeric value, you set the FieldValueTypeIDn with SysElementType.
						 Length is a numeric, so you enter the value in the FieldValue. You set the FieldValueTypeIDn wiht integer java type.

		SysEntityStructureFieldDefaultValues_Tbl
			Contains the default value of each field or column. When you set a value for a column, if the user don't enter, the system get from this table the value to enter.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				FieldIDn			--> The FieldIDn is the IDNum of the field/column of the entity. Link with the SysBaseElement_Tbl.
				DefaultVersionIDn	--> The DefaultVersionIDn is the IDNum of the DefaultVersion, defined in the SysBaseElement_Tbl.
				EntityStructureIDn	--> The EntityStructureIDn is the IDNum of the entity structure. Link with the SysEntityStructure_Tbl.
										The EntityStructureIDn has a FieldIDn + EntityIDn primary key.
			The table operation:
				After you select the table and the field you need to define this two fields:
					DefaultVersionIDn	--> The DefaultVersionIDn is the IDNum of the DefaultVersion, defined in the SysBaseElement_Tbl.
					FieldDefaultValue	--> The FieldDefaultValue is a value that must be the same type of the field defined in the SysEntityStructure_Tbl.
				You can define more than one version. 
				Example: If you have to import some data from two diferent suppliers, you can define two diferent version. 
						 In the field supplierIDn, you set the specific number of the supplier.

	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create the Companies and theis Structures
		SysCompanies_Tbl
			Contains the companies that use the software. 
			The Companies exists since you create them in this table. It`s information, of what are they, are in the BaseElement_Tbl table.
			The key for each record:
				This table hasn't its own key, because in this table you only enable the company to all the system.
			The unique Key is the union of:
				BusinessUnitIDn		--> The CompanyIDn is the company that the business unit belong. Link with the SysCompanies_Tbl.

		SysCompanyTree_Tbl
			Contains the structure of each company. If you have a economic group in this table can build this structure.
			Here you can build the tree of the groups companies using the father/son schema.
			This type of structure allow you to get a individual or group report.
			<<<<<<<<<<<<<<<<<<<<<<<<<<< Pending of Development >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

		SysBusinessUnits_Tbl
			Contains the business units that belong to the company.
			The business unit exists since you create them in this table and you assing it to a Company. It`s information, of what are they, are in the BaseElement_Tbl table.
			The key for each record:
				This table hasn't its own key, because in this table you only enable the business unit to a company to all the system.
			The unique Key is the union of:
				BusinessUnitIDn		--> The BusinessUnitIDn is the IDNum of the business unit. Link with the SysBaseElement_Tbl.
				CompanyIDn			--> The CompanyIDn is the company that the business unit belong. Link with the SysCompany_Tbl.
			The table operation:
				After you create the BusinessUnit in the BaseElement table, you add it to this table and assign it to a specific Company. 
				One company can have one or more than a BusinessUnit.

========================================================================================================================================================================================
Postgres Queries
	Used to create the main elements
		SysBaseElements_Tbl	--> Contains the diccionary of all system elements of the Microservice.
			SELECT * FROM public."SysBaseElements_Tbl";
		SysBaseElementLanguages_Tbl	--> Contains the meaning of the diccionary in other languages.
			
		SysBaseElementComments_Tbl	--> Contains one or more comments/details/explains of each record of the diccionary.
			
	Used to create multiples tables
		SysRootElements_Tbl	--> Contains the other element of the system. When you enter the IDNum in this table, you active the element to be used in other microservices. 
			SELECT * FROM public."SysBaseElements_Tbl";
	Used to create the software structure
		SysEntities_Tbl	--> Contains the entities of the system, they can be database tables or java classes.
			SELECT * FROM public."SysEntities_Tbl";
		SysEntityFields_Tbl	--> Contains the fields or the columns of the entities.
			SELECT * FROM public."SysEntityFields_Tbl";
		SysEntityStructures_Tbl	--> Contains each structure of the database tables or java classes.
			SELECT * FROM public."SysEntityStructures_Tbl";
		SysEntityStructureFieldProperties_Tbl	--> Contains the properties of each field or column. They can be DataType, Lenght, etc.
			SELECT * FROM public."SysEntityStructureFieldProperties_Tbl";
		SysEntityStructureFieldDefaultValues_Tbl	--> Contains the default value of each field or column. When you set a value for a column, if the user don't enter, the system get from this table the value to enter.
			SELECT * FROM public."SysEntityStructureFieldDefaultValues_Tbl";
	Used to create the Companies and theis Structures
		SysCompanies_Tbl	--> Contains the companies that use the software.
			SELECT * FROM public."SysCompanies_Tbl";
		SysCompanyTree_Tbl	--> Contains the structure of each company. If you have a economic group in this table can build this structure. 
			
		SysBusinessUnits_Tbl	--> Contains the business units that belong to the company.
			SELECT * FROM public."SysBusinessUnits_Tbl";
