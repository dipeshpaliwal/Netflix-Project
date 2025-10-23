# 🎬 Netflix SQL Analysis

A comprehensive SQL-based analysis of **Netflix movies and TV shows**.  
This project explores content distribution, ratings, genres, and regional trends using SQL queries.

---

## 📊 Project Overview

This project involves analyzing Netflix's content dataset to extract valuable insights and answer key business questions such as:

- Distribution of **Movies vs TV Shows**
- Most common **ratings**
- Content trends by **year**, **country**, and **genre**
- Content categorization based on keywords like *violence* and *kill*

---

## 🎯 Objectives

1. Analyze the distribution of content types (**Movies vs TV Shows**)
2. Identify the most common **ratings**
3. Explore content by **year**, **country**, and **duration**
4. Retrieve insights on **directors**, **actors**, and **genres**
5. Categorize content based on specific **keywords**

---

## 🧱 Database Schema

```sql
DROP TABLE IF EXISTS netflix;

CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);

---

## 💡 Business Problems & SQL Solutions

---

 1️⃣ Count the Number of Movies vs TV Shows

 Objective:Determine the distribution of content types (Movies vs TV Shows) on Netflix.

SELECT 
    type,
    COUNT(*) AS total_count
FROM netflix
GROUP BY type;

---

2️⃣ Find the Most Common Rating for Movies and TV Shows

Objective: Identify the most frequently occurring rating for each type of content.

WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;

---

3️⃣ List All Movies Released in a Specific Year (e.g., 2020)

Objective: Retrieve all movies released in a specific year.

SELECT * 
FROM netflix
WHERE release_year = 2020;

4️⃣ Find the Top 5 Countries with the Most Content on Netflix

Objective: Identify the top 5 countries with the highest number of content items.

SELECT * 
FROM (
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;

5️⃣ Identify the Longest Movie

Objective: Find the movie with the longest duration.

SELECT 
    *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC
LIMIT 1;

6️⃣ Find Content Added in the Last 5 Years

Objective: Retrieve content added to Netflix in the last 5 years.

SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';

7️⃣ Find All Movies/TV Shows by Director 'Rajiv Chilaka'

Objective: List all content directed by 'Rajiv Chilaka'.

SELECT *
FROM (
    SELECT 
        *,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';

8️⃣ List All TV Shows with More Than 5 Seasons

Objective: Identify TV shows with more than 5 seasons.

SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;

9️⃣ Count the Number of Content Items in Each Genre

Objective: Count the number of content items in each genre.

SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;

🔟 Find Each Year and the Average Number of Content Releases in India on Netflix (Top 5 Years)

Objective: Calculate and rank years by the average number of content releases from India.

SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;

1️⃣1️⃣ List All Movies that are Documentaries

Objective: Retrieve all movies classified as documentaries.

SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';

1️⃣2️⃣ Find All Content Without a Director

Objective: List content that does not have a director.

SELECT * 
FROM netflix
WHERE director IS NULL;

1️⃣3️⃣ Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years

Objective: Count the number of movies featuring 'Salman Khan' in the last 10 years.

SELECT * 
FROM netflix
WHERE casts LIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;

1️⃣4️⃣ Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

Objective: Identify the top 10 actors with the most appearances in Indian-produced movies.

SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*) AS total_appearances
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY total_appearances DESC
LIMIT 10;

1️⃣5️⃣ Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords

Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise.

SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;


 🧩 Findings and Conclusion

  Content Distribution:
  The dataset contains a diverse range of movies and TV shows with varying ratings and genres.

  Common Ratings: 
  Insights into the most common ratings provide an understanding of the content's target audience.

  Geographical Insights:
  The top countries and the average content releases by India highlight regional content distribution.

  Content Categorization:
  Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

📊 Overall Conclusion: 
This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

