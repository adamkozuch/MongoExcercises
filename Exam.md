##Część pierwsza - Przeprowadzanie MapReduce na pliku anagramy
Po wczytaniu do bazy pliku word_list.txt do bazy 
funkcja map wygląda następująco 

```javascript
function() {
var word=  this.word.split("");
word =  word.sort();
word=  word.join();
  emit(word,this.word);
}
```
Natomiast funkcja reduce ma taką postać :
```javascript
function(key, values) {
  return values.toString();
};
```

![](https://cloud.githubusercontent.com/assets/5136443/5631485/647e413e-95c7-11e4-8f03-e635f9fb534a.png)

Jak widzimy mapReduce redukuje 914 slów co jest równoznaczne z otrzymaniem 914 słów które są anagramami innych słów.

aby zobaczyć wszystkie anagramy możemy użyc funkcji
```javascript
db.anag.out.find({$where:"this.value.length>6"})
```
Każda wartość która ma więcej niż 6 znaków zawiera więcej niż jedno słowo i jest anagramem.

##Część druga Map reduce przeprowadzone na danych z serwisu Amazon

Do zadania zostału użyte własne dane o nazwie Six Categories of Amazon Product Reviews pobraze z http://times.cs.uiuc.edu/~wang296/Data/ .
Dane zawierają informacje o produktach znajdujących się w sklepie Amazon. Większość produktów jest recenzowana i to właśnie na recenzjach 
tych produktów zamierzam przeprowadzić MapReduce. Każdy produkt może być zrecenzowany kilka razy.
Każdy dokument ma następującą strukturę
![](https://cloud.githubusercontent.com/assets/5136443/5702245/a6c2df2a-9a57-11e4-88d9-6ffe258d278c.png)
Jak widzimy Reviews jest tablicą obiektów json. Taka struktura dokumentu powoduje że w funkcji Map konieczne bedzie użycie podwojnej pętli forEach.


Funkcja Map wygląda następująco:
```javascript
m = function() {
    var reviews =  this.Reviews;
    if(reviews!=null)
    {
        reviews.forEach(function(review) {
                if (review.Content != null) {
                    var matchReview = review.Content.match(/[a-z]+/);
                    if (matchReview != null) {
                        matchReview.forEach(function (word) {
                                emit(word, 1);
                            }
                        );
                    }
                }
            }
            );
    }
}
```
Funkcja Reduce standardowo wygląda tak :

```javascript
r = function(key, values) {
  return Array.sum(values);
};
```

Natomiast wynik map reduce jest uzyskany za pomocą użycia następującej komendy:
```javascript
result = db.amazonReview.mapReduce(m,r,{out:revWord});
```
![](https://cloud.githubusercontent.com/assets/5136443/5701796/cec5fc66-9a50-11e4-9297-dd296279af33.png)
Jak widzimy mongo wyemitowało 1146677 słów a wynik został zredukowany do 8469 słów.
Aby znaleźć pierwsze dwadzieścia najczęściej występujących słów użyłem komendy:
```javascript
db.revWord.find().limit(20).sort({value:-1})
```
Wynik jest następujący:


![](https://cloud.githubusercontent.com/assets/5136443/5701798/d2000d2c-9a50-11e4-9679-29456f68bf93.png)

Jak widzimy najczęściej powtarzają się typowe angielskie słowa takie jak his, he, was.
Znajdujemy również słowa odnoszące się do czynności zakupu tz. purchased, bought.
Często występuje słowo love które prawdopodobnie często jest wyrazem zachwytu danym produktem. 
