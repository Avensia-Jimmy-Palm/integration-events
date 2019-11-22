# Supplier Package Events

These events ralates to changes made to packages by "alternative suppliers". Changes made by the item owner are separate events.

The events related to supplier packages will be sent from Avensia Middleware to an Azure Service Bus queue for consumption by Byggtjeneste.

[Supplier Package Created](#Supplier-Package-Created)

[Supplier Package Updated](#Supplier-Package-Updated)

[Supplier Package Deleted](#Supplier-Package-Deleted)

## Properties

| Property              | Type     | Required     | Nullable | Description                                |
| --------------------- | -------- | ------------ | -------- | ------------------------------------------ |
| [data](#data)         | `object` | **Required** | No       |         |
| [metadata](#metadata) | `object` | **Required** | No       |         |

### metadata

`object` with the following properties:

| Property          | Type    | Required     | Description |
| ------------------| ------- | ------------ | ------- |
| `eventType`       | string  | **Required** | Either "Create", "Update" or "Delete".
| `event`           | string  | **Required** | Always "SupplierPackage" for supplier package events.
| `date`            | string  | **Required** | Date and time for the action that triggered the event. In format yyyy-MM-dd hh:mm:ss.
| `author`          | string  | **Required** | Author of the action that triggered the event.

### data
The data model depends on the event type, see below.


# Supplier Package Created 

## Preconditions
- only sent once (any later update to a supplier package triggers a "Supplier package updated" event)

## Properties

### data

`object` with following properties:

| Property                | Type    | Required     | Description |
| ----------------------- | ------- | ------------ | ------- |
| `id`                    | string  | **Required** | 
| `participantNumber`	  | integer | **Required** | Alternative supplier participant number
| `packageId`          	  | string  | **Required** | Parent package GUID
| `gtin`          		  | string  | **Optional** | 
| `stocked`          	  | boolean | **Required** | 
| `deliverable`           | boolean | **Required** | 
| `dPakLayerCount`  	  | integer  | **Optional** | 
| `maxStackingWeight`     | decimal  | **Optional** | 


## Sample json
```json
{
	"metadata": {
		"eventType": "Create", // string
		"event": "SupplierPackage", // string
		"date": "2019-09-30 12:34:56", // datetime in yyyy-MM-dd hh:mm:ss
		"author": "Glava AS" // string
	},
	
	"data": {
		// identifiers
		"id": "b7c6081c-7b8e-47fd-8294-b195fe05ae63", // GUID
		"participantNumber": 201768, // integer
		"packageId": "52b552a7-274c-4610-b3f7-3bc43663ab50", // GUID to a package entity
		
		// other data fields
		"gtin": "055566667777", // string, optional
		"stocked": true, // boolean
		"deliverable": false, // boolean
	}
}

```

# Supplier Package Updated 

## Preconditions
- the "Supplier package created" event has already been sent

## Notes
The identifiers must be part of the event data.
Otherwise, only changed fields can be part of the event data.

## Properties

### data

`object` with following properties:

| Property                | Type    | Required     | Description |
| ----------------------- | ------- | ------------ | ------- |
| `id`                    | string  | **Required** | 
| `participantNumber`	  | integer | **Required** | Alternative supplier participant number
| `packageId`          	  | string  | **Required** | Parent package (item owners package) GUID
| `gtin`          		  | string  | **Optional** | 
| `stocked`          	  | boolean | **Optional** | 
| `deliverable`           | boolean | **Optional** | 
| `dPakLayerCount`  	  | integer  | **Optional** | 
| `maxStackingWeight`     | decimal  | **Optional** | 

## Sample json

```json
{
	"metadata": {
		"eventType": "Update", // string
		"event": "SupplierPackage", // string
		"date": "2019-09-30 12:34:56", // datetime in yyyy-MM-dd hh:mm:ss
		"author": "Glava AS" // string
	},
	
	"data": {
		// identifiers must always be part of the event and can't change value
		"id": "b7c6081c-7b8e-47fd-8294-b195fe05ae63",
		"participantNumber": 201768,
		"packageId": "52b552a7-274c-4610-b3f7-3bc43663ab50",
		
		// an example of updating stocked
		"stocked": false
	}
}

```

# Supplier Package Deleted 

## Preconditions
- the "Supplier package created" event has already been sent

## Notes
The identifiers must be part of the event data.

## Properties

### data

`object` with following properties:

| Property                | Type    | Required     | Description |
| ----------------------- | ------- | ------------ | ------- |
| `id`                    | string  | **Required** | 
| `participantNumber`	  | integer | **Required** | Alternative supplier participant number
| `packageId`          	  | string  | **Required** | Parent package 


## Sample json

```json
{
	"metadata": {
		"eventType": "Delete", // string
		"event": "SupplierPackage", // string
		"date": "2019-09-30 12:34:56", // datetime in yyyy-MM-dd hh:mm:ss
		"author": "Glava AS" // string
	},
	
	"data": {
		// the identifiers to the supplier package that was deleted
		"id": "b7c6081c-7b8e-47fd-8294-b195fe05ae63",
		"participantNumber": 201768,
		"packageId": "52b552a7-274c-4610-b3f7-3bc43663ab50"
	}
}

```