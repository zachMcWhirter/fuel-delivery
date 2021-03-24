# fuel-delivery

## Description

### Summary:
A client needs a system designed to assist with dispatching fuel delivery
trucks in the local area. All the fuel trucks have the same fixed capacity of fuel
for delivery, and deliveries will usually drain between 5% and 17% of the total
capacity. Unfortunately, the amount needed at each stop is unknown so a dispatcher
is required to set the next stop on the fly or instruct the truck to return to base
for refilling. The system will utilize three components to get job done; an embedded
system on the trucks, a desktop application for the dispatcher, and a web service so
the systems can communicate.

### Requirements:
- Create a design consisting of any combination of code, pseudo-code, or
diagrams that can help us set the roadmap for creating this new system.
- List class definitions for any objects needed in the dispatcher application.
- Provide a high level outline for how these classes will be used to maintain a
current “state”.
- List web service method signatures that the truck will call to get or post
information to the dispatcher.
- Describe how we will store and retrieve information from a SQL database for a
recorded fuel stop. Provide a minimal two table definition of “Stops” and
“Fuel delivery event”
- Demonstrate delivery reporting capability by writing two SQL queries to show
the following:
o Top 10 fuel consuming “Stops” over the past 12 months.
o Average fuel consumption per delivery, listed by “Stop” for the month.

### Limitations/extra info:
- Trucks are limited to certain “operating regions” and need to be displayed to
the dispatcher by “operating region”
- The truck’s embedded system only “knows” three things: current fuel level,
current stop, and next stop.
- The truck does not have its next stop set remotely, it must poll the service
for a new stop.
- “Stops” are unique identifiers for customer locations. (though the truck can
be set to a special “stop” to instruct it to return for refill)

## Sequence of Events and DB Requests required to provide functionality

