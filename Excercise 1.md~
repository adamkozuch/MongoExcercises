#1a. Import csv do mongo i postgress

##*import do bazy mongo
```javascript
time mongoimport --db test --collection trainCollection  --type csv --headerline --file /home/adam/Downloads/Train_.csv 
```
 
**Import jest zakonczony z nastepujacymi wynikami:**
![](https://cloud.githubusercontent.com/assets/5136443/5113019/7df80296-702c-11e4-967f-9fa70151854d.png)

Zostalo zaimportowanych 6034195 obiektow a import trwal 23 minuty 47 sekund.<p>
Rozmiar kolekcji jest nastepujacy:<p>
![](https://cloud.githubusercontent.com/assets/5136443/5113016/7df1043c-702c-11e4-9e0c-9a694d0c97ed.png)

**Użycie procesora podczas importu:**
*Podczas importu danych do Mongo można zaobserwować interesujące zjawisko a mianowicie w miarę jednostajne wzrosty i spadki użycia procesora. Aby sprawdzić czym może być spowodowane to zjawisko przeprowadziłem pomiary za pomocą programu iostat a konkretnie komenda 
```javascript
iostat -x 1 180 > miara
```
 Miara w tym przypadku to wyjściowy plik tekstowy który po zaimportowaniu do arkusza kalkulacyjnego oraz uzycia drobnych filtrow wyglada nastepujaco(plik jest wrzucony do repo Zadanie1):
![](https://cloud.githubusercontent.com/assets/5136443/5652831/e47315ca-96b4-11e4-8580-1cc5f2a94a8f.PNG).
 Zaznaczone wartosci zostaly uzyte w wykresach tz. uzycie procesora przez uztykownika i system oraz ilosc paczek zapisywanych i czytanych na sekunde. Pomiar był przeprowadzany co jedną sekundę przez 180 sekund przy czym mongoimport było uruchomione po 60 sekundach pomiaru.*
*Tak wygląda wycinek zużycia procesora*
![](https://cloud.githubusercontent.com/assets/5136443/5564171/805759de-8ead-11e4-8296-07d0aa4b5566.PNG)
*Niebieska linia na powyszym wykresie reprezentuje procentowe użycie procesora przez programy uzytkownika szara natomiast procentowe użycie procesora przez system operacyjny. W okolo 70 sekundzie pomiaru rozpoczął sie import mongo co obrazuje znaczny wzrost zuzycia procesora przez programy uzytkownika.* 
*Charakterystyczne sa znaczne wzrosty i spadki uzycia procesora podczas importu mongo. Moim zdaniem spowodowane sa one tym ze procesor czeka az dana porcja danych zostanie zapisana na dysku. Ponizej postaram sie udowodnic teze.*
*Aby zobrazowac zjawisko zestawilem na jednym wykresie zuzycie procesora przez programy uzytkownika oraz ilosc danych ktore sa zapisywane na dysku w czasie działania mongoimporty. Wykres wygląda następująco: *
![](https://cloud.githubusercontent.com/assets/5136443/5564168/804eadac-8ead-11e4-8078-958a918c3e0b.PNG)
*Na powyższym wykresie niebieska linia reprezentuje procent uzycia procesora natomiast brazowa linia pokazuje ilosc zadan zapisu danych na dysku. W tym wypadku na pierwszy rzut oka mozna zaobserwowac to ze w momecie gdy ilosc zadan zapisu sie zwieksza uzycie procesora maleje oraz to ze w momecie gdy ilosc zadan maleje zuzycie procesora rosnie. Nastepny wykrez zobrazuje jak to wyglada od strony czytania danych.*
![](https://cloud.githubusercontent.com/assets/5136443/5564169/80518108-8ead-11e4-91d1-4a0f6c1b0afb.PNG)
*Szara linia na powyzszym wykresie reprezentuje ilosc zadan odczytania danych z dysku natomiast niebieska standardowo uzycie procesora przez programy uzytkownika. Jak widzimy wykresy sa skorelowane. W momecie gdy rosnie ilosc zadan odczytu rosnie rowniez uzycie CPU.* 
*Na postawie powyższych danych myślę że można stwierdzić że wahanie użycia procesora podczas mongoimport jest spowodowane wydajnością dysku. Interesujący byłby podobny pomiar przeprowadzony z użyciem szybkiego dysku SSD gdyż z takim sprzętem być może nie zaobserwowalibyśmy*
*tak dużych wahań w użyciu procesora*
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


 
 
 



 

##*import do bazy postgres 
-tworzymy tabele za pomoca polecenia 
#####create table traintable1( Id text, Title text, Body text, Tags text);



-Używamy polecenia copy aby zaladowac rekordy

#####COPY trainTable1(Id,Title,Body, Tags) FROM '~Downloads/Train_.csv' WITH DELIMITER ',' CSV HEADER
Niestety nie udalo mi sie uzyc polecenia explain analyze na tym zapytaniu ale import trwal 32 minuty.


#1b Zliczenie liczby rekordów 
## mongo 
 uzywamy 
#####db.train.count() 
wynik jest nastepujacy
![](https://cloud.githubusercontent.com/assets/5136443/5113346/358b55f0-702f-11e4-8908-8fde58ce2f26.png)

 
Jak wida? w bazie znajduje 6034195 rekordow.
Zuzycie pamieci i procesora podczas tego procesu jest nastepujace 
![](https://cloud.githubusercontent.com/assets/5136443/5113017/7df7102a-702c-11e4-9907-e6006824a261.png)




 ##postgres
 uzywamy
#####Select count(*) from tabletrain; 

![](https://cloud.githubusercontent.com/assets/5136443/5114443/07dfff66-7039-11e4-86ac-e1a2e07f7dda.png)







#Zadanie 1c

#*wykonanie w postgres
Tabela zawierajaca wczytane dane nazywa sie tabletrain. Aby zmienic string na tablice stringów uzywamy w postgressien polecenia 

#####ALTER TABLE tabletrain ALTER COLUMN TAGS TYPE TEXT[] using string_to_array(tags,' ') ;

Nastepnie zliczamy ilosc rekordów poleceniem 

#####Select sum(array_length(tags,1)) from tabletrain;
![](https://cloud.githubusercontent.com/assets/5136443/5113011/7dde53fa-702c-11e4-83d0-1c066f4980e1.png)
W bzaie znajduje sie 17409994 tagow.

Zeby liiczyc ilosc roznych tagow uzywamy zapytania 
#####select  count(*) from (select distinct unnest(tags) from tabletrain) as foo. Wynik jest nastepujacy

![](https://cloud.githubusercontent.com/assets/5136443/5113015/7deae372-702c-11e4-9166-79ecdac2f85b.png)
W bazie znajduje sie 42048 roznych tagow.

##Druga czesc zadania 
Natomiast w drugiej czesci zadania zostal uzyty sterowniki Javy


*Sterownik Java 
Zadanie jest wykonane za pomoc? nastepujacego kodu:



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

Wyniki wykonania kodu sa nastepujace:
![](https://cloud.githubusercontent.com/assets/5136443/5113013/7de34cac-702c-11e4-8140-3e583040edc6.png)
Zadanie bylo realizowane przez 178 sekund.Natomiast obciazenie komputera bylo nastepujace 
