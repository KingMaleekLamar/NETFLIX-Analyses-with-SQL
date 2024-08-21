# NETFLIX-Analyses-with-SQL
================================

A repository containing SQL queries and analyses for exploring the Netflix dataset.

**About the Dataset**
-------------------

The Netflix dataset is a publicly available dataset that contains information about Netflix movies and TV shows. The dataset includes attributes such as Artist, director, release year, rating, and more.

**Analyses**
------------

This repository contains a series of SQL queries and analyses that explore the Netflix dataset. The analyses are designed to provide insights into various aspects of the data, such as:

* **Top-rated movies and TV shows**: Which movies and TV shows have the highest ratings on Netflix?
* **Ratings distribution**: What is the distribution of movies and TV shows by ratings?
* **Director popularity**: Which directors have the most popular movies and TV shows on Netflix?
* **Release year trends**: How has the popularity of movies and TV shows changed over time?

**Getting Started**
-------------------

To get started with the analyses, follow these steps:

1. Clone the repository to your local machine.
2. Install the necessary dependencies (e.g., MySQL or PostgreSQL).
3. Run the SQL queries in a database management system such as MySQL or PostgreSQL.
4. Explore the results and insights gained from the analyses.

**SQL Queries**
----------------

The repository contains a range of SQL queries that can be used to analyze the Netflix dataset. Some examples include:

* `top_rated_movies.sql`: A query that retrieves the top-rated movies on Netflix.
* `genre_distribution.sql`: A query that displays the distribution of movies and TV shows by genre.
* `director_popularity.sql`: A query that retrieves the most popular directors on Netflix.








BELOW ARE SQL QUERIES

1 - CREATING_TABLES
CREATE TABLE netflix_movies (duration_minutes INT,	duration_seasons INT,	type VARCHAR,	title VARCHAR,	date_added DATE,	release_year DATE,	rating VARCHAR,	description VARCHAR,	show_id INT)
CREATE TABLE Netflix_Movies_Dr (director VARCHAR,	show_id INT)
CREATE TABLE Netflix_Movies_Countries (country VARCHAR,	show_id INT)
CREATE TABLE Netflix_Movies_Cast (Movie_Cast VARCHAR,	show_id INT)
CREATE TABLE Netflix_Movies_Category (categories VARCHAR,	show_id INT)

2. WRITE TO CHECK IF THERE IS (NULL VALUES/FIGURES):
SELECT 
duration_minutes = NULL AS duration_minutes,
duration_seasons = NULL AS duration_seasons,
TYPE  = NULL AS type,
title = NULL AS title,
date_added = NULL as date_added,
release_year = NULL AS release_year,
rating = NULL AS rating ,
description = NULL AS description,
show_id  = NULL AS show_id
FROM public.netflix_movies

3. Count the total number of movies and TV shows in the dataset:
SELECT DISTINCT (type),
COUNT (*) AS Total_Movies
FROM netflix_movies
GROUP by type
order by Total_Movies desc

4. Find the average duration of movies and TV shows.
SELECT "type",
ROUND ( AVG (duration_mins), 2) AS Average_mins,
ROUND (SUM (duration_mins), 2) AS Total_mins,
ROUND (SUM (duration_mins)/ 60, 2) AS Total_Hours
FROM public.netflix_movies
GROUP BY "type"
ORDER BY Total_mins DESC


5. List the top 10 longest movies based on duration
SELECT "title", "duration_mins",
ROUND (SUM (duration_mins)/60, 2) AS Total_hours,
release_year
FROM netflix_movies
WHERE "type" = 'Movie'
GROUP BY title, duration_mins,release_year
ORDER by duration_mins DESC
LIMIT 10

6. Find the number of movies released in each year.
SELECT  release_year,
COUNT (type) AS Total_Movies
FROM netflix_movies
WHERE "type" = 'Movie'
GROUP BY release_year
ORDER BY Total_Movies DESC

7. Determine the most common rating for TV shows
SELECT DISTINCT rating,
COUNT (*) AS Total_rating
FROM netflix_movies
WHERE "type" = 'TV Show'
GROUP BY "rating"
ORDER BY Total_rating DESC

