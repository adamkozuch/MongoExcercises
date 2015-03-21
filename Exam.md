##First part-MapReduce on anagrams file
After importing  word_list.txt file to database map function looks like this:


```javascript
function() {
var word=  this.word.split("");
word =  word.sort();
word=  word.join();
  emit(word,this.word);
}
```
Function reduce looks like this :
```javascript
function(key, values) {
  return values.toString();
};
```

![](https://cloud.githubusercontent.com/assets/5136443/5631485/647e413e-95c7-11e4-8f03-e635f9fb534a.png)

As we can see mapReduce reduce 914 words what mean that there is 914 words which are anagrams different words.
In order to show all anagrams we can use:
```javascript
db.anag.out.find({$where:"this.value.length>6"})
```


##Secound part Map reduce on data from Amazon

To do that exercise I used data from  Six Categories of Amazon Product Reviews downloaded http://times.cs.uiuc.edu/~wang296/Data/ .
Data contain information about products in Amazon shop.Most of products is reviewed and on this reviews I will do mapRduce. Every product can be reviewed couple of times.  
Each document has fallowing structure
![](https://cloud.githubusercontent.com/assets/5136443/5702245/a6c2df2a-9a57-11e4-88d9-6ffe258d278c.png)
 As we can se Reviews is an array of JSON objects.In such a structure I will use double forEach loop in order to do mapReduce.

Function Map looks like this:
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
Function Reduce looks like this :

```javascript
r = function(key, values) {
  return Array.sum(values);
};
```

We use fallowing command to get a result:
```javascript
result = db.amazonReview.mapReduce(m,r,{out:revWord});
```
![](https://cloud.githubusercontent.com/assets/5136443/5701796/cec5fc66-9a50-11e4-9297-dd296279af33.png)
As we can see mongoe emited 1146677 words and result become reduced to 8469 words.
In order to find twenty most popular words I used:
```javascript
db.revWord.find().limit(20).sort({value:-1})
```
Result::


![](https://cloud.githubusercontent.com/assets/5136443/5701798/d2000d2c-9a50-11e4-9679-29456f68bf93.png)

As we can see most popular are typical english words like his, he, was.
We also find words related with shopping hence,purchased, bought.
often occur word love which is probably expression of delight by particular product.
