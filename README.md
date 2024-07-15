# Netflix-Data-Analysis---SQL
#1. Write a query to list all titles with their show_id, title, and type.
-- Jhalak Bora
select show_id,title,type from shows_2021;


#2. Write a query to display all columns for titles that are Movies.
-- Jhalak Bora
select * from shows_2021 where type = 'Movie';




#3. Write a query to list TV shows that were released in the year 2021.
-- Jhalak Bora
select * from shows_2021
	where type = 'TV Show' AND release_year = 2021;
    

    
#4. Write a query to find all titles where the description contains the word family.
-- Jhalak Bora
select title from shows_2021
	where description like '%family%';
    

#5. Write a query to count the total number of titles in the dataset.
-- Jhalak Bora
select count(title) from shows_2021;


#6. Write a query to find the average duration of all movies (in minutes, wherever the season is mentioned, consider 400 minutes per season).
-- Jhalak Bora
ALTER TABLE shows_2021 ADD COLUMN duration_in_minutes INT;
UPDATE shows_2021
SET duration_in_minutes = 
    CASE
        WHEN duration LIKE '% min' THEN CAST(REPLACE(duration, ' min', '') AS UNSIGNED)
        WHEN duration LIKE '% Seasons' THEN CAST(REPLACE(duration, ' Seasons', '') AS UNSIGNED) * 400
        WHEN duration LIKE '% Season' THEN CAST(REPLACE(duration, ' Season', '') AS UNSIGNED) * 400
    END;
SELECT type,title,duration_in_minutes AS average_duration FROM shows_2021
WHERE type = 'movie';


#7. Write a query to list the top 5 latest titles based on the date_added, sorted in descending order
-- Jhalak Bora
select title,date_added from shows_2021
	order by date_added DESC LIMIT 5;
    

#8. Write a query to list all titles along with the number of other titles by the same director. Include columns for show_id, title, director, and number_of_titles_by_director.
-- Jhalak Bora
SELECT n1.show_id, n1.title, n1.director, n2.number_of_titles_by_director 
FROM  shows_2021 n1
JOIN (SELECT director, COUNT(*) AS number_of_titles_by_director FROM shows_2021 GROUP BY director) n2 
ON n1.director = n2.director
ORDER BY n2.number_of_titles_by_director;


#9. Write a query to find the total number of titles for each country. Display country and the count of titles.
-- Jhalak Bora
select country,
count(*) as 'count_of_titles'
from shows_2021
group by country
order by count_of_titles desc;


#10. Write a query using a CASE statement to categorize titles into three categories based on their rating: Family for ratings G, PG, PG-13, Kids for TV-Y, TV-Y7, TV-G, and Adult for all other ratings.
-- Jhalak Bora
select show_id,type,title,
CASE
	when rating = 'G' then 'Family'
    when rating = 'PG' then 'Family'
    when rating = 'PG-13' then 'Family'
    when rating = 'TV-Y' then 'Kids'
    when rating = 'TV-Y7' then 'Kids'
    when rating = 'TV-G' then 'Kids'
    else
		'Adult'
	END AS CATEGORY
from shows_2021;




#11. Write a query to add a new column title_length to the titles table that calculates the length of each title.
-- Jhalak Bora
alter table shows_2021 add column title_length INT;
SET SQL_SAFE_UPDATES = 0;
update shows_2021 set title_length = LENGTH(title)  WHERE show_id IS NOT NULL;
select title,title_length from shows_2021;


#12. Write a query using an advanced function to find the title with the longest duration in minutes
-- Jhalak Bora
SELECT title, duration_in_minutes FROM shows_2021 ORDER BY duration_in_minutes DESC LIMIT 1;


#13. Create a view named RecentTitles that includes titles added in the last 30 days.
-- Jhalak Bora
CREATE VIEW RecentTitles AS select * from shows_2021
	WHERE date_added >= CURDATE() - INTERVAL 30 DAY;
SELECT * from RecentTitles;


#14. Write a query using a window function to rank titles based on their release_year within each country.
-- Jhalak Bora
SELECT title, country, release_year, RANK() OVER (PARTITION BY country ORDER BY release_year) 
AS release_year_rank FROM shows_2021;


#15. Write a query to calculate the cumulative count of titles added each month sorted by date_added.
-- Jhalak Bora
SELECT
	date_format(date_added, '%Y/%m') AS month_added,
    Count(*) AS titles_added
FROM shows_2021
GROUP BY month_added
ORDER BY month_added;

#16. Write a stored procedure to update the rating of a title given its show_id and new rating.



#17. Write a query to find the country with the highest average rating for titles. Use subqueries and aggregate functions to achieve this.
-- Jhalak Bora
SELECT s.country, s.title AS title_with_highest_rating, s.rating AS highest_rating
FROM shows_2021 s
INNER JOIN (
			SELECT country, 
            MAX(rating) AS max_rating FROM shows_2021 
            GROUP BY country) AS max_ratings 
ON s.country = max_ratings.country AND s.rating = max_ratings.max_rating;


#18. Write a query to find pairs of titles from the same country where one title has a higher rating than the other. Display columns for show_id_1, title_1, rating_1, show_id_2, title_2, and rating_2.
-- Jhalak Bora
SELECT s1.show_id AS show_id_1, s1.title AS title_1, s1.rating AS rating_1, s2.show_id AS show_id_2, s2.title AS title_2, s2.rating AS rating_2 
FROM shows_2021 s1 
	INNER JOIN shows_2021 s2 ON s1.country = s2.country 
		WHERE 
			s1.show_id < s2.show_id AND s1.rating > s2.rating;