8. Find the average duration of movies released in the last 5 years
SELECT DISTINCT release_year,
ROUND (AVG (duration_mins), 2 ) AS Avg_mins,
ROUND (AVG (duration_mins)/60, 2 ) AS Avg_hours
FROM netflix_movies
WHERE "type" = 'Movie'
AND release_year >= 2016 
GROUP BY release_year
ORDER BY Avg_mins DESC

9. List the titles of movies with a duration greater than 180 minutes and a rating of 'R'
SELECT title, duration_mins, rating
from netflix_movies
WHERE "type" = 'Movie'
AND duration_mins >= 180
AND rating ='R'

10. Find the number of TV shows added to Netflix in each quarter
SELECT DISTINCT "type", COUNT (*) AS Number_of_movies,
EXTRACT ( quarter from date_added) AS quarter,
EXTRACT ( year from date_added) AS year
from netflix_movies
GROUP BY "type",quarter,year
ORDER BY Number_of_movies DESC


11. Calculate the average rating for movies released before 2000.
SELECT rating, ROUND (AVG (CASE rating
WHEN 'TV-Y' THEN 0 -- Assign a numerical value for 'TV-Y' rating 
WHEN 'TV-G' THEN 1 -- Assign a numerical value for 'TV-G' rating
WHEN 'TV-Y7-FV' THEN 2 -- Assign a numerical value for 'TV-Y7-FV' rating
WHEN 'TV-14' THEN 3 -- Assign a numerical value for 'TV-14' rating
WHEN 'TV-MA' THEN 4 -- Assign a numerical value for 'TV-MA' rating
WHEN 'TV-Y7' THEN 1.5 -- Assign a numerical value between 'TV-Y' and 'TV-G' rating
WHEN 'G' THEN 2 -- Assign a numerical value for 'G' rating
WHEN 'NC-17' THEN 5 -- Assign a numerical value for 'NC-17' rating
WHEN 'Not Provided' THEN 0 -- Assign a default value for missing ratings
WHEN 'PG' THEN 3 -- Assign a numerical value for 'PG' rating
WHEN 'TV-PG' THEN 2.5 -- Assign a numerical value between 'G' and 'TV-Y7' rating
WHEN 'PG-13' THEN 4 -- Assign a numerical value for 'PG-13' rating
WHEN 'R' THEN 5 -- Assign a numerical value for 'R' rating
WHEN 'UR' THEN 0 -- Assign a numerical value for 'UR' rating 
WHEN 'NR' THEN 0 -- Assign a numerical value for 'NR' rating
END ),2) AS Avg_Rating
FROM netflix_movies
WHERE "type" = 'Movie'
AND release_year <2000
GROUP BY rating
ORDER BY Avg_Rating DESC

12.  Determine the top 5 most frequently occurring words in the description column.
SELECT word, count(*) AS word_count
FROM (  SELECT lower(regexp_split_to_table(description, E'\\W+')) AS word
  FROM netflix_movies) AS words_tbl
GROUP BY word
ORDER BY word_count DESC
LIMIT 10;

13. Find TOP_10 Most Popular Categories
SELECT DISTINCT (categories), COUNT (*) AS Total_Movies
FROM netflix_movies_category
GROUP BY categories
ORDER BY  Total_Movies DESC
LIMIT 10

14. Total Movies Uploaded by Months over_time
SELECT
to_char(date_added, 'Month') AS Months_Names,
COUNT (show_id) AS Total_Views
FROM netflix_movies
GROUP BY Months_Names
ORDER BY Total_Views DESC

15. Days with the most frequent movies uploaded
SELECT
to_char(date_added, 'Day') AS Days,
COUNT (show_id) AS Total_Views
FROM netflix_movies
GROUP BY Days
ORDER BY Total_Views DESC

16.  Country with the highest Netflix_Movies
SELECT DISTINCT (country), COUNT (show_id)   AS Total_movies
FROM public.netflix_movies_countries
GROUP BY country
ORDER BY Total_movies DESC

JOINS !!!

