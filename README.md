# api-styleguide
Style Guide for API Services using Microsoft Web API 

As of 8/18/16 this style guide is provisional

## Table of Contents


1. [General](#general)
1. [Controllers](#controllers)
1. [Routes](#routes)
1. [Business Classes](#business-classes)
1. [Data Access Layer](#data-access-layer)



## General

###### [Style [A000](#style-a000)]

- Application Access to the database should be managed through the following Six Components

		- Controller Class - defines API endpoints / routes
		- Business Class - Calls the DAL / loads entity classes
		- Entity Class - Define structure the application will receive, serialized to JSON
		- Mapper Class - Translates the table class to an entity class, and visa-versa
		- DAL Class - call stored procedures, connect to database
		- Table Class - defines shape returned by database / sproc calls

*Why?*: Single responsibility principle

*Why?*: keep file sizes small

*Why?*: decouple database knowledge from service / business layer


###### [Style [A000](#style-a000)]

- Single Class per file

*Why?*: single responsibility principle

*Why?*: Diffing, Merging, greatly simplified with smaller, more focused files.

###### [Style [A000](#style-a000)]

- class name must be the same as the file name

*Why?*: Since files only contain one class, this is the most appropriate name.  any other name would only create confusion.  

###### [Style [A000](#style-a000)]

- Organize files around entity, not type.

```
	do:
		\api
			\customer
				CustomerController.cs
				CustomerBiz.cs
				Customer.cs
				CustomerMapper.cs
				CustomerDAL.cs
				CustomerTable.cs
				CustomerResultXXX.cs
	
	don't:
		\api
			\controllers
				CustomerController.cs
			\models
				CustomerBiz.cs
				Customer.cs
				CustomerMapper.cs
		\dal
			\DAL Classes
				CustomerDAL.cs
			\Entities
				CustomerTable.cs
				CustomerResultXXX.cs
```

*Why?*: Keep related entities together

issue: It might be good to separate controllers, because of calls like this: /api/customers/2000/orders order  (the order entity being retrieved by CustomerController, like the OrderController as well)

###### [Style [A000](#style-a000)]

- API should be defined as a Visual Studio 2015 project Web Application project using the ASP.NET 4.5.2 Web API Template

###### [Style [A000](#style-a000)]

- Add a sub folder for each entity

```
	do:
		\api
			\customer
			\activity
			\account
			\Quote
```


###### [Style [A000](#style-a000)]

- Use a single API project vs separate projects for API, Biz, and DAL

*Why?*: Keep API related components together in a single location, when possible.  While some components are shared, many are not

		issue: namspace issues?
		issue: does this matter for decoupling?  


###### [Style [A000](#style-a000)]

- API project name should be `<app name>.api`

*Why?*: proper namespacing

## Controllers

###### [Style [A000](#style-a000)]

- controller classes must named `<entity>Controller`

*Why?*: Consistency


###### [Style [A000](#style-a000)]

- Do not include business logic in controller classes


*Why?*: controller classes define api endpoints - Single responsibility principle



###### [Style [A000](#style-a000)]

- Include application routes as comments over each public service method as well as POST/PUT/GET HTTP verbs

*Why?*: explicitly define what valid routes should look like


###### [Style [A000](#style-a000)]

- API class should work with JSON objects

*Why?*: ideal for angular work

*Why?*: JSON more readable than XML


## Routes

###### [Style [A000](#style-a000)]

- API Routes should begin with `/api`

*Why?*: distinguish api routes from front-end routes


###### [Style [A000](#style-a000)]

- Entities in api routes should be pluralized

```
do:   /api/customers/

don't: /api/customer

```

*Why?*: distinguish api routes from front-end routes


###### [Style [A000](#style-a000)]

- API routes should read as retrieving resources vs performing actions

```
	do:
		/api/customers/2000
		
	don't:
		/api/getCustomerbyid/2000
```

*Why?*: Restful principles

*Why?*: readability

*Why?*: better organization


###### [Style [A000](#style-a000)]

- Define sub routes for getting related entities 

```
do:  
		/api/customers/2000/orders
		
	don't:
		/api/customerorders/2000
		/api/orders/custid=2000
```

*Why?*: data access should revolve around well-defined entities, rather than a collection of dozens of method calls

*Why?*: encourages property refactoring and reviewing design choices

*Why?*: helps define what the application can do.   e.g. /api/orders/454541/customers would not exist because an order may only have one customer. 


###### [Style [A000](#style-a000)]

- use Primary keys for selecting entities

*Why?*: multi-line result sets when single entities are expected

*Why?*:  user does not normally see these routes


###### [Style [A000](#style-a000)]

- include primary key's as part of the path

```
	do:
		/api/customers/2000
	don't:
		/api/customers/id=2000
```

*Why?*: RESTful principles


###### [Style [A000](#style-a000)]

- include optional settings, search filters using query strings

```
    do:
		/api/customers?status=new
		/api/products/name="paint"&category="automotive"
		
		
	don't
		/api/customers/new
		/api/products/paint/automotive

```

*Why?*: confusing when many choices are present, across multiple fields

## Business Classes

###### [Style [A000](#style-a000)]

- Business classes must named `<entity>Biz`

*Why?*: Consistency



###### [Style [A000](#style-a000)]

- Entity classes must named `<entity>`

*Why?*: Consistency

*Why?*: this handle will be used frequently inside the application


###### [Style [A000](#style-a000)]

- Mapper classes must named `<entity>Mapper`

*Why?*: Consistency


###### [Style [A000](#style-a000)]

- use a mapper class to move data between entity classes and table classes

*Why?*: keep boilerplate assignment statements out of business class



###### [Style [A000](#style-a000)]

- entity classes should use CamelCase 

*Why?*: will be serialized to JSON

*Why?*: Follow application / front-end conventions


###### [Style [A000](#style-a000)]

- Do not include business logic in Entity class

*Why?*: keep entity definition separate from behavior - this is angular recommendation on client

*Why?*: keeps usage consistent in the controller (sometimes call the entity method, other times the DAL class method...)

*Why?*: keeps DAL components out of the controller

	issue: This is commonly done


## Data Access Layer

###### [Style [A000](#style-a000)]

- DAL classes must named `<entity>DAL`

*Why?*: Consistency


###### [Style [A000](#style-a000)]

- Table classes that primarily define a database table must named `<entity>Table`

e.g. CustomerTable may contain all fields from Customer table, and associated lookup values e.g. Customer_Type_Name

*Why?*: The business layer needs to frequently interact with database table representations


###### [Style [A000](#style-a000)]

- Table classes that define a composite result set must be named <entity>Result<descriptor>

This needs some work.  The result feels forced - the idea comes from a sql ResultSet - basically every sproc we have either needs to map to a XXXTable class or 
a XXXResultYYYY class...

*Why?*: 


###### [Style [A000](#style-a000)]

- table classes should follow database naming conventions

*Why?*: necessary for allowing dapper to map result set columns to class instance(s)


###### [Style [A000](#style-a000)]

- Use Dapper framework to perform database calls

*Why?*: dapper is lightweight, fast


###### [Style [A000](#style-a000)]

- Use stored procedures for database access

*Why?*: do not want to hardcode sql in DAL classes - difficult to test, debug, no intellisense

*Why?*: required for application security architecture


###### [Style [A000](#style-a000)]

- do not include data access logic in Table Class

*Why?*: class should only define shape of result set
