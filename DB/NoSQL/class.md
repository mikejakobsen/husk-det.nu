#Excercise from class

1. Write a MongoDB query to display all the documents in the collection series.

	db.series.find()

2. Write a MongoDB query to display the fields Title and Season for all the
 documents in the collection series.

	db.series.find({}, {Title: 1, Season: 1})

3. Write a MongoDB query to display the fields Title, Season and Episode, but
 exclude the field _id for all the documents in the collection series.

	db.series.find({}, {Title: 1, Season: 1, "Episodes.Episode": 1, _id : 0})

4. Write a MongoDB query to display all the seasons which is in the first series
 you chose (excluding those from the second series).

	db.series.find({Title: "Game of Thrones"})

5. Write a MongoDB query to display the first 3 seasons which is in the first
 series you chose.

	db.series.find({Title : "Game of Thrones"}).sort({Season : 1}).limit(3);

6. Write a MongoDB query to display the next 2 seasons after skipping first 3
 which are in the first series you chose.

	db.series.find({Title : "Game of Thrones"}).sort({Season : 1}).skip(3).limit(2);

7. Write a MongoDB query to find the Title, Episode and IMDB rating for those
 episodes which contains 'The' as first three letters for its name.

	db.series.find({"Episodes.Title" : { $regex : /^The/ }},{Title : 1, "Episodes.Title" : 1, "Episodes.imdbRating" : 1, "Episodes.$" : 1});


8. Write a MongoDB query to find the Title, Episode and IMDB rating for those
 episodes which contains 'ing' as last three letters for its name.

	db.series.find({"Episodes.Title" : { $regex : /ing$/ }},{Title : 1, "Episodes.Episode" : 1, "Episodes.imdbRating" : 1});


9. Write a MongoDB query to find the Title, Episode and IMDB rating for those
 episodes which contains 'car' as three letters somewhere in its name


	db.series.find({"Episodes.Title" : { $regex : /rains/i }},{Title : 1, "Episodes.Episode" : 1, "Episodes.imdbRating" : 1});

10. Write a MongoDB query to arrange the name of the series in ascending order
 along with all the columns.

	db.series.find({}).sort({Title : 1})

11. Write a MongoDB query to arrange the name of the series in ascending order
 with only the Title column showing.

db.series.find({}, {Title : 1}).sort({Title : 1})

12. Write a MongoDB query to arrange the name of the series in descending
 along with all the columns.


		db.series.find().sort({Title : -1})


	db.series.find({ 'Episodes.Title': 'Secrets' }, {'Episodes.$': 1});