17. Movies wit the highest number_of_cast and their realse  YEAR
SELECT
DISTINCT COUNT (netflix_movies_cast.movie_cast) AS Total_cast, (netflix_movies.title), (netflix_movies.release_year)
FROM netflix_movies
	RIGHT JOIN netflix_movies_cast ON netflix_movies.show_id = netflix_movies_cast.show_id
GROUP BY title, release_year
ORDER BY Total_cast DESC

OR!

SELECT 
DISTINCT COUNT (mc.movie_cast) AS Total_cast,
(nm.title), (nm.release_year)
FROM netflix_movies AS nm
	RIGHT JOIN netflix_movies_cast AS mc
	ON mc.show_id = nm.show_id
GROUP BY title, release_year
ORDER BY Total_cast DESC



19. Country with the highest Avg_Rating
SELECT mc.country, ROUND (AVG (CASE nm.rating
WHEN 'TV-Y' THEN 0 -- Assign a numerical value for 'TV-Y' rating 
WHEN 'TV-G' THEN 1 -- Assign a numerical value for 'TV-G' rating
WHEN 'TV-Y7-FV' THEN 2 -- Assign a numerical value for 'TV-Y7-FV' rating
WHEN 'TV-14' THEN 3 -- Assign a numerical value for 'TV-14' rating
WHEN 'TV-MA' THEN 4 -- Assign a numerical value for 'TV-MA' rating
WHEN 'TV-Y7' THEN 1.5 -- Assign a numerical value between 'TV-Y' and 'TV-G' rating
WHEN 'G' THEN 2 -- Assign a numerical value for 'G' rating
WHEN 'NC-17' THEN 5 -- Assign a numerical value for 'NC-17' rating
WHEN 'Not Provided' THEN 0 -- Assign a default value for missing ratings
WHEN 'PG' THEN 3 -- Assign a numerical value for 'PG' rating
WHEN 'TV-PG' THEN 2.5 -- Assign a numerical value between 'G' and 'TV-Y7' rating
WHEN 'PG-13' THEN 4 -- Assign a numerical value for 'PG-13' rating
WHEN 'R' THEN 5 -- Assign a numerical value for 'R' rating
WHEN 'UR' THEN 0 -- Assign a numerical value for 'UR' rating 
WHEN 'NR' THEN 0 -- Assign a numerical value for 'NR' rating
END ),2) AS Avg_Rating
FROM netflix_movies AS nm
 RIGHT JOIN netflix_movies_countries AS mc ON mc.show_id = nm.show_id
GROUP BY mc.country
ORDER BY Avg_Rating DESC

20. COUNTRIES WITH THE HIGHEST NUMBER OF ARTIST
SELECT
	DISTINCT nmc.country,
		COUNT ( mc.movie_cast)   AS Total_Artist
FROM netflix_movies_countries AS nmc
	INNER JOIN netflix_movies_cast AS mc ON nmc.show_id = mc.show_id
GROUP BY nmc.country
ORDER BY Total_Artist DESC 
LIMIT 10

21. COUNTRIES WITH THE HIGHEST NUMBER OF directors
SELECT
	DISTINCT nmc.country,
		COUNT ( md.director)   AS Total_Directors
FROM netflix_movies_countries AS nmc
	INNER JOIN netflix_movies_dr AS md ON  md.show_id = nmc.show_id
GROUP BY nmc.country
ORDER BY Total_Directors DESC 
LIMIT 10

22. Find and display all Countries in the dataset
SELECT DISTINCT 
REPLACE (country,',','') AS countries
FROM netflix_movies_countries
ORDER BY countries ASC

**Future Developments**
----------------------
This repository is intended to be a starting point for further analysis and exploration of the Netflix dataset. Future developments may include:

* Adding new analyses and SQL queries to explore different aspects of the data.
* Integrating machine learning algorithms to predict user behavior and preferences.
* Creating visualizations to help illustrate the insights gained from the analyses.

**Acknowledgments**
----------------

This repository is based on publicly available data from Netflix. We would like to acknowledge their efforts in providing access to this data and inspire further analysis and exploration.
