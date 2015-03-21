#Secound Excercise
##1.
I use Individual household electric power consumption to prepare analysis  <a href="http://archive.ics.uci.edu/ml/datasets/Individual+household+electric+power+consumption">Individual household electric power consumption</a>. It contain almost two millions of records about usage of electric energy measured in one household for four years in one minute interval.

Document contain fallowing attributes :<p>
1.date: Date in format dd/mm/yyyy<p>
2.time: time in format hh:mm:ss<p>
3.global_active_power: household global minute-averaged active power (in kilowatt)<p>
4.global_reactive_power: household global minute-averaged reactive power (in kilowatt)
5.voltage: minute-averaged voltage (in volt)<p>
6.global_intensity: household global minute-averaged current intensity (in ampere)<p>
7.sub_metering_1: energy sub-metering No. 1 (in watt-hour of active energy). It corresponds to the kitchen, containing mainly a dishwasher, an oven and a microwave (hot plates are not electric but gas powered).<p>
8.sub_metering_2: energy sub-metering No. 2 (in watt-hour of active energy). It corresponds to the laundry room, containing a washing-machine, a tumble-drier, a refrigerator and a light.<p>
9.sub_metering_3: energy sub-metering No. 3 (in watt-hour of active energy). It corresponds to an electric water-heater and an air-conditioner.
##2
in order to import file we have to change semicolons to colons. We also have to change extension form txt to csv.<p>
Import had fallowing parameters:
![](https://cloud.githubusercontent.com/assets/5136443/5113012/7ddf8e96-702c-11e4-90c8-c0f8fcd86316.png)
To database was imported 2075259 objects and import lasts for 1 minute 29 secounds.
##3
Before aggregation Date attribute was changed to ISODate.

Global energy usage by month : <p>
db.power.aggregate(
  { $group:    {_id : { month: { $month: "$Date" }}, monthSum: {$sum: "$Global_active_power"}}} ,
     { $sort: { gapByDay: -1 }}
   );<p>
Reslul:
![](https://cloud.githubusercontent.com/assets/5136443/5114596/32e4f8c8-703a-11e4-926d-8187617b9f63.png)<p>
Average intensity in amper by week in a year:
db.power.aggregate(

    { $group:    {_id : { week: { $week: "$Date" }}, minDayWeek: {$avg: "$Global_intensity"}}}
    { $sort: { week: -1 }}

   );
Result: ![](https://cloud.githubusercontent.com/assets/5136443/5115130/e0bbe50c-703e-11e4-8153-c78a252a4445.png)

##4
First aggregation in Java:<p>
DBObject groupFields = new BasicDBObject( "_id", "month: { $month: "$Date" }");
groupFields.put("mmonthSum", new BasicDBObject( "$sum","$Global_active_power"));
DBObject group = new BasicDBObject("$group", groupFields);
DBObject sort = new BasicDBObject("$sort", new BasicDBObject("monthSum", -1)

List<DBObject> pipeline = Arrays.asList(group, sort);



Secound agragation in Java:<p>
DBObject groupFields = new BasicDBObject( "_id", " week: { $week: "$Date" }");
groupFields.put("minDayWeek", new BasicDBObject( "$avg","$Global_intensity"));
DBObject group = new BasicDBObject("$group", groupFields);

List<DBObject> pipeline = Arrays.asList(group);






##5
   Chart from first aggregation
   ![](https://cloud.githubusercontent.com/assets/5136443/5115350/722c5462-7040-11e4-96ec-4a8bf53a572b.png)<p>
   Chart from second aggregation
   ![](https://cloud.githubusercontent.com/assets/5136443/5115984/1d2704a2-7046-11e4-8acf-8bd86c02aa10.png)