1. Truck sends `POST` request to start route
```
POST /trucks/I<truck_id>
{
   "status": "true"
}
```
response:
```
{
    "currentStop": {  
        "id": "1", 
        "nextScheduledService": "datetime",
        "title", "Bobs Truck Stop",
        "address": "1505 Bob Road, Nashville, TN 37214",
        "type": "delivery
    },
    "nextStop": {  
        "id":  "2", 
        "nextScheduledService": "datetime",
        "title", "Bills Truck Stop",
        "address": "1505 Bill Road, Nashville, TN 37214",
        "type": "delivery"
    },
    "fuelLevel": 10000.0
}
```
2. Truck drives to the next stop and delivers the fuel
3. Truck reports new fuel level to server
```
POST /fillup
{
    "gallons": 1500.0,
    "stopId" : "2",
    "truckId": "1"
}
```
response:
```
{
	"truck": {
        "currentStop": {  
            "id": "2", 
            "nextScheduledService": "datetime",
            "title", "Bills Truck Stop",
            "address": "1505 Bill Road, Nashville, TN 37216",
            "type": "delivery"
        },
        "nextStop": {  
            "id":  "3", 
            "nextScheduledService": "datetime",
            "title", "Sues Truck Stop",
            "address": "1505 Sue Road, Nashville, TN 37216",
            "type": "delivery"
        },
        "fuelLevel": 8500.0
   }
}
```
4. Truck drives to the next stop and delivers the fuel
5. Truck reports new fuel level to server
```
POST /fillup
{
    "gallons": 1200.0,
    "stopId" : "3",
    "truckId": "1"
}
```
response:
```
{
	"truck": {
        "currentStop": {  
            "id":  "3", 
            "nextScheduledService": "datetime",
            "title", "Sues Truck Stop",
            "address": "1505 Sue Road, Nashville, TN 37207",
            "type": "delivery"
        },
        "nextStop": {  
            "id":  "4", 
            "nextScheduledService": "datetime",
            "title", "Joes Truck Stop",
            "address": "1505 Joe Road, Nashville, TN 37076",
            "type": "delivery"
        },
        "fuelLevel": 7300.0
   }
}
```
6. Truck drives to the next stop and delivers the fuel
7. Truck reports new fuel level to server
```
POST /fillup
{
    "gallons": 1700.0,
    "stopId" : "4",
    "truckId": "1"
}
```
response:
```
{
	"truck": {
        "currentStop": {  
            "id":  "4", 
            "nextScheduledService": "datetime",
            "title", "Joes Truck Stop",
            "address": "1505 Joe Road, Nashville, TN 37076",
            "type": "delivery"
        },
        "nextStop": {  
            "id":  "5", 
            "nextScheduledService": "datetime",
            "title", "Janes Truck Stop",
            "address": "1505 Jane Road, Nashville, TN 37206",
            "type": "delivery"
        },
        "fuelLevel": 5600.0
   }
}
```
8. Truck drives to the next stop and delivers the fuel
9. Truck reports new fuel level to server
```
POST /fillup
{
    "gallons": 1600.0,
    "stopId" : "5",
    "truckId": "1"
}
```
response:
```
{
	"truck": {
        "currentStop": {  
            "id":  "5", 
            "nextScheduledService": "datetime",
            "title", "Janes Truck Stop",
            "address": "1505 Jane Road, Nashville, TN 37206",
            "type": "delivery"
        },
        "nextStop": {  
            "id":  "6", 
            "nextScheduledService": "datetime",
            "title", "Johns Truck Stop",
            "address": "1505 John Road, Nashville, TN 37206",
            "type": "delivery"
        },
        "fuelLevel": 4000.0
   }
}
```
10. Truck drives to the next stop and delivers the fuel
11. Truck reports new fuel level to server
```
POST /fillup
{
    "gallons": 1400.0,
    "stopId" : "6",
    "truckId": "1"
}
```
response:
```
{
	"truck": {
        "currentStop": {  
            "id":  "6", 
            "nextScheduledService": "datetime",
            "title", "Johns Truck Stop",
            "address": "1505 John Road, Nashville, TN 37206",
            "type": "delivery"
        },
        "nextStop": {  
            "id":  "7", 
            "nextScheduledService": "datetime",
            "title", "Jacks Truck Stop",
            "address": "1505 Jack Road, Nashville, TN 37216",
            "type": "delivery"
        },
        "fuelLevel": 2600.0
   }
}
```
12. Truck drives to the next stop and delivers the fuel
13. Truck reports new fuel level to server
```
POST /fillup
{
    "gallons": 1500.0,
    "stopId" : "7",
    "truckId": "1"
}
```
response:
```
{
	"truck": {
        "currentStop": {  
            "id":  "7", 
            "nextScheduledService": "datetime",
            "title", "Jacks Truck Stop",
            "address": "1505 Jack Road, Nashville, TN 37216",
            "type": "delivery"
        },
        "nextStop": {  
            "id":  "8", 
            "nextScheduledService": "datetime",
            "title", "Headquarters",
            "address": "101 Fill Up Road, Nashville, TN 37204",
            "type": "fill up"
        },
        "fuelLevel": 1100.0
   }
}
```
14. Truck returns to refill
15. Truck reports new fuel level to server
```
POST /fillup
{
    "gallons": 10000.0,
    "stopId" : "8",
    "truckId": "1"
}
```
response:
```
{
	"truck": {
        "currentStop": { 
            "id":  "8", 
            "nextScheduledService": "datetime",
            "title", "Headquarters",
            "address": "101 Fill Up Road, Nashville, TN 37204",
            "type": "fill up"
            
        },
        "nextStop": {  
			"id":  "9", 
            "nextScheduledService": "datetime",
            "title", "Chucks Truck Stop",
            "address": "1505 Sam Road, Nashville, TN 37216",
            "type": "delivery"
        },
        "fuelLevel": 10000.0
   }
}
```
16. Truck drives to the next stop and delivers the fuel

