## NOTES

- **CRUD** : 
	- create
	- read
	- update
	- delete

- **ATLAS SETUP / COMPASS /SHELL** 

- **Creating Documents: insertOne()**
	```javascript
	use video;
	show collections	
	db.videoScratch.insertOne({
  	    "title" : "Star Trek II: The Wrath of Khan",
  	    "year" : 1982,
  	    "type" : "movie"
        })
	```
	- auto creates collections if not present. 
	-  creates `_id` for every document. Unique and auto increasing. 
	- can use own value for `_id`	


- **Creating Documents: insertMany()**
	```javascript
	db.moviesScratch.insertMany(
    [ 
     {"_id" : "tt0084726","title" : "Star Trek II: The Wrath of Khan","year" : 1982,"type" : "movie"  },
     {"_id" : "tt0796366","title" : "Star Trek","year" : 2009,"type" : "movie"  },
     {"_id" : "tt0084726","title" : "Star Trek II: The Wrath of Khan","year" : 1982,"type" : "movie"  },
     {"_id" : "tt1408101","title" : "Star Trek Into Darkness","year" : 2013,"type" : "movie"  }
    ]);
	```

	```javascript
	db.moviesScratch.insertMany(
    [ 
     {"_id" : "tt0084726","title" : "Star Trek II: The Wrath of Khan","year" : 1982,"type" : "movie"  },
     {"_id" : "tt0796366","title" : "Star Trek","year" : 2009,"type" : "movie"  },
     {"_id" : "tt0084726","title" : "Star Trek II: The Wrath of Khan","year" : 1982,"type" : "movie"  },
     {"_id" : "tt1408101","title" : "Star Trek Into Darkness","year" : 2013,"type" : "movie"  },
    {
        "ordered": false 
    }
    ]);
	```
	
 #

- **READ /FIND Queries**
	- 
	```javascript
	db.movies.find(
	{
		"mpaaRating" : "PG-13",
		"year" : "2009"
	});
	```
	```javascript
	db.movieDetails.find({"rated" : "PG" , "awards.nominations":10}).count()
	```

	
	- **FIND in Array** :
		- Exact match : 
			```javascript
			db.movieDetails.find({"actors" : ["Will Smith","Kevin Kline","Kenneth Branagh","Salma Hayek"]}).count()
			```
		- Any Index match :
			```javascript
			db.movieDetails.find({"actors" : "Kevin Kline"}).count()
			```
		- Only single Index match :
			```javascript
			db.movieDetails.find({"actors.1" : "Kevin Kline"}).count()
			```
#
- **Cursor / Iterator** 
	- View next set of 20 items in shell by using
		- it
	- https://docs.mongodb.com/manual/tutorial/iterate-a-cursor/index.html
	- https://docs.mongodb.com/manual/reference/command/getMore/#dbcmd.getMore


#
- **Projections**
	- Return only selected ( 1 ) and `_id`
		- `db.movieDetails.find({"genre" : "Action"} , {"title" : 1}).count()`

	- Return only selected ( 1 ) 
		- `db.movieDetails.find({"genre" : "Action"} , {"title" : 1, "_id" : 0}).count()`

	- Return all except Excluded( 0 ) 
		- `db.movieDetails.find({"genre" : "Action"} , {"title" : 0, "_id" : 0}).count()`

#

- **Updating Documents: updateOne** 
	 ```javascript
	db.movieDetails.updateOne(
		{"title": "The Martian" },
		{"$set": {  "poster": "http://poster_1.com/1" } } 
	);
	 ```

- `$set` 
	- replaces a whole field

- `$unset`
	- removes the field 
-	Array update operators 
	-	`$push`
		creates a new field/key  if not exists 
	- `$push` with `$each` will insert new elements to array 


- **Updating Multiple Documents: updateMany**
	```javascript
	db.movieDetails.updateMany(
		{ "rated": null },
		{ "$unset": { "rated": "" }}
  );
	``` 
- **Upsert**
	- Insert if it doesn't exists 
	- If exists then UPDATE THAT DOC
	- to avoid duplicate entry 
	```javascript
	let detail = //{"some json data"}
	db.movieDetails.updateOne(
		{"imdb.id": "abc" },
		{ "$set": detail},
		{"upsert" : true }
	);
	```
	
- **replaceOne**
	- This is similar to update One, but it replaces a WHOLE DOC with a given json object that matches the filter.
	- If mulitple match it replcaes only the 1st one. 
	```db.movieDetails.replaceOne(filter, doc)```

#

- **Deletes** : 
	```
	db.movieDetails.deteleOne(filter)
	db.movieDetails.deteleMany(filter)
	```


**NB** : filter is a json object like `{"title" : "abc"}` 