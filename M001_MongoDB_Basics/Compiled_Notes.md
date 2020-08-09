## CHAPTER 1 NOTES

- **Data types are directly supported by JSON** : 
	- String
	- Array
	- Object
	- Decimal number
	

- DB > Collections > Documents

##CHAPTER 2 NOTES

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




## CHAPTER 3 NOTES

- **Comparison Operators** : 

	```javascript
	db.movieDetails.find({runtime: {$gt: 90}})
	//With projection
	db.movieDetails.find({runtime: {$gt: 90}}, {_id: 0, title: 1, runtime: 1})
	
	db.movieDetails.find({runtime: {$gt: 90, $lt: 120}}, {_id: 0, title: 1, runtime: 1})

	db.movieDetails.find({runtime: {$gte: 90, $lte: 120}}, {_id: 0, title: 1, runtime: 1})

	//MULTIFIELD FILTER
	db.movieDetails.find({runtime: {$gte: 180}, "tomato.meter": 100}, {_id: 0, title: 1, runtime: 1})

	//ALl docs where rated != UNRATED
	//ALSO RETURNS THE DOC WHIH DONOT HAVE "rated" field . TC 
	db.movieDetails.find({rated: {$ne: "UNRATED"}}, {_id: 0, title: 1, rated: 1})


	//EITHER OF G/PG 
	db.movieDetails.find({rated: {$in: ["G", "PG"]}}, {_id: 0, title: 1, rated: 1})


	//EITHER OF G/PG/PG-13
	db.movieDetails.find({rated: {$in: ["G", "PG", "PG-13"]}}, {_id: 0, title: 1, rated: 1}).pretty()

	db.movieDetails.find({rated: {$in: ["R", "PG-13"]}}, {_id: 0, title: 1, rated: 1}).pretty()
	```

- **QUIZ**
	- `db.movieDetails.find({writers: {$in: ["Ethan Coen", "Joel Coen"]}}).count()`

- **Element Operators**
	```javascript
	db.moviesDetails.find({"tomato": {$exists: true}})

	db.moviesDetails.find({"tomato": {$exists: false}})

	//RETURNS ALL THE RECORDS 
	// - with null
	// - no such field 
	db.movieDetails.find({"tomato.consensus": null})

	//RETURNS ONLY NULL
	db.movieDetails.find({"tomato.consensus": {$exists: true , $eq: null}})

	//Return all record
	db.movieDetails.find({})

	db.movies.find({viewerRating: {$type: "int"}}).pretty()

	db.movies.find({viewerRating: {$type: "double"}}).pretty()

	```

- **Quiz**:
	```javascript
	use 100YWeatherSmall ; 
	db.data.find({"atmosphericPressureChange": {"$exists" : false}}).count()
	```
- ** Logical Operators**	:
	```javascript
	db.movieDetails.find({$or: [{"tomato.meter": {$gt: 95}},                               
	                            {"metacritic": {$gt: 88}}]},
	                     {_id: 0, title: 1, "tomato.meter": 1, "metacritic": 1})

	db.movieDetails.find({$and: [{"tomato.meter": {$gt: 95}},                               
	                            {"metacritic": {$gt: 88}}]},
	                     {_id: 0, title: 1, "tomato.meter": 1, "metacritic": 1})

	//THE ABOVE QUERY IS EXACTLY SAME AS BELOW 

	db.movieDetails.find({"tomato.meter": {$gt: 95},                               
	                      "metacritic": {$gt: 88}},
	                     {_id: 0, title: 1, "tomato.meter": 1, "metacritic": 1})

	//We need to use AND only when we need to fire multple query for the same FIELD like below

	db.movieDetails.find({$and: [{"metacritic": {$ne: null}},
	                             {"metacritic": {$exists: true}}]},
	                          {_id: 0, title: 1, "metacritic": 1})


	//In such sitaution without AND , it will assume the last condition only 
	db.movieDetails.find({"metacritic": {$ne: null}},
	                    {"metacritic": {$exists: true}},
	                    {_id: 0, title: 1, "metacritic": 1})

	db.movieDetails.find({$and: [{"metacritic": null},
	                             {"metacritic": {$exists: true}}]},
	                     {_id: 0, title: 1, "metacritic": 1})

	```

- **Quiz** : 
	```javascript
	use ships

	db.shipwrecks.find({
		"$or" : [
			{"watlev" : "always dry"},
			{ "depth" : 0}
		]
	}).count()
	```

- ** Array Operators** : 
	- **$all**
		```javascript
		//Matches all records that contain aleast these mentioned fields
		// irrespective of their order 
		db.movieDetails.find({genres: {$all: ["Comedy", "Crime", "Drama"]}}, 
                     {_id: 0, title: 1, genres: 1}).pretty()
		```

		- **Quiz**
		`db.data.find({"sections" : { "$all" : ["AG1", "MD1", "OA1"]}}).count()`
		
	- **$size** :
		```javascript
		db.movieDetails.find({countries: {$size: 1}}).pretty()
		```

		- **Quiz**
			 `db.data.find({"sections" : {$size: 2}}).count()`
		
	- **$elemMatch** 

		```javascript
		//Asumming we have a field in doc like : 
		boxOffice: [ { "country": "USA", "revenue": 228.4 },
		             { "country": "Australia", "revenue": 19.6 },
		             { "country": "UK", "revenue": 33.9 },
		             { "country": "Germany", "revenue": 16.2 },
		             { "country": "France", "revenue": 19.8 } ]

		// this will return the above menntioned collectio, which is nont intended 
		db.movieDetails.find({"boxOffice.country": "Germany", "boxOffice.revenue": {$gt: 228}})


		//Use elemnatch : reutrn 0 records 
		db.movieDetails.find({boxOffice: {$elemMatch: {"country": "Germany", "revenue": {$gt: 228}}}})

		//Use elemnatch : reutrn 1 records  : as intended
		db.movieDetails.find({boxOffice: {$elemMatch: {"country": "Germany", "revenue": {$gt: 16}}}})
		```
	

		- **Quiz**
			 `db.surveys.find({"results" : {"$elemMatch": {"score":7 , "product" : "abc"}}}).count()`


	- **Regex**
		```javascript
		db.movieDetails.find({"awards.text": {$regex: /^Won.* /}}, {_id: 0, title: 1, "awards.text": 1}).pretty()
		```
		
	- **Challenge** :
		```javascript
		db.scores.find({ "results" : { "$elemMatch" : { $gte : 70 , $lt : 80}}}).count
		```
		
