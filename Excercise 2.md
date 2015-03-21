#Drugie zadanie
##1.
Do analizy zostalo uzyte reopozytorium <a href="http://archive.ics.uci.edu/ml/datasets/Individual+household+electric+power+consumption">Individual household electric power consumption</a>. Zaiwera ono ponad dwa miliony rekordów. Zawierra ono dane na temat zu?ycia energii w jednym gospodarstwie domowym mierzonym z odst?pem jednominutowym przez cztery lata.
Na dokument skladaja sie nastepujace atrybuty:<p>                                        
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
 Aby zaimportowac plik do mongo konieczna jest zamiana srednikow na przcinki. Pierwotny plik ma rozszezenie txt zmieniamy je na CSV <p>
Import charakteryzowal sie nastepujacymi parametrami 
![](https://cloud.githubusercontent.com/assets/5136443/5113012/7ddf8e96-702c-11e4-90c8-c0f8fcd86316.png)
Do bazy zostalo zaimportowane 2075259 obiektów a import trwa? 1 minuta 29 sekund.
##3
Przed tworzeniem agregacji typ atrybutu Date zostal zmienieony na ISODate.

Globalne zuzycie energii z podzialem na miesiac: <p>
db.power.aggregate(
  { $group:    {_id : { month: { $month: "$Date" }}, monthSum: {$sum: "$Global_active_power"}}} ,
     { $sort: { gapByDay: -1 }}
   );<p>
Wynik agregacji jest nastepujacy
![](https://cloud.githubusercontent.com/assets/5136443/5114596/32e4f8c8-703a-11e4-926d-8187617b9f63.png)<p>
Srednie napiecie pradu mierzone w amperach z podzialem na tydzien w roku:
db.power.aggregate(
 
    { $group:    {_id : { week: { $week: "$Date" }}, minDayWeek: {$avg: "$Global_intensity"}}}
    { $sort: { week: -1 }}
      
   );
   
   Wynik jest nast?puj?cy(w wykresach bedzie lepiej obja?nione):
   ![](https://cloud.githubusercontent.com/assets/5136443/5115130/e0bbe50c-703e-11e4-8153-c78a252a4445.png)
   
##4
Pierwsza agregacja Java:<p>
DBObject groupFields = new BasicDBObject( "_id", "month: { $month: "$Date" }");
groupFields.put("mmonthSum", new BasicDBObject( "$sum","$Global_active_power"));
DBObject group = new BasicDBObject("$group", groupFields);
DBObject sort = new BasicDBObject("$sort", new BasicDBObject("monthSum", -1)

List<DBObject> pipeline = Arrays.asList(group, sort);



Druga agregacja w Java:<p>
DBObject groupFields = new BasicDBObject( "_id", " week: { $week: "$Date" }");
groupFields.put("minDayWeek", new BasicDBObject( "$avg","$Global_intensity"));
DBObject group = new BasicDBObject("$group", groupFields);

List<DBObject> pipeline = Arrays.asList(group);


   
   
   
   
##5
   Wykres z pierwszej agregacji
   ![](https://cloud.githubusercontent.com/assets/5136443/5115350/722c5462-7040-11e4-96ec-4a8bf53a572b.png)<p>
   Wykres z drugiej agregacji - ?rednie nat??enie pr?du w amperach z podzia?em na tygodnie w roku
   ![](https://cloud.githubusercontent.com/assets/5136443/5115984/1d2704a2-7046-11e4-8acf-8bd86c02aa10.png)
