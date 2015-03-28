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
*During importing data to Mongo we can observe interesting phenomenon hence, regular increasing and decreasing usage of processor. In order to check what cause that I made measurement whit the use of iostat. i used fallowing command.
```javascript
iostat -x 1 180 > miara
```
 Generated file was imported to spreadsheet:
![](https://cloud.githubusercontent.com/assets/5136443/5652831/e47315ca-96b4-11e4-8580-1cc5f2a94a8f.PNG).
 Highlighted values are used in charts hence, processor usage by user and operating system also number of packages read and witted by second. Measurement was conducted in one second interval for 180 seconds and mongoimport was running after 60 seconds .*
*This how looks like usage of processor*
![](https://cloud.githubusercontent.com/assets/5136443/5564171/805759de-8ead-11e4-8296-07d0aa4b5566.PNG)
*Blue line on chart representing percentage of processor use by user programs, gray usage of processor by operating system. In approximately 60m second of measurement I run import as we can see on chart.*
*Increasing and decreasing of usage of processor is very characteristic. In my opinion that phenomenon is caused
by waiting of processor until portion of data is written to hard drive.I will try to prove that below.*
* I compared two line graphs: usage of processor by user and amount of data recorded to hard drive during mongoimport. We can see it below: *
![](https://cloud.githubusercontent.com/assets/5136443/5564168/804eadac-8ead-11e4-8078-958a918c3e0b.PNG)
*Blue line represent percentage of using of processor and brown line representing number of tasks(writting data to hard drive). In this case we can observe that when number of tasks for writing data is increase , usage of processor decrease. That works both ways. Next graph line will show how it looks like from reading data perspective.*
![](https://cloud.githubusercontent.com/assets/5136443/5564169/80518108-8ead-11e4-91d1-4a0f6c1b0afb.PNG)
*Grey line representing amount of data read from hard drive and blue line usage of processor by user programs. As we can see line graphs are correlated. When reading from disk rising, CPU usage also rising.*
*Based on above results i can say that hesitation of CPU usage during mongoimport is caused by performance of hard drive. Probably result would be different with Solid State Drive.*


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







#Excercise 1c

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
Time of execution 178 seconds.
