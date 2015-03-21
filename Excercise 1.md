#1a. Import csv do mongo i postgress
The task is to import collection
##*import to mongo database
```javascript
time mongoimport --db test --collection trainCollection  --type csv --headerline --file /home/adam/Downloads/Train_.csv
```

**Import is finished with following results:**
![](https://cloud.githubusercontent.com/assets/5136443/5113019/7df80296-702c-11e4-967f-9fa70151854d.png)

There is 6034195 objects and import lasts for 23 minutes 47 secounds.<p>
Size of collection:<p>
![](https://cloud.githubusercontent.com/assets/5136443/5113016/7df1043c-702c-11e4-9e0c-9a694d0c97ed.png)

**Procesor usage during import:**
*During importing data to Mongo we can observe interesting phenomenon hence, regular increasing and decreasing usage of processor. In order to check what cause that I made measurement whit the use of iostat. i used fallowin command.
```javascript
iostat -x 1 180 > miara
```
 Miara w tym przypadku to wyjściowy plik tekstowy który po zaimportowaniu do arkusza kalkulacyjnego oraz uzycia drobnych filtrow wyglada nastepujaco(plik jest wrzucony do repo Zadanie1):
![](https://cloud.githubusercontent.com/assets/5136443/5652831/e47315ca-96b4-11e4-8580-1cc5f2a94a8f.PNG).
 Highlited values are used in charts hence, procesor usage by user and opertaing sytem also number of packages readed and writted by secoud. Mesurement was conducted in one secund interval for 180 secounds and mongoimport was running after 60 secound .*
*Tak wygląda wycinek zużycia procesora*
![](https://cloud.githubusercontent.com/assets/5136443/5564171/805759de-8ead-11e4-8296-07d0aa4b5566.PNG)
*Blue line on chart representing percentage of processor use by user programs, gray usage of processor by operating system. In approximately 60m secound of measurement I run import as we can see on chart.*
*Increasing and decreasing of usage of processor is very characteristic. In my opinion that phenomenon is caused
by waiting of processor until portion of data is writtent to hard drive.I will try to prove that below.*
* I compared ttwo ling graphs usage of processor by user and amount of data recorded to hard drive during mongoimport. We can see it below: *
![](https://cloud.githubusercontent.com/assets/5136443/5564168/804eadac-8ead-11e4-8078-958a918c3e0b.PNG)
*Na powyższym wykresie niebieska linia reprezentuje procent uzycia procesora natomiast brazowa linia pokazuje ilosc zadan zapisu danych na dysku. W tym wypadku na pierwszy rzut oka mozna zaobserwowac to ze w momecie gdy ilosc zadan zapisu sie zwieksza uzycie procesora maleje oraz to ze w momecie gdy ilosc zadan maleje zuzycie procesora rosnie. Nastepny wykrez zobrazuje jak to wyglada od strony czytania danych.*
![](https://cloud.githubusercontent.com/assets/5136443/5564169/80518108-8ead-11e4-91d1-4a0f6c1b0afb.PNG)
*Grey line representing amount of data readed from hard drive and blue line usage of processor by user programs. As we can see line graphs are correlated. When reading from disk rising, CPU usage also rising.*
*Based on above results i can say that hesitation of CPU usage during mongoimport is caused by performance of hard drive. Probably result would be different with Solid State Drive.*
##mongodump i mongorestore

Zaimportowałem plik z recenzjami produktów amazon do bazy mongo. Zamierzam wyeksportować bazę mongo w formacie BSON a następnie zmierzyć
użycie procesora i pamięci podczas importu tego pliku za pomocą mongorestore.
###1.Import
```javascript
time mongoimport -db test -collection amazon -type json -headerline  -file /home/adam/AmazonReviews/endFile.json --jsonArray
```
Czas imporu pliku to 6 minut 27 sekund
###Mongodump
Mongodump służy do eksportu bazy mongo w formacie BSON. Użyta została komenda mongodump
```javascript
adam@adam-HP-Pavilion-g6-Notebook-PC:~/Downloads/dump$ mongodump  --db test --collection amazon
connected to: 127.0.0.1
2015-01-22T17:58:46.214+0100 DATABASE: test	 to 	dump/test
2015-01-22T17:58:46.230+0100 	test.amazon to dump/test/amazon.bson
2015-01-22T17:58:49.020+0100 		Collection File Writing Progress: 4200/23294	18%	(documents)
2015-01-22T17:58:52.091+0100 		Collection File Writing Progress: 5300/23294	22%	(documents)
2015-01-22T17:58:55.004+0100 		Collection File Writing Progress: 7900/23294	33%	(documents)
2015-01-22T17:58:58.050+0100 		Collection File Writing Progress: 10200/23294	43%	(documents)
2015-01-22T17:59:01.312+0100 		Collection File Writing Progress: 11700/23294	50%	(documents)
2015-01-22T17:59:04.672+0100 		Collection File Writing Progress: 12400/23294	53%	(documents)
2015-01-22T17:59:07.271+0100 		Collection File Writing Progress: 13000/23294	55%	(documents)
2015-01-22T17:59:10.745+0100 		Collection File Writing Progress: 16500/23294	70%	(documents)
2015-01-22T17:59:13.075+0100 		Collection File Writing Progress: 17400/23294	74%	(documents)
2015-01-22T17:59:16.109+0100 		Collection File Writing Progress: 18500/23294	79%	(documents)
2015-01-22T17:59:19.277+0100 		Collection File Writing Progress: 20300/23294	87%	(documents)
2015-01-22T17:59:22.496+0100 		Collection File Writing Progress: 21500/23294	92%	(documents)
2015-01-22T17:59:24.892+0100 		 23294 documents
2015-01-22T17:59:24.906+0100 	Metadata for test.amazon to dump/test/amazon.metadata.json
```

###Mongorestore

W tym momęcie chciałbym zobaczyć jak zachowuje się dysk i procesor podczas ponownego importu pliku tym razem w formacie BSON.
 Używam komendy :
 ```javascript
 mongorestore --collection new --db test /home/adam/Downloads/dump/dump/test/amazon.bson
  ```

 Efektem tej akcji jest
 ```javascript
 23294 objects found
2015-01-22T19:25:51.680+0100 	Creating index: { key: { _id: 1 }, name: "_id_", ns: "test.newCollections" }

real	3m29.305s
user	0m1.379s
sys	0m1.289s
 ```

Jak widzimy czas mongorestore jos niemal o połowę krótszy w porównaniu do mongoimport.
Wykres zużycia dysku podczas operacji wygląda następująco:
![](https://cloud.githubusercontent.com/assets/5136443/5863128/8c42c336-a277-11e4-8bf0-07b8128e24dc.png)

Jak widzimy mamy doczynienia ze standardowymi sekwencjami odczytu i zapisu danych. Na uwagę zasługuje wycinek wykresu od 130 do 170 sekundy
ponieważ w tym czasie bardzo duża ilość danych jest zapisywana natomiast brak jest operacji czytania danych.
Następny wykres pokazuje jak pracuje procesor podczas zapisu danych na dysk
![](https://cloud.githubusercontent.com/assets/5136443/5863522/6d36da42-a27a-11e4-8583-aa2358176c69.png)










##*import to postgress database
-we create table by using:
#####create table traintable1( Id text, Title text, Body text, Tags text);



-use COPY to import records

#####COPY trainTable1(Id,Title,Body, Tags) FROM '~Downloads/Train_.csv' WITH DELIMITER ',' CSV HEADER
 import lasts for 32 minutes.


#1b Counting number of records
## mongo
 we use
#####db.train.count()
result is fallowing
![](https://cloud.githubusercontent.com/assets/5136443/5113346/358b55f0-702f-11e4-8908-8fde58ce2f26.png)


As we can see in database there is 6034195 records.
Usage of RAM and CPU is fallowing:
![](https://cloud.githubusercontent.com/assets/5136443/5113017/7df7102a-702c-11e4-9907-e6006824a261.png)




 ##postgres
 We use
#####Select count(*) from tabletrain;

![](https://cloud.githubusercontent.com/assets/5136443/5114443/07dfff66-7039-11e4-86ac-e1a2e07f7dda.png)







#Zadanie 1c

#*Same with postgress
Name of table with data is tabletrain. In order to change string to array of strings we use in postgress fallowing command:

#####ALTER TABLE tabletrain ALTER COLUMN TAGS TYPE TEXT[] using string_to_array(tags,' ') ;

We can count number of records by using

#####Select sum(array_length(tags,1)) from tabletrain;
![](https://cloud.githubusercontent.com/assets/5136443/5113011/7dde53fa-702c-11e4-83d0-1c066f4980e1.png)
In database there is 17409994 tags.

In order to count number of different tags we use
#####select  count(*) from (select distinct unnest(tags) from tabletrain) as foo. Wynik jest nastepujacy

![](https://cloud.githubusercontent.com/assets/5136443/5113015/7deae372-702c-11e4-9166-79ecdac2f85b.png)
In database there is 42048 different tags.

##Secound part
In secound part we use Java Driver to do same thing  


*Java Driver
Excercise is done by use following code:



 ```javascript
    public static void main(String[] args) {
        try {

            MongoClient mongoClient = new MongoClient("localhost");

           DB database = mongoClient.getDB("test");

        DBCollection collection = database.getCollection("trainCollection");

        BasicDBObject query = new BasicDBObject();

        BasicDBObject fields=new BasicDBObject();

        fields.put("Tags", 1);

          DBCursor cursor = collection.find(query,fields );

          int numberOfTags =0;

          Set<String> tagsWithoutDuplicates = new HashSet<String>();

          long start = System.currentTimeMillis();


        	while(cursor.hasNext()) {

        String tags =cursor.next().get("Tags").toString();
       String[] tableOftags= tags.split(" ");
       numberOfTags+=tableOftags.length;

       for(int in=0;in<tableOftags.length;in++)
       {
    	   tagsWithoutDuplicates.add(tableOftags[in]);
       }

       }

        	long elapsedTime = System.currentTimeMillis() - start;
        	System.out.println(numberOfTags);
        	System.out.println("Ilo?c ro?nych tagów "+tagsWithoutDuplicates.size());

        	System.out.println("Czas potrzebny na zrealizowanie zadania :"+elapsedTime );

            mongoClient.close();

        } catch (UnknownHostException ex) {
            ex.printStackTrace();
        }

    }
```

Results of executing code:
![](https://cloud.githubusercontent.com/assets/5136443/5113013/7de34cac-702c-11e4-8140-3e583040edc6.png)
Zadanie bylo realizowane przez 178 sekund.Natomiast obciazenie komputera bylo nastepujace
