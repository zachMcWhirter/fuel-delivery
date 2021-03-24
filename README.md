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
   "status": "in-service"
}
```
response:
```
{
   "currentStop": {  
        "id": "1", 
        "nextScheduledService": "datetime",
        "title", "Bobs Truck Stop",
        "address": "1505 Bob Road, Nashville, TN 37214"
     },
 "nextStop": {  
        "id":  "2", 
         "nextScheduledService": "datetime",
         "title", "Bills Truck Stop",
         "address": "1505 Bill Road, Nashville, TN 37214"
     },
   "fuelLevel": 10000.0
}
```
2. Truck drives to the next stop, and delivers the fuel
3. Truck reports new fuel level to server
```
POST /fllup
{
   "gallons": 1000.0,
    "stopId" : "1",
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
        "address": "1505 Bob Road, Nashville, TN 37207"
       },
      "nextStop": {  
     	"id":  "3", 
     	"nextScheduledService": "datetime",
     	"title", "Sues Truck Stop",
     	"address": "1505 Sue Road, Nashville, TN 37207"
     },
   "fuelLevel": 9000.0
   }
}
```
4. Truck drives to the next stop, and delivers the fuel
5. Truck reports new fuel level to server
6. Truck sends `GET` request to server asking for the new value of `CurrentFuelLevel`
7. *IF* `CurrentFuelLevel` > 17% of `Capacity`
	- Truck sends a `GET` request to server asking for the next stop again
8. The process described in steps 2-7 are repeated until `CurrentFuelLevel` < 17% of `Capacity`
	- At which point the Truck returns to refill and we begin the process over again at Step 1

