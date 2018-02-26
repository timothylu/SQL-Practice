# Stanford Online - Langunita
## Databases: DB5 SQL
### Movie-Rating Query Exercises

1. Find the titles of all movies directed by Steven Spielberg. 
```
SELECT title FROM Movie
WHERE director='Steven Spielberg';
```
2. Find all years that have a movie that received a rating of 4 or 5, and sort them in increasing order. 
```
SELECT year
FROM Movie
WHERE mID IN (SELECT mID FROM Rating WHERE stars BETWEEN 4 AND 5)
ORDER BY year;
```
3. Find the titles of all movies that have no ratings. 
```
SELECT title
FROM Movie
WHERE mID NOT IN (SELECT mID FROM Rating);
``` 
4. Some reviewers didn't provide a date with their rating. Find the names of all reviewers who have ratings with a NULL value for the date. 
```
SELECT name
FROM Reviewer
WHERE rID IN (SELECT rID FROM Rating WHERE ratingDate IS NULL);
```
5. Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars. 
```
SELECT Reviewer.name, Movie.title, Rating.stars, Rating.ratingDate
FROM Rating
JOIN Reviewer
JOIN Movie
WHERE Reviewer.rID = Rating.rID AND Movie.mID = Rating.mID
ORDER BY Reviewer.name, Movie.title, Rating.stars;
```
6. For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie. 
```
SELECT Reviewer.name, Movie.title
FROM Reviewer
JOIN Movie
JOIN (
        SELECT Higher.rID, Higher.mID
        FROM Rating Higher, Rating Lower
        WHERE Higher.rID = Lower.rID AND Higher.mID = Lower.mID
            AND Higher.stars > Lower.stars AND Higher.ratingDate > Lower.ratingDate
    ) as R
Where Reviewer.rID = R.rID AND Movie.mID = R.mID;
```
7. For each movie that has at least one rating, find the highest number of stars that movie received. Return the movie title and number of stars. Sort by movie title. 
```
SELECT Movie.title, MAX(stars)
FROM Rating 
JOIN Movie on Rating.mID = Movie.mID
GROUP BY Movie.mID
ORDER BY Movie.title;
```
8. For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie title. 
```
SELECT Movie.title, MAX(Rating.stars) - MIN(Rating.stars)
FROM Movie
JOIN Rating ON Movie.mID = Rating.mID
GROUP BY Movie.mID
ORDER BY MAX(Rating.stars) - MIN(Rating.stars) DESC, Movie.title;
```
9. Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.) 
```
SELECT AVG(PRE.stars) - AVG(POST.stars)
FROM (
        SELECT AVG(Rating.stars) as stars
        FROM Rating
        JOIN Movie ON Rating.mID = Movie.mID
        WHERE Movie.year < 1980
        GROUP BY Rating.mID
    ) as PRE
JOIN (
        SELECT AVG(Rating.stars) as stars
        FROM Rating
        JOIN Movie ON Rating.mID = Movie.mID
        WHERE Movie.year > 1980
        GROUP BY Rating.mID
     ) as POST;
```
